title: com.android.browser/com.android.browser.BrowserActivity找不到
author: superman
date: 2018-02-07 22:37:19
categories:
- android
tags:
- 异常
---

```
android.content.ActivityNotFoundException
Unable to find explicit activity class {com.android.browser/com.android.browser.BrowserActivity}; have you declared this activity in your AndroidManifest.xml?
```
在三星的一款手机遇到这个问题,后来找到原因是他刷机后没有这个累了,之前的调用方法是
```
Intent intent = new Intent();  
intent.setAction(Intent.ACTION_VIEW);  
Uri content_uri_browsers = Uri.parse(***);  
intent.setData(content_uri_browsers);  
intent.setClassName("com.android.browser", "com.android.browser.BrowserActivity");  
this.startActivity(intent);  
```
解决这个问题需要去掉intent.setClassName(...)
改完以后是下面这样
```
Intent intent = new Intent();  
intent.setAction(Intent.ACTION_VIEW);  
Uri content_uri_browsers = Uri.parse(***);  
intent.setData(content_uri_browsers);  
this.startActivity(intent);  
```
这样让用户去选择浏览器下载,就解决这个问题了