title: 如何在library中使用productFlavors
author: superman
tags: []
categories:
  - gradle
date: 2018-02-08 09:51:00
---
源地址:http://blog.csdn.net/yulyu/article/details/70257015?utm_medium=referral&utm_source=itdadao
<!--more-->
###前言：
前面笔者介绍过，如果用一套代码，定制化多个app。那就是使用productFlavors。
一般情况下都没有问题，但是在library的gradle里面，直接使用productFlavors是不允许的。所以下面我们就来介绍一下如何在library中使用productFlavors。
###1.基础
前面介绍过如何使用productFlavors，不了解的朋友需要先看看这篇文章
[活用productFlavors,批量定制化](http://blog.csdn.net/yulyu/article/details/59111697)
[http://blog.csdn.net/yulyu/article/details/59111697](http://blog.csdn.net/yulyu/article/details/59111697)
###2.场景介绍
首先我们引用了一个picmodule，然后picmodule里面有一个正常的产品，两个定制化的产品

![image.png](http://upload-images.jianshu.io/upload_images/545982-e0a9a4ea96a62da6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main是普通产品，red和blue是定制化产品（这里只是里面使用的图片不一样）
###3.解决方法
于是我们就在picmodule的gradle配置productFlavors，但是picmodule是属于一个library，所以这样做是不允许的，项目将找不到引入的picmodule

![image.png](http://upload-images.jianshu.io/upload_images/545982-65c9816351c171d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么如何解决呢？
首先我们需要在picmodule的gradle里面加入一句话（记住不要漏了）
```
publishNonDefault true
```

![image.png](http://upload-images.jianshu.io/upload_images/545982-3762056f0f5faf2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接着我们在application下的build.gradle里面加入一些配置
在[Android](http://lib.csdn.net/base/android)模块内加入
```
productFlavors{
    common{}

    red{}

    blue{}
}
```

在gradle最外层加入
```
configurations {
    commonCompile
    redCompile
    blueCompile
}
```


![image.png](http://upload-images.jianshu.io/upload_images/545982-9a7cae994136745b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接着修改一下引入picmodule的方法
平时引入module是这样的
```
compile project(':picmodule')
```

我们改成下面这样
```
commonCompile project(path: ':picmodule', configuration: 'commonRelease')
redCompile project(path: ':picmodule', configuration: 'redRelease')
blueCompile project(path: ':picmodule', configuration: 'blueRelease')
```

这里library我是用的Release包，如果要用debug版的library也可以改成下面这样
```
commonCompile project(path: ':picmodule', configuration: 'commonDebug')
redCompile project(path: ':picmodule', configuration: 'redDebug')
blueCompile project(path: ':picmodule', configuration: 'blueDebug')
```

**然后同步一下就可以了**
**这个时候如果找不到R文件，那么clean一下或者重启一下as都可以**