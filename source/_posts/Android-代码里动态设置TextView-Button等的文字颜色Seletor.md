title: Android 代码里动态设置TextView/Button等的文字颜色Seletor
author: superman
date: 2018-02-07 22:37:04
categories:
- android
tags:
- View
---

######前言 
今天遇到个很蛋疼的问题，下载时，多个按钮共用一个button，也就是不同下载状态下，button的背景以及字体颜色都不一样，结果自己挖了坑把自己埋进去了。
<!--more-->
以下是我在/res/color文件夹里给button设置的文字颜色seletor：networkdata_btn_open_txtcolor_selector.xml
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:color="#ffffff" android:state_pressed="true"></item>
    <item android:color="#ffffff" android:state_selected="true"></item>
    <item android:color="#f88b00"></item>

</selector>
```
######坑？ 
在代码里设置颜色seletor，以为在代码里直接调用 button.setTextColor(int colorValue) 就可以了，结果运行效果让我傻眼了。
```
mBtnDownAndOpen.setText(DOWNLOAD_OPEN);  //设置button文字
mBtnDownAndOpen.setTextColor(mContext.getResources().getColor(R.color.networkdata_btn_open_txtcolor_selector));    //设置button文字颜色
mBtnDownAndOpen.setBackground(mContext.getResources().getDrawable(R.drawable.networkdata_btn_open_selector));    //设置button背景
```
######郁闷的是，只能读取到没获取焦点时的色值，也就是 <item android:color="#f88b00"></item> ，其他状态获取不到。
######如何填坑？ 
采用 
```
mBtnDownAndOpen.setTextColor(mContext.getResources().getColorStateList(R.color.networkdata_btn_open_txtcolor_selector)); 
```
为button设置文字颜色。
区别在于：改之前用的是getColor，改之后用的是getColorStateList
######WHY? 
文字颜色的seletor在代码里的显示形式是ColorStateList，而res/color放的就是ColorStateList资源XML文件，getColor只能读取单个的color。

转载:http://blog.csdn.net/zhangshao45612/article/details/49784997