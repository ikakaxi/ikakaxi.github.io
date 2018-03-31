title: 360插件框架RePlugin的坑
author: superman
date: 2018-02-07 22:32:34
categories:
- android
tags:
- 第三方框架
---
6月30号360新开源了一个插件开发框架RePlugin,然而文档不全,根据360的文档你做出来的项目一编译就是一大堆错误,因为很多需要配置的东西在360的插件开发文档里根本就没有,我在这里记录一下给需要的人
<!--more-->


####1.https://github.com/Qihoo360/RePlugin/wiki/主程序接入指南
在这里的第一步 <strong>[添加 RePlugin Host Gradle 依赖]</strong>之前还有一步,文档并没有写,就是在项目根目录的 <strong>build.gradle</strong>中的<strong>buildscript</strong>和<strong>allprojects</strong>的下面添加
```
        maven {
            url  "https://dl.bintray.com/qihoo360/replugin"
        }
```
否则框架所需要的资源根本找不到,完整配置类似如下代码:

```
buildscript {
    repositories {
        jcenter()
        maven {
            url  "https://dl.bintray.com/qihoo360/replugin"
        }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.3'
        classpath 'com.qihoo360.replugin:replugin-host-gradle:2.1.0'
    }
}

allprojects {
    repositories {
        jcenter()
        maven {
            url  "https://dl.bintray.com/qihoo360/replugin"
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

####2.还是在https://github.com/Qihoo360/RePlugin/wiki/主程序接入指南 还有一步也没有写,必须在你的宿主Module的assets下面增加plugins文件夹,即使里面没有插件文件也要创建该文件夹,否则会出现以下错误
```
Error:Execution failed for task ':app:processDebugResources'.
> /Users/xxx/Documents/WorkSpace/WorkSpaceAndroid/TestRePlugin/app/src/main/assets/plugins
```
添加了这个文件夹后编译会自动出现plugins-builtin.json文件

####3.LocalBroadcastManager类找不到的错误
你的程序需要添加v4包,因为插件框架内部使用到了v4包的LocalBroadcastManager类

####4.Failed to apply plugin [id 'replugin-plugin-gradle']
在https://github.com/Qihoo360/RePlugin/wiki/插件接入指南 这里需要注意这几个地方:

######4.1.1 gradle版本必须是gradle-2.14.1-all,如果编译出现下图这样的错误
![image.png](http://upload-images.jianshu.io/upload_images/545982-73c4e051275ac484.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
那么你需要修改你的工程中的gradle->warpper->gradle-wrapper.properties文件的distributionUrl为https\://services.gradle.org/distributions/gradle-2.14.1-all.zip
比如下面这样的配置
```
#Sun Jul 02 15:33:02 CST 2017
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip
```

######4.1.2 apply plugin: 'replugin-plugin-gradle'这句话要放在build.gradle文件的末尾处
例如下面这样

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 25
    buildToolsVersion "26.0.0"
    defaultConfig {
        applicationId "com.test.replugin.plugin"
        minSdkVersion 15
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile 'com.android.support:support-v4:25.3.0'
    compile 'com.qihoo360.replugin:replugin-plugin-lib:2.1.0'
}
apply plugin: 'replugin-plugin-gradle'
```

<small>参考问题:https://github.com/Qihoo360/RePlugin/issues/53</small>

#####4.2 在https://github.com/Qihoo360/RePlugin/issues/43 这个问题中,作者说宿主和插件不能在同一个工程,否则编译不通过,会报上面的Failed to apply plugin [id 'replugin-plugin-gradle']错误,即使你的gradle配置全部正确也无法通过编译,本人测试发现确实是这样的,作者建议将插件和宿主放到同一个工程目录下（本质上仍然是两个工程），这样只需打开一个根目录，就能打开主程序 + 插件工程了。