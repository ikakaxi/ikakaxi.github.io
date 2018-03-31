title: GridLayout子控件超出屏幕的解决办法
author: superman
date: 2018-02-07 22:31:25
categories:
- android
tags:
- View
---

GridLayout的具体使用方法不赘述,这里主要解决子控件超出屏幕的解决办法,在项目用使用GridLayout的时候,发现EditText超出屏幕,解决办法是这一列的EditText都加上下面的属性,如果这一列某一个没有加上下面的属性,那么这一列所有的EditText仍然超出屏幕
<!--more-->
```
android:layout_width="wrap_content"
android:layout_gravity="fill_horizontal"
```
加上这个属性,就可以充满屏幕而不是超出屏幕了
例如:
```
<GridLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="2">
        <TextView
            android:layout_gravity="right"
            android:layout_marginRight="10dp"
            android:text="标题1:"/>

        <EditText
            android:layout_width="wrap_content"
            android:layout_gravity="fill_horizontal"/>

        <TextView
            android:layout_gravity="right"
            android:layout_marginRight="10dp"
            android:text="标题2:"/>

        <EditText
            android:layout_width="wrap_content"
            android:layout_gravity="fill_horizontal"/>
    </GridLayout>
```