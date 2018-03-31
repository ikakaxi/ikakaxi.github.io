title: windowSoftInputMode属性讲解
author: superman
date: 2018-02-07 18:15:30
categories:
- android
tags:
- AndroidManifest
---

**windowSoftInputMode属性讲解**（下面这段内容我参考别人的博客，并加入我的一些意见）
我们从这个属性的名称中，可以很直观的看出它的作用，这个属性就是来设置窗口软键盘的交互模式的。android:windowSoftInputMode属性一共有9个取值，分别是：
<!--more-->

【A】stateUnspecified：软键盘的状态并没有指定，系统将选择一个合适的状态或依赖于主题的设置
【B】stateUnchanged：当这个activity出现时，软键盘将一直保持在上一个activity里的状态，无论是隐藏还是显示
【C】stateHidden：用户选择activity时，软键盘总是被隐藏
【D】stateAlwaysHidden：当该Activity主窗口获取焦点时，软键盘也总是被隐藏的
【E】stateVisible：软键盘通常是可见的
【F】stateAlwaysVisible：用户选择activity时，软键盘总是显示的状态
【G】adjustUnspecified：默认设置，通常由系统自行决定是隐藏还是显示
【H】adjustResize：该Activity总是调整屏幕的大小以便留出软键盘的空间
【I】adjustPan：当前窗口的内容将自动移动以便当前焦点从不被键盘覆盖和用户能总是看到输入内容的部分

来源:http://www.bozhiyue.com/anroid/boke/2016/0604/177871.html