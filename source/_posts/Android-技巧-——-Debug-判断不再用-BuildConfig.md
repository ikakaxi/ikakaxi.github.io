title: Android 技巧 —— Debug 判断不再用 BuildConfig
author: superman
date: 2018-02-07 22:30:48
categories:
- android
tags:
- debug
---

Android 开发中一般会通过 BuildConfig.DEBUG 判断是否是 Debug 模式，从而做一些在 Debug 模式才开启的特殊操作，比如打印日志。这样好处是不用在发布前去主动修改，因为这个值在 Debug 模式下为 true，Release 模式下为 false。
<!--more-->
 
#####1. 问题
如果应用只有一个 Module 没有问题，Debug 模式下 BuildConfig.DEBUG 会始终为 true。如果现在有两个 Module，分别为 App 和 Lib，且 App 依赖 Lib，在 Lib 内有工具类 LogUtils，代码如下：
```
package cn.trinea.android.lib.util;
 
import android.util.Log;
import cn.trinea.android.lib.util.BuildConfig;
 
public class LogUtils {
 
    public static void d(String log) {
        if (BuildConfig.DEBUG) {
            Log.d("trinea-debug", log);
        }
    }
    ……
}
```
当我们在 App Module 内调用 LogUtils 时我们会发现始终无法打印日志，因为上面的 BuildConfig.DEBUG 会始终为 false。为什么呢？
#####2. 原因
BuildConfig.java 是编译时自动生成的，并且每个 Module 都会生成一份，以该 Module 的 packageName 为 BuildConfig.java 的 packageName。所以如果你的应用有多个 Module 就会有多个 BuildConfig.java 生成，而上面的 Lib Module import 的是自己的 BuildConfig.java，编译时被依赖的 Module 默认会提供 Release 版给其他 Module 或工程使用，这就导致该 BuildConfig.DEBUG 会始终为 false。
 
#####3. 解决方案
根据上面分析的原因，目前我们有两个思路：
(1) 始终调用最终运行的 Module 的 BuildConfig，因为它没有被任何其他 Module 依赖，所以 BuildConfig.DEBUG 值会准确。
(2) 让被依赖的 Module 提供除 Release 版以外的其他版本。
 
3.1 解决方案一：使用其他的 BuildConfig.java
如果 Lib Module 中能够 import 到外层真正运行 App 的 BuildConfig 就 ok 了，如下：
```
package cn.trinea.android.lib.util;
 
/**
 * Utils for App
 * <ul>
 * <li>{@link #syncIsDebug(Context)} Should be called in module Application</li>
 * </ul>
 * Created by Trinea on 2017/3/9.
 */
public class AppUtils {
 
    private static Boolean isDebug = null;
 
    public static boolean isDebug() {
        return isDebug == null ? false : isDebug.booleanValue();
    }
 
    /**
     * Sync lib debug with app's debug value. Should be called in module Application
     *
     * @param context
     */
    public static void syncIsDebug(Context context) {
        if (isDebug == null) {
            try {
                String packageName = context.getPackageName();
                Class buildConfig = Class.forName(packageName + ".BuildConfig");
                Field DEBUG = buildConfig.getField("DEBUG");
                DEBUG.setAccessible(true);
                isDebug = DEBUG.getBoolean(null);
            } catch (Throwable t) {
                // Do nothing
            }
        }
    }
}
```
通过反射得到真正执行的 Module 的 BuildConfig，在自己的 Application 内调用：
```
AppUtils.syncIsDebug(getApplicationContext());
```
这样看起来达到目的了。
 
但仔细想想会发现这种解决方案还是有问题，因为 BuildConfig.java 的 packageName 是 Module 的 Package Name，即 AndroidManifest.xml 中的 package 属性，而 context.getPackageName() 得到的是应用的 applicationId，这个 applicationId 通过 build.gradle 是可以修改的。所以当 build.gradle 中的 applicationId 与 AndroidManifest.xml 中的 package 属性不一致时，上面的反射查找类路径便会出错。
 
PS：这种方案还有个变种就是通过 android.app.ActivityThread.currentPackageName 得到包名，从而省去传递 Context 初始化的步骤，但依然有 applicationId 被修改后类查找不到类似的问题。
 
#####3.2 解决方案二：被依赖的 Module 提供其他版本
让被依赖的 Module 提供除 Release 版以外的其他版本，这种方案需要将所有被依赖 library 中添加：
```
android {
    publishNonDefault true
}
```
表示该 Module 打包时会同时打包其他版本，包括 Debug 版。并且需要在 App Module 中将其依赖的 library 如下逐个添加：
```
dependencies {
    releaseCompile project(path: ':library', configuration: 'release')
    debugCompile project(path: ':library', configuration: 'debug')
}
```
表示依赖不同版本的依赖 Module。
然而这种方式所有 Module 配置都需要修改，侵入性太强。
 
#####3.3 最终解决方案：使用 ApplicationInfo.FLAG_DEBUGGABLE
既然 BuildConfig 的方式行不通，我们反编译 Debug 包和 Release 包对比看看有没有其他的区别，会发现他们 AndroidManifest.xml 中 application 节点的 android:debuggable 值是不同的。Debug 包值为 true，Release 包值为 false，这是编译自动修改的。所以我们考虑通过 ApplicationInfo 的这个属性去判断是否是 Debug 版本，如下：
```
package cn.trinea.android.lib.util;
 
/**
 * Utils for App
 * <ul>
 * <li>{@link #syncIsDebug(Context)} Should be called in module Application</li>
 * </ul>
 * Created by Trinea on 2017/3/9.
 */
public class AppUtils {
 
    private static Boolean isDebug = null;
 
    public static boolean isDebug() {
        return isDebug == null ? false : isDebug.booleanValue();
    }
 
    /**
     * Sync lib debug with app's debug value. Should be called in module Application
     *
     * @param context
     */
    public static void syncIsDebug(Context context) {
        if (isDebug == null) {
            isDebug = context.getApplicationInfo() != null &&
                    (context.getApplicationInfo().flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0;
        }
    }
}
```
在自己的 Application 内调用进行初始化，
```
AppUtils.syncIsDebug(getApplicationContext());
```
这样以后调用 AppUtils.isDebug() 即可判断是否是 Debug 版本，比如在上面的 LogUtils 中。同时适用于 Module 是 Lib 和 applicationId 被修改的情况，比 BuildConfig.DEBUG 靠谱的多。
 
这个方案有个注意事项就是自己 App Module 中不能主动设置 android:debuggable，否则无论 Debug 还是 Release 版会始终是设置的值。当然本身就没有自动设置的必要。

原文地址:http://www.trinea.cn/android/android-whether-debug-mode-why-buildconfig-debug-always-false/