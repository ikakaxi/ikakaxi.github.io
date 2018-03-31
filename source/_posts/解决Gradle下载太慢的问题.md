title: 解决Gradle下载太慢的问题
author: superman
date: 2018-02-08 09:52:49
tags:
---
因为GFW的原因,国内用android studio的时候,在gradle这步一定会卡住,所以需要以下步骤自己去下载gradle
<!--more-->
###1.在gradle-wrapper.properties中查看gradle下载地址和版本
###2.去查看所有分发的gradle版本地址:https://services.gradle.org/distributions/
###3.下载完成后放到什么地方?
    1.windows在 C:\Users\yourname\.gradle\wrapper\dists\gradle-版本\随机字符串\
    2.mac在 /Users/用户名/.gradle/wrapper/dists/gradle-版本/随机字符串/
    3.linux
###4.将gradle-版本.zip.part移除，把自己下载的gradle-gradle-版本.zip复制到这个目录。然后再次启动Andriod Studio，会自动读取gradle并解压

#完成