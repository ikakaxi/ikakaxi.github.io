title: 如何使用Service的Context弹出Dialog对话框，即全局性对话框
author: superman
date: 2018-02-07 18:14:24
categories:
- android
tags:
- Dialog
---

在dialog.show()语句前加入：
```
dialog.getWindow().setType(WindowManager.LayoutParams.TYPE_SYSTEM_ALERT);
```
然后在AndroidManifest.xml中加入权限：
```
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```
下面进行简单的解释：
<!--more-->

如果只在Service中写入常在Activity中使用的创建Dialog的代码，运行时是会发生错误的，因为Dialog的显示需要依附于一个确定的Activity类。而以上做法就是声明我们要弹出的这个提示框是一个系统的提示框，即全局性质的提示框，所以只要手机处于开机状态，无论它现在处于何种界面之下，只要调用dialog.show()，就会弹出提示框来。

需要注意的是，如果用**TYPE_SYSTEM_ALERT**，某些手机对底层进行了修改(小米，魅族之类)，系统会默认会拒绝该权限。解决：通过将**type**设定为**TYPE_TOAST**，就可以绕过检查，这时候需要改动**TYPE_SYSTEM_ALERT**为**TYPE_TOAST**