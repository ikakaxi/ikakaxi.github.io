title: android studio 的配置
author: superman
date: 2018-02-07 18:15:56
categories:
- android
tags:
- IDE
---

因为GFW,android studio不是下载了就可以用的,最常见的是gradle的问题,现在把自己遇到的问题记录一下,以后再配置就直接看文章就可以了
<!--more-->

1.gradle问题,下载最新gradle,然后android studio指定gradle目录

2.虽然指定了gradle目录,但是build的时候还得FQ,然后android studio会下载一些文件,这些文件就很小了,FQ下没关系

3.apply plugin: 'com.android.application'这句话会报错,需要添加下面的代码到build.gradle,与android同级
```
buildscript {
    repositories {
        mavenCentral() // or jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.2'
    }
}
```
4.junit报错,FQ可以解决,或者直接删掉testCompile 'junit:junit:4.12'