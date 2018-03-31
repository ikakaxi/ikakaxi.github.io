title: TargeApi的使用方法
author: superman
date: 2018-02-07 22:37:53
categories:
- android
tags:
---
直接记录总结:

在使用高于 minSdkVersion API level 的方法需要:

1. 用@TargeApi($API_LEVEL)使可以编译通过,不建议使用@SuppressLint("NewApi")。
2. 运行时判断 API level;仅在足够高,有此方法的API level系统中,调用此方法。
3. 保证低API版本通过其他方法提供功能实现。