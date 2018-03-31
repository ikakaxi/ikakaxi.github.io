title: linux常用命令-用户管理命令
author: superman
date: 2018-02-08 09:46:20
tags:
---
useradd 用户名
passwd 用户名:修改该用户的密码
groupadd 组名
<!--more-->
who:
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/545982-e39eaacca1825e18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　who用来查看现在登录了几个用户,得到的信息含义如下

　　登录用户名　　登录终端类型　　登录时间　　登录终端的IP地址(如果没有写就是本机登录)

|登录终端类型|登录终端含义|
|:------:|:------:|
|tty|本地终端|
|pts|远程终端,后面的 /数字 代表不同的远程终端号|

w:查看更详细的已登录用户信息

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/545982-98f12894c88b6db2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　信息含义如下:

　　当前时间　　系统已运行时间(uptime命令可以得到相同信息)　　当前有几个用户登录　　负载信息,分别是过去1,5,15分钟的负载情况

　　IDLE 空闲运行时间
　　PCPU   当前用户登录以后当前执行的操作占用CPU的时间
　　WHAT 当前执行了什么操作
　　JCPU   当前用户登录以后累计执行的所有操作占用CPU的时间
