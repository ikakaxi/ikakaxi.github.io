title: 'android:clipChildren属性的作用'
author: superman
date: 2018-02-07 18:13:10
categories:
- android
tags:
- AndroidManifest
---
该属性默认为true,这个属性需要添加到最顶层的ViewGroup,作用是控制子View是否可以超出它所在的父View设定的边界
<!--more-->


比如ImageView设置高度100dp,而它所在的父View设置的高度50dp,那么给这个xml最顶层(注意是最顶层,最顶层不一定是这个ImageView的父View)设置android:clipChildren="false",那么这个ImageView是可以显示100dp高度的,否则会被剪切为50dp