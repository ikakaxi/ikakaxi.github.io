title: gradle类重复的问题解决方法
author: superman
date: 2018-02-08 09:53:27
tags:
---
今天遇到一个gradle的类重复问题,学习到一个命令 **gradle -q dependencies
**,可以查看项目里包的依赖关系,发生这个错误是因为我用了一个相册的项目,这个项目里用到了v4包,我自己的项目也用到了v4包,

然后我把这个项目 
<!--more-->
```
compile 'cn.finalteam:galleryfinal:1.4.8.7'
```
改为
```
compile('cn.finalteam:galleryfinal:1.4.8.7') {
  exclude module: 'support-v4'
}
```
就解决了
 
如果是本地的项目,可以用下面的代码
```
compile (project(':yourAndroidLibrary')){
  exclude module: 'support-v4'
}
```