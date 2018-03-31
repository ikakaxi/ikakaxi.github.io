title: 解决ScrollView里如果有动态更新的ChildView时会自动滚动到底部的方法
author: superman
date: 2018-02-07 18:16:42
categories:
- android
tags:
- ScrollView
---

在这个ChildView的xml属性里加上

android:focusable="true"
android:focusableInTouchMode="true"

就可以解决