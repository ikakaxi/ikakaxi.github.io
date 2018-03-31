title: Android的Intent.FLAG_ACTIVITY_CLEAR_TOP无效
author: superman
date: 2018-02-07 18:06:39
categories:
- android
tags:
- intent
---
转载:http://blog.csdn.net/u011361576/article/details/48626237

<!--more-->

今天写代码遇到了一个问题：
当 B - A - B 跳转的时候，使用Intent的FLAG_ACTIVITY_CLEAR_TOP会让第一个B和第二个A，destory掉，但是当B - A - C跳转的时候不会调用B和A的destory。
查看API文档才发现原因，所以这里记录一下避免下次忘记了：

``` 
public static final int FLAG_ACTIVITY_CLEAR_TOP
Added in [API level 1](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
If set, and the activity being launched is already running in the current task, then instead of launching a new instance of that activity, all of the other activities on top of it will be closed and this Intent will be delivered to the (now on top) old activity as a new Intent.
```
 
如果设置这个属性，是当要启动的Activity已经存在当前Task中，才会在启动的时候销毁其他的Activity。
所以上面当A跳C的时候不满足此条件。
 
当然如果想实现这个效果可以使用：
```
it.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK);
```
不过此方法要求最低API为11