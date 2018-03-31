title: Toolbar使用方法
author: superman
date: 2018-02-07 22:38:24
categories:
- android
tags:
- View
- Toolbar
---
Toolbar作为ActionBar的替代品,功能更加强大,使用更加方便,在这里根据自己在网上学习到的资料总结一下
<!--more-->


#####一. 配置
1. theme配置
2. layout配置
3. 代码配置

#####二.自定义
1. 状态栏背景颜色自定义
2. Toolbar背景颜色,文字颜色与大小自定义
3. 导航条背景颜色自定义(仅能在 API v21 也就是 Android 5 以后的版本中使用， 因此要将之设定在 res/values-v21/theme.xml 里面)
4. 页面背景颜色设置
5. 溢出菜单背景,文字颜色自定义

**一. 配置**
1. theme配置
theme要调整的地方有两处
一在 res/values/theme.xml中
二在 res/values-v21/theme.xml中
为了之后设定方便，我们先在 res/values/theme.xml 里增加一个名为 AppTheme.Base 的风格,因为此范例只使用 Toolbar，所以我们要将让原本的 ActionBar 隐藏起来
> 这里我修改过,之前parent="Theme.AppCompat",但是这样的话在Toolbar弹出菜单里的item没有水波纹点击效果,后来设置成下面的形式才有了点击效果

```
<resources>
  <style name="AppTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowNoTitle">true</item>
    <!-- 使用 API Level 22 编译的话，要拿掉前缀字 -->
    <item name="windowNoTitle">true</item>
  </style>
 
  <!-- Base application theme. -->
  <style name="UI.AppTheme" parent="AppTheme.Base">
  </style>
</resources>
```
然后将需要在manifest.xml中配置的 UI.AppTheme 的 parent 属性改为上面的AppTheme.Base

再来调整Android 5.0的style：  /res/values-v21/theme.xml，也将其 parent 属性改为  AppTheme.Base：
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="UI.AppTheme" parent="AppTheme.Base">
    </style>
</resources>
```

2. layout配置
在 activity_main.xml 里面添加 Toolbar 控件:
```
<android.support.v7.widget.Toolbar
  android:id="@+id/toolbar"
  android:layout_height="?attr/actionBarSize"
  android:layout_width="match_parent"/>
```
请记得用 support v7 里的 toolbar，不然只有 API Level 21 也就是 Android 5.0 以上的版本才能使用,?attr/actionBarSize意思是使用系统的值

3.代码配置
```
Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
setSupportActionBar(toolbar);
```
**二.自定义**
先看一个图片
![](http://upload-images.jianshu.io/upload_images/545982-74b1a27ea5225ee1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我就不分开写了,直接写我配置好的代码
1. 在 res/values/theme.xml中
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="AppTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowNoTitle">true</item>
        <!-- 使用 API Level 22 编译的话,要去掉前缀字 -->
        <item name="windowNoTitle">true</item>
        <!--Toolbar高度,暂时不知道怎么配置到上层使用Toolbar的地方-->
        <!--<item name="actionBarSize">?android:attr/actionBarSize</item>-->
        <!--Toolbar颜色等自定义-->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="colorControlNormal">@color/colorControlNormal</item>
        <item name="android:textColorPrimary">@color/textColorPrimary</item>
        <!--页面背景颜色-->
        <item name="android:windowBackground">@color/windowBackground</item>
        <!--该属性无效,需要在使用Toolbar的xml那设置-->
        <!--<item name="actionBarPopupTheme">@style/ToolbarPopupTheme</item>-->
        <item name="android:actionBarStyle">@style/CustomActionBarStyle</item>
    </style>

    <!-- Base application theme. -->
    <style name="UI.AppTheme" parent="AppTheme.Base">
    </style>
</resources>
```
2. 同时增加res/values/styles.xml文件,内容如下
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!--溢出弹出菜单样式-->
    <style name="ToolbarPopupTheme">
        <!--新增一个item，用于控制menu-->
        <item name="actionOverflowMenuStyle">@style/OverflowMenuStyle</item>
        <!-- 弹出层背景颜色 -->
        <item name="android:colorBackground">@color/popupBackground</item>
        <!-- 设置弹出菜单文字颜色 -->
        <item name="android:textColor">@color/popupTextColor</item>
    </style>

    <style name="OverflowMenuStyle" parent="Widget.AppCompat.Light.PopupMenu.Overflow">
        <!--把该属性改为false即可使menu位置位于toolbar之下,该值默认为true-->
        <item name="overlapAnchor">false</item>
    </style>

    <!--http://blog.csdn.net/afei__/article/details/51476096-->
    <style name="CustomActionBarStyle" parent="Widget.AppCompat.Light.ActionBar">
        <item name="contentInsetStart">0dp</item>
        <item name="contentInsetEnd">0dp</item>
        <item name="contentInsetLeft">0dp</item>
        <item name="contentInsetRight">0dp</item>
    </style>
