---
title: android-获得".apk"文件的相关信息。包名、版本号等等
date: 2018-02-07 17:37:00
categories:
- android
tags: 
- apk
---


```
String filePath = "/sdcard/feijiedemo.apk";  
PackageManager packageManager = getPackageManager();  
PackageInfo packageInfo = packageManager.getPackageArchiveInfo(filePath, PackageManager.GET_ACTIVITIES);  
Log.d("name", packageInfo.packageName);  
Log.d("uid", packageInfo.sharedUserId);  
Log.d("vname", packageInfo.versionName);  
Log.d("code", packageInfo.versionCode+"");
```