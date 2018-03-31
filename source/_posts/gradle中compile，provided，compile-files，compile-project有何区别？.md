title: gradle中compile，provided，compile files，compile project有何区别？
author: superman
tags: []
categories:
  - gradle
date: 2018-02-08 09:53:00
---
在一个Android项目的build.gradle中，dependencies闭包中有以下四种形式的声明：
<!--more-->

```
compile 'com.android.support:appcompat-v7:22.2.1'
provided 'com.squareup.dagger:dagger-compiler:1.2.1'
compile files('libs/picasso-2.4.0.jar')
compile project(':androidPullToRefresh')
```

1，3，4本质上是一样的，区别在于：

1 是从repository（默认是jCenter())里下载一个依赖包进行编译并打包
3 是从本地的libs目录下寻找picasso-2.4.0.jar这个文件进行编译并打包。类似的命令有`compile fileTree(dir: 'libs', include: '*.jar')`——将libs目录下所有jar文件进行编译并打包。
4 是将另一个module（等同eclipse中的library)进行编译并打包
至于provided，是提供给那些只编译不打包场景的命令。就是，我在编译的时候对某一个jar文件有依赖，但是最终打包apk文件时，我不想把这个jar文件放进去，可以用这个命令。生成jar文件的时候,也不会打包provided的模块。

推荐一本书 《gradle for android》
https://segmentfault.com/a/1190000004229002