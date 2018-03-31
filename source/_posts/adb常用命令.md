title: adb常用命令
author: superman
date: 2018-02-07 18:17:36
categories:
- android
tags:
- adb
---

**1.当有多个设备online时，命令行窗口通过adb连接指定设备方法**
<!--more-->
1. 通过adb devices命令获取所有online设备的serial number。
```
C:\Users\Administrator>adb devices
List of devices 
attachedemulator-5554   device
SH0A6PL00243    device
```
上面表示，当前有两个设备online，第一个emulator-5554是模拟器，后一个是真机会SH0A6PL00243。
2. 通过adb -s <serial number> cmd向设备发送adb命令。
比如：运行命令shell。
C:\Users\Administrator>adb -s SH0A6PL00243 shell
比如：down一个应用的[数据库]到本地f:\test目录下面。
```
C:\Users\Administrator>adb -s SH0A6PL00243 pull data/data/com.android.tencent/databases/AgendaDetails.db f:\test
555 KB/s (5120 bytes in 0.009s)
```
运行其它命令和运行pull命令一样的，只是在adb和cmd之间需要额外添加-s <serial number>即可。

**2.模拟app被系统回收掉（需要root权限）**
```
# find the process id
adb shell ps
# then find the line with the package name of your app
# Mac/Unix: save some time by using grep:
adb shell ps | grep your.app.package
# The result should look like:
# USER      PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
# u0_a198   21997 160   827940 22064 ffffffff 00000000 S your.app.package
# Kill the app by PID
adb shell kill -9 21997
# the app is now killed
```