title: android实现通过浏览器点击链接打开本地应用（APP）并拿到浏览器传递的数据
author: superman
date: 2018-02-07 18:18:59
categories:
- android
tags:
---

点击浏览器中的URL链接，启动特定的App。
首先做成HTML的页面，页面内容格式如下：
<!--more-->
```
<a href="scheme://host/path?key=value">启动应用程序</a> 
```
这一句就可以了。path,key,value都可以为空
作为测试好好写了一下，如下：
```
<a href="myapp://test.com/openwith?name=zhangsan&age=26">启动应用程序</a> 
```
接下来是Android端。
如果要启动的Activity不是MainActivity,则需要加上如下代码
```
<intent-filter>  
    <action android:name="android.intent.action.VIEW"/>  
    <category android:name="android.intent.category.DEFAULT" />  
    <category android:name="android.intent.category.BROWSABLE" />  
    <data android:scheme="myapp" android:host="test.com" android:pathPrefix="/openwith"/>  
</intent-filter> 
```

本文参考http://blog.csdn.net/geekpark/article/details/16118457