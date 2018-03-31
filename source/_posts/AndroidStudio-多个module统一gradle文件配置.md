title: AndroidStudio 多个module统一gradle文件配置
author: superman
date: 2018-02-08 09:41:09
tags:
---
看到AS中External Libraries有好几个同名但是版本不一样的lib的时候，真的逼死强迫症患者。 这些多个重复的lib出现的原因就是多个module各自compile了同名但是不同版本的lib（即依赖没有统一），所以统一性的确认这些依赖就好了。
<!--more-->
<strong>Step1:</strong>
在根目录新建一个config.gradle文件，里面键入要统一的依赖：
```
ext {
  android = [
          compileSdkVersion: 23,
          buildToolsVersion: "23.0.3",
          minSdkVersion    : 15,
          targetSdkVersion : 22,
          versionCode      : 1,
          versionName      : "1.0"
  ]

  dependencies = [
          "gson"               : "com.google.code.gson:gson:2.6.2",
          "eventbus"           : 'org.greenrobot:eventbus:3.0.0',
          "butterknife"        : 'com.jakewharton:butterknife:7.0.1',
          "support-design"     : 'com.android.support:design:24.1.1',
          "support-appcompatV7": 'com.android.support:appcompat-v7:24.1.1',
          "support-percent"    : 'com.android.support:percent:24.1.1',
          "support-multidex"   : 'com.android.support:multidex:1.0.1',
          "glide"              : 'com.github.bumptech.glide:glide:3.7.0',
          "support-v4"         : 'com.android.support:support-v4:24.1.1',
          "okhttp3"            : 'com.squareup.okhttp3:okhttp:3.3.1',
          "nineoldandroids"    : 'com.nineoldandroids:library:2.4.0'
  ]
}
```
<strong>Step2:</strong>
在根目录的build.gradle文件里面头部增加一句引用 apply from: "config.gradle"

<strong>Step3:</strong>
在module里面开始应用：
```
compileSdkVersion rootProject.ext.android.compileSdkVersion //android{}节点
compile rootProject.ext.dependencies["support-appcompatV7"] //dependencies{}节点
```
<strong>The end</strong>
clean一下去External Libraries看看，是不是还有重复的，如果还有，说明前面config里面的依赖其他地方还有遗漏的，全局搜索一下在同样方式替换一下就好了。

转载:https://jackl-e-e.github.io/android/2016/08/26/多module统一gradle配置.html