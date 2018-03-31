title: 解决No cached version of net.orfjackal.retrolambda:retrolambda:x.x.x available
  for offline mode.
author: superman
date: 2018-02-07 18:18:31
categories:
- android
tags:
- 异常
---
转载:https://my.oschina.net/zhangdengjiexuyu/blog/701311
<!--more-->


为了使用java8 的lambda表达式特性，特地在项目中添加了 me.tatarka:gradle-retrolambda插件，按照github上的指南，配置好gradle之后，同步项目是没问题的，但是运行打包安装的时候就出了问题，报错：

```
No cached version of net.orfjackal.retrolambda:retrolambda:x.x.x available for offline mode
```
看到offline字眼，果断把Gradle配置项的Global Gradle settings的offline work 取消了，结果还是出现了改错误，实在不行，找不到办法，google了一下，发现了问题的所在：compile部分的运行参数之前加了--offline参数了，去除改参数即可。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/545982-3e6b43967c84d670.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/545982-95a63f20f45ca8ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置gradle离线模式的地方有两处，如上图所示

对原文的补充:在github上搜索retrolambda会发现有2个最多星的,分别是evant/gradle-retrolambda和orfjackal/retrolambda。他们之间是啥关系呢？简单来说，gradle-retrolambda只是AS的一个gradle插件，他里面也依赖第二个开源库orfjackal/retrolambda。所以这里我们直接选第一个进行配置。