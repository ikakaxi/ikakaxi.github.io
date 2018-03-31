title: 使用adb dumpsys 命令查看app占用内存
author: superman
date: 2018-02-07 22:10:49
categories:
- android
tags:
- debug
---

adb是一个非常强大的工具，使用adb查看应用程序内存使用情况可按如下格式在命令行里查看内存使用情况：
adb shell dumpsys meminfo <package_name>
其中，package_name 也可以换成程序的pid，pid可以通过 adb shell top | grep app_name 来查找，下图是某个程序的内存使用情况：
![](http://upload-images.jianshu.io/upload_images/545982-75efbde6fda86be3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)重点关注如下几个字段：
（1） Native/Dalvik 的 Heap 信息具体在上面的第一行和第二行，它分别给出的是JNI层和Java层的内存分配情况，如果发现这个值一直增长，则代表程序可能出现了内存泄漏。
（2） Total 的 PSS 信息这个值就是你的应用真正占据的内存大小，通过这个信息，你可以轻松判别手机中哪些程序占内存比较大了。