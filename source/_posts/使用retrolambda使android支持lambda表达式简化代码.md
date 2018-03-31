title: 使用retrolambda使android支持lambda表达式简化代码
author: superman
date: 2018-02-07 18:18:46
categories:
- android
tags:
---

在github上搜索retrolambda会发现有2个最多星的,分别是evant/gradle-retrolambda和orfjackal/retrolambda。他们之间是啥关系呢？简单来说，gradle-retrolambda只是AS的一个gradle插件，他里面也依赖第二个开源库orfjackal/retrolambda。所以这里我们直接选第一个进行配置。
<!--more-->

在module的gradle.build中加入下面的代码即可在项目中使用lambda表达式了

```
...
apply plugin: 'me.tatarka.retrolambda'
buildscript {
    repositories {
        mavenCentral()
    }

    dependencies {
        classpath 'me.tatarka:gradle-retrolambda:3.4.0'
    }
}
android {
...
    compileOptions {
        targetCompatibility 1.8
        sourceCompatibility 1.8
    }
...
}
//指定源码编译的级别,使用下列代码,会将代码编译到兼容1.6的字节码格式,android2.3.3-4.4使用的jdk6
retrolambda {
    javaVersion JavaVersion.VERSION_1_6
}

```