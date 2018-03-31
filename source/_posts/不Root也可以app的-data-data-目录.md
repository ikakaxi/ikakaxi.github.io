title: 不Root也可以app的/data/data/目录
author: superman
date: 2018-02-07 22:35:49
categories:
- android
tags:
- debug
---
手机root后,执行adb shell然后执行su命令,就可以为所欲为,但是测试机不见得都可以root,这时候做一些操作,可以执行
```
run-as 需要查看内容的应用包名
```
这个命令查看,但是只能在debug下面才能使用,所以,只能看自己开发的app了...