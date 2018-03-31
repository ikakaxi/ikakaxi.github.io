title: Android Studio 2.2 问题汇总
author: superman
tags:
  - IDE
categories:
  - android studio
date: 2018-02-08 09:41:00
---
1. Gradle 2.2 使用
出现下面的警告，编译的时候出现更多。。。
<!--more-->
```
Warning:The `android.dexOptions.incremental` property is deprecated and it has no effect on the build process.
```
**解决：** 项目build.gradle 中删除下面配置 ，新的貌似不需要dex配置了！
```
//655xxx的方法限制分包处理 
dexOptions { 
  javaMaxHeapSize "4g" 
  //加快编译速度 
  incremental true 
}
```
2. java8 与 jack工具链问题
出现下面错误
```
Error:Could not get unknown property 'classpath' for task':app:transformJackWithJackForInstantrunconfigDebug' of type com.android.build.gradle.internal.pipeline
```
**解决：** 项目build.gradle 中删除下面配置
```
1.apply plugin: 'android-apt'
2.把apt替换成annotationProcessor
3.删除根目录的classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
4.这个不用Java8也可以这么写的，现在AS自带了注解插件
```
3. intant Run 与 shrinkResources 问题
当你进行运行时，出现下面错误：
```
app/build/intermediates/res/resources-debug-stripped.ap_' specified for property 'resourceFile' does not exist.
```
**解决：** 
项目build.gradle 中的 buildTypes 中的 shrinkResoutces设置为false 
具体可以看下：[https://developer.android.com/studio/build/shrink-code.html](https://developer.android.com/studio/build/shrink-code.html)
```
 buildTypes {
        debug {
            buildConfigField "boolean", "LOG_DEBUG", "true"
            debuggable true
            zipAlignEnabled true
            minifyEnabled false
            shrinkResources false //android studio 2.2 设置为false 
            signingConfig signingConfigs.config
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        release {
            buildConfigField "boolean", "LOG_DEBUG", "false"
            debuggable false
            zipAlignEnabled true
            minifyEnabled false
            shrinkResources false //android 2.2 设置为false
            signingConfig signingConfigs.config
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
```
4. Instant Run 与 C++ Debugger
出现下面三个问题：
```
Instant Run requires that the platform corresponding to your target device 
Instant Run requires that the platform corresponding to your target device (Android 5.0 (Lollipop)) is installed. 
C++ debugger package is missing or incompatible - do you want to fix it
```
**一样的解决方案：**
打开SDK管理，下载 5.0.1 , 5.1.1 的 SDK Platform 即可 ！可以试试，我只下载 5.1.1不行，下载了5.0.1才可以！

![](http://upload-images.jianshu.io/upload_images/545982-f1428a18553044e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.Android Studio Gradle Plugin 版本
Gradle Plugin 插件地址：配置已有的版本即可 
[https://jcenter.bintray.com/com/android/tools/build/gradle/](https://jcenter.bintray.com/com/android/tools/build/gradle/)
**举个例子：** 
目前最新版是 2.2 ，如果你在跟目录下的build.gradle配置 3.0 就会出现下面错误
下面配置会出错，截止今日最新版为2.2！
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```
报错如下！
```
Error:Could not find com.android.tools.build:gradle:3.0.
Searched in the following locations:
    file:/C:/Program Files/Android/Android Studio/gradle/m2repository/com/android/tools/build/gradle/3.0/gradle-3.0.pom
    file:/C:/Program Files/Android/Android Studio/gradle/m2repository/com/android/tools/build/gradle/3.0/gradle-3.0.jar
    https://jcenter.bintray.com/com/android/tools/build/gradle/3.0/gradle-3.0.pom
    https://jcenter.bintray.com/com/android/tools/build/gradle/3.0/gradle-3.0.jar
    http://maven.bughd.com/public/com/android/tools/build/gradle/3.0/gradle-3.0.pom
    http://maven.bughd.com/public/com/android/tools/build/gradle/3.0/gradle-3.0.jar
```
============ 上面是Android Studio Gradle Plugin 版本 ==================

============ 下面是Android Studio Gradle 编译进行编译的版本 ================== 
在gradle-wrapper.properties 中进行配置 ：
```
#Mon Sep 19 15:29:17 CST 2016
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-3.0-all.zip
```
Gradle 下载地址 ： 使用3.0编译，速度更快！ [http://services.gradle.org/distributions](http://services.gradle.org/distributions)