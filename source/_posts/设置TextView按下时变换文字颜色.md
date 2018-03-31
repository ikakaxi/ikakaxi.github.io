title: 设置TextView按下时变换文字颜色
author: superman
date: 2018-02-07 18:13:57
categories:
- android
tags:
- View
- TextView
---
在res中建立一个color文件夹，在其中新建一个xml（这里为text_color.xml）：
<!--more-->

```
<selector xmlns:android="http://schemas.android.com/apk/res/android" >
  <item android:state_pressed="true" android:color="@color/white"></item>
  <item android:color="@color/black"></item>
</selector>
```
然后设置你的TextView属性：

```
<TextView
  android:id="@+id/textView"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:textColor="@color/text_color"
  android:textSize="11dp"
  android:clickable="true"
  android:text="忘记密码" />
```
重点要讲一下的是clickable属性，默认该属性为false，此时TextView是不可点击的，也就不会有变换颜色的效果。所以要将该属性设为true。