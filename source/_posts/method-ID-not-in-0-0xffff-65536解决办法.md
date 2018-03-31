title: 'method ID not in [0, 0xffff]: 65536解决办法'
author: superman
date: 2018-02-07 22:31:59
categories:
- android
tags:
- 异常
---

###配置方法数超过 64K 的应用
<!--more-->
随着 Android 平台的持续成长，Android 应用的大小也在增加。当您的应用及其引用的库达到特定大小时，您会遇到构建错误，指明您的应用已达到 Android 应用构建架构的极限。早期版本的构建系统按如下方式报告这一错误：
```
Conversion to Dalvik format failed:
Unable to execute dex: method ID not in [0, 0xffff]: 65536
```
较新版本的 Android 构建系统虽然显示的错误不同，但指示的是同一问题：
```
trouble writing output:
Too many field references: 131000; max is 65536.
You may try using --multi-dex option.
```
这些错误状况都会显示下面这个数字：65,536。这个数字很重要，因为它代表的是单个 Dalvik Executable (DEX) 字节码文件内的代码可调用的引用总数。本页介绍如何通过启用被称为 Dalvik 可执行文件分包的应用配置来越过这一限制，使您的应用能够构建并读取 Dalvik 可执行文件分包 DEX 文件。

###关于 64K 引用限制
Android 应用 (APK) 文件包含 [Dalvik](https://source.android.com/devices/tech/dalvik/) Executable (DEX) 文件形式的可执行字节码文件，其中包含用来运行您的应用的已编译代码。Dalvik Executable 规范将可在单个 DEX 文件内可引用的方法总数限制在 65,536，其中包括 Android 框架方法、库方法以及您自己代码中的方法。在计算机科学领域内，术语[*千（简称 K）*](https://en.wikipedia.org/wiki/Kilo-)表示 1024（或 2^10）。由于 65,536 等于 64 X 1024，因此这一限制也称为“64K 引用限制”。

###Android 5.0 之前版本的 Dalvik 可执行文件分包支持
Android 5.0（API 级别 21）之前的平台版本使用 Dalvik 运行时来执行应用代码。默认情况下，Dalvik 限制应用的每个 APK 只能使用单个 classes.dex字节码文件。要想绕过这一限制，您可以使用 [Dalvik 可执行文件分包支持库](https://developer.android.com/tools/support-library/features.html#multidex)，它会成为您的应用主要 DEX 文件的一部分，然后管理对其他 DEX 文件及其所包含代码的访问。
>**注**：如果您的项目配置时所面向的 Dalvik 可执行文件分包使用的是 minSdkVersion 20或更低版本，并且您将其部署到运行 Android 4.4（API 级别 20）或更低版本的目标设备上，则 Android Studio 会停用 [Instant Run](https://developer.android.com/tools/building/building-studio.html#instant-run)。

###Android 5.0 及更高版本的 Dalvik 可执行文件分包支持
Android 5.0（API 级别 21）及更高版本使用名为 ART 的运行时，后者原生支持从 APK 文件加载多个 DEX 文件。ART 在应用安装时执行预编译，扫描 classesN.dex 文件，并将它们编译成单个 .oat 文件，供 Android 设备执行。因此，如果您的 minSdkVersion 为 21 或更高值，则不需要 Dalvik 可执行文件分包支持库。
如需了解有关 Android 5.0 运行时的详细信息，请参阅 [ART 和 Dalvik](https://source.android.com/devices/tech/dalvik/art.html)。
>**注**：如果将应用的 minSdkVersion 设置为 21 或更高值，使用 [Instant Run](https://developer.android.com/tools/building/building-studio.html#instant-run) 时，Android Studio 会自动将应用配置为进行 Dalvik 可执行文件分包。由于 Instant Run 仅适用于调试版本的应用，您仍需配置发布构建进行 Dalvik 可执行文件分包，以规避 64K 限制。

###规避 64K 限制
在将您的应用配置为支持使用 64K 或更多方法引用之前，您应该采取措施减少应用代码调用的引用总数，包括由您的应用代码或包含的库定义的方法。下列策略可帮助您避免达到 DEX 引用限制：
**检查您的应用的直接和传递依赖项** - 确保您在应用中使用任何庞大依赖库所带来的好处大于为应用添加大量代码所带来的弊端。一种常见的反面模式是，仅仅为了使用几个实用方法就在应用中加入非常庞大的库。减少您的应用代码依赖项往往能够帮助您规避 dex 引用限制。
**通过 ProGuard 移除未使用的代码** - 为您的版本构建[启用代码压缩](https://developer.android.com/studio/build/shrink-code.html)以运行 ProGuard。启用压缩可确保您交付的 APK 不含有未使用的代码。

使用这些技巧使您不必在应用中启用 Dalvik 可执行文件分包，同时还会减小 APK 的总体大小。
###配置您的应用进行 Dalvik 可执行文件分包
将您的应用项目设置为使用 Dalvik 可执行文件分包配置需要对您的应用项目进行以下修改，具体取决于应用支持的最低 Android 版本。

如果您的 minSdkVersion 设置为 21 或更高值，您只需在模块级 build.gradle 文件中将 multiDexEnabled 设置为 true，如此处所示：

```
android {
    defaultConfig {
        ...
        minSdkVersion 21 
        targetSdkVersion 25
        multiDexEnabled true
    }
    ...
}
```
但是，如果您的 minSdkVersion 设置为 20 或更低值，则您必须按如下方式使用 [Dalvik 可执行文件分包支持库](https://developer.android.com/tools/support-library/features.html#multidex)：
修改模块级 build.gradle 文件以启用 Dalvik 可执行文件分包，并将 Dalvik 可执行文件分包库添加为依赖项，如此处所示：
```
android {
    defaultConfig {
        ...
        minSdkVersion 15 
        targetSdkVersion 25
        multiDexEnabled true
    }
    ...
}

dependencies {
  compile 'com.android.support:multidex:1.0.1'
}
```
根据是否要替换 [Application
](https://developer.android.com/reference/android/app/Application.html) 类，执行以下操作之一：
如果您*没有*替换 [Application
](https://developer.android.com/reference/android/app/Application.html) 类，请编辑清单文件，按如下方式设置 <application>标记中的 android:name：
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">
    <application
            android:name="android.support.multidex.MultiDexApplication" >
        ...
    </application>
</manifest>
```
如果您替换了 [Application
](https://developer.android.com/reference/android/app/Application.html) 类，请按如下方式对其进行更改以扩展 [MultiDexApplication](https://developer.android.com/reference/android/support/multidex/MultiDexApplication.html)
（如果可能）：
```
public class MyApplication extends MultiDexApplication { ... }
```
或者，如果您替换了 [Application
](https://developer.android.com/reference/android/app/Application.html) 类，但无法更改基本类，则可以改为替换 [attachBaseContext()
](https://developer.android.com/reference/android/content/ContextWrapper.html#attachBaseContext(android.content.Context)) 方法并调用 [MultiDex.install(this)
](https://developer.android.com/reference/android/support/multidex/MultiDex.html#install(android.content.Context)) 来启用 Dalvik 可执行文件分包：
```
public class MyApplication extends SomeOtherApplication {
  @Override
  protected void attachBaseContext(Context base) {
     super.attachBaseContext(context);
     Multidex.install(this);
  }
}
```
构建应用后，Android 构建工具会根据需要构建主 DEX 文件 (classes.dex) 和辅助 DEX 文件（classes2.dex 和 classes3.dex 等）。然后，构建系统会将所有 DEX 文件打包到您的 APK 中。

运行时，Dalvik 可执行文件分包 API 使用特殊的类加载器来搜索适用于您的方法的所有 DEX 文件（而不是仅在主 classes.dex 文件中搜索）。

原文:https://developer.android.com/studio/build/multidex.html#keep