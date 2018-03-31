title: 解决“Cannot merge new index xxx into a non-jumbo instruction”的问题
author: superman
date: 2018-02-07 22:27:53
categories:
- android
tags:
- 异常
---

xxx一般是一个整数，比如66345等，从这个提示看，和Dex方法超过64K的限制一样，应该是同一个问题。不过App已经解决了这个64K方法的问题，怎么还会提示呢。
<!--more-->
从提上看，是一个non-jumbo，让我想到了Dex的jumbo模式，这是一个用来配置制定该Dex是不是一个巨大的Dex的。报错的日志里显示是一个模块，从这可以推断出基本的问题：该模块需要生成一个Dex放进AAR包里给App使用，现在这个Dex生成不了啦，提示太大，这个是根本原因，所以只要解决了这个就可以了。
那么以前为什么不会出现这个问题呢，我们从git提交的历史来看，发现昨天的需求新增了一个第三方包，导致该模块的方法变多，超过了限制，所以今天就有了这个错误的提示。。
既然知道了原因，那么就很好解决了。
使用Gradle构建的，在模块的build.gradle里配置：
```
android {
  dexOptions {
    jumboMode true
  }
}
```

如果是使用Eclipse+Ant构建的，在project.properties文件中增加如下配置：
```
dex.force.jumbo=true
```

就可以解决如上问题了。。
关于超过64K方法分Dex的解决办法可以参考官方的[Configure Apps with Over 64K Methods](https://developer.android.com/studio/build/multidex.html)这篇文章。