</resources>
```
因为我是把这些做到了一个UILib库里,所以在res/values下还增加了2个文件
3. res/values/abstract_id.xml
```
<!--这些都是需要上层App设置的值-->
<resources>
    <item name="toolbar" type="id"/>
</resources>
```
4. res/values/abstract_color.xml
```
<!--这些都是需要上层App设置的值-->
<!--属性参考http://yifeng.studio/2017/04/18/android-theme-appcompat-color-attrs/ -->
<resources>
    <!--Toolbar背景颜色,通常也是一个App的主题色调,
        如果是ActionBar直接在这里设置即可,
        而Toolbar需要在xml设置background,
        我把xml里的Toolbar的背景颜色设置为下面这个值了-->
    <item name="colorPrimary" type="color"/>

    <!--状态栏颜色-->
    <item name="colorPrimaryDark" type="color"/>

    <!--
    许多控件在选中状态或获取焦点状态下使用这个颜色，常见有：
        CheckBox：checked 状态
        RadioButton：checked 状态
        SwitchCompat：checked 状态
        EditText：获取焦点时的 underline 和 cursor 颜色
        TextInputLayout：悬浮 label 字体颜色
        等等
    -->
    <item name="colorAccent" type="color"/>

    <!--页面背景颜色-->
    <item name="windowBackground" type="color"/>

    <!--标题栏文字和弹出的菜单文字颜色-->
    <item name="textColorPrimary" type="color"/>

    <!--某些 Views “normal” 状态下的颜色，
    常见如：
        unselected CheckBox 和 RadioButton，
        失去焦点时的 EditText，Toolbar 溢出按钮(更多)颜色，等等。-->
    <item name="colorControlNormal" type="color"/>

    <!-- Toolbar弹出层背景颜色 -->
    <item name="popupBackground" type="color"/>

    <!-- Toolbar弹出层文字颜色 -->
    <item name="popupTextColor" type="color"/>
</resources>
```
这些只是在这里定义一下名称,并没有实际的数值,上面说了我这些都写在UILib里,所以这些值到时候需要在上层App配置

5.在res/values-v21/theme.xml中
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="UI.AppTheme" parent="AppTheme.Base">
        <!--底部导航栏颜色,仅能在API v21也就是Android 5以后的版本中使用
        因此要将之设定在 res/values-v21/styles.xml 里面-->
        <item name="android:navigationBarColor">@color/colorPrimary</item>
    </style>
</resources>
```

**因为我自定义了Toolbar的溢出菜单背景和文字颜色等值(在res/values/styles.xml文件中配置的,名字叫OverflowMenuStyle),所以我需要在使用Toolbar的地方配置上这个style才会生效**

例如上面使用Toolbar的地方是activity_main.xml,所以这里需要改为
```
<android.support.v7.widget.Toolbar
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/colorPrimary"
    app:popupTheme="@style/ToolbarPopupTheme"/>
```

参考文章:
http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html
http://blog.mosil.biz/2014/10/android-toolbar/
http://yifeng.studio/2017/04/18/android-theme-appcompat-color-attrs/