title: Android模拟app被系统回收掉（需要root权限）
author: superman
date: 2018-02-07 18:19:24
categories:
- android
tags:
- debug
---

```
# 找到进程PID
adb shell ps | grep your.app.package
# 结果类似下面这样:
USER      PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
u0_a198   21997 160   827940 22064 ffffffff 00000000 S your.app.package
# kill掉该进程,如果说没有权限就先执行adb root
adb shell kill -9 21997
# app已经被模拟回收了
```

<!--more-->
我习惯用下面的方式
```
# 找到进程PID
adb shell
ps | grep your.app.package
# 结果类似下面这样:
USER      PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
u0_a198   21997 160   827940 22064 ffffffff 00000000 S your.app.package
# kill掉该进程,如果说没有权限就先执行su
kill -9 21997
# app已经被模拟回收了
```