title: android自定义权限可能出现的问题
author: superman
date: 2018-02-07 22:34:27
categories:
- android
tags:
- 权限
---

为了方便描述,这里先定义两个app的名字:
调用方:A
被调用方:B
A调用B的Activity名字:AActivity
被调用方的Activity名字:BActivity
一个很可能出现的异常:
<!--more-->
```
java.lang.SecurityException: Permission Denial: starting Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10000000 cmp=xxx/.xxx.BActivity } from null (pid=3202, uid=2000) requires xxx.permission
```
在B的manifest.xml的manifest标签里定义自定义权限(signature代表同样签名的app才可以调用)
```
<permission
        android:name="xxx.permission"
        android:label="权限描述"
        android:permissionGroup="权限组名,随便写"
        android:protectionLevel="signature"/>
```
还需要在B的manifest.xml里写上类似下面的代码,注意最好添加android:exported="true",虽然如果intent-filter下面有action会默认android:exported为true,但是最好还是写上,因为即使有action也可以将android:exported设置为false的
(例如这里叫BActivity)
(如果需要隐藏这个app的图标,就添加类似下面的data属性)
```
<activity
  android:name=".BActivity"
  android:exported="true">
  <intent-filter>
    <action android:name="android.intent.action.MAIN"/>
    <category android:name="android.intent.category.LAUNCHER"/>
    <data
        android:host="随便写"
        android:scheme="随便写" />
  </intent-filter>
</activity>
```

然后在A的manifest.xml的manifest标签里写上,否则如果先安装A再安装B会出现上面说的授权错误
```
<uses-permission android:name="xxx.permission"/>
```