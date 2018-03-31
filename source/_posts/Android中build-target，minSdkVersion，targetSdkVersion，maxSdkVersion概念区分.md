title: Android中build target，minSdkVersion，targetSdkVersion，maxSdkVersion概念区分
author: superman
date: 2018-02-07 22:37:36
categories:
- android
tags:
- gradle
---
原文:http://blog.csdn.net/zhangjg_blog/article/details/17142395
<!--more-->


个人总结一下:
1. build target:
一般情况下，应该使用最新的API level作为build target。这也是eclipse生成项目时的默认行为。
如果使用没有在build target里存在的API就会报错

2. minSdkVersion:
指明应用程序运行所需的最小API level。如果不指明的话，默认是1。也就是说该应用兼容所有的android版本。我们应该总是声明这个属性。否则基本上所有的API都没办法调用,因为API等级为1的时候没有什么现在可以用的API
**如果系统的API level低于android:minSdkVersion设定的值，那么android系统会阻止用户安装这个应用。
如果指明了这个属性，并且在项目中使用了高于这个API level的API， 那么会在编译时报错。**

3. targetSdkVersion:
标明应用程序目标API Level的一个整数。如果不设置，默认值和minSdkVersion相同。
这个属性通知系统，你已经针对这个指定的目标版本测试过你的程序，系统不必再使用兼容模式来让你的应用程序向前兼容这个目标版本。应用程序仍然能在低于targetSdkVersion的系统上运行。
根据你设置的targetSdkVersion 的值，系统会执行很多兼容行为。一些行为在对应平台版本的Build.VERSION_CODES中有讨论。
**targetSdkVersion这个属性是在程序运行时期起作用的，系统根据这个属性决定要不要以兼容模式运行这个程序。**
一般情况下，应该将这个属性的值设置为最新的API level 值，这样的话可以利用新版本系统上的新特性。eclipse在生成项目时，默认将该值设置为最高，如果设置一个较低的值，会给出一个警告