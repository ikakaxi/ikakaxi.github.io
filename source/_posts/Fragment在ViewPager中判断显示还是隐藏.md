---
title: Fragment在ViewPager中判断显示还是隐藏
date: 2018-02-07 16:59:56
categories:
- android
tags:
- fragment
---


注意只有在ViewPager中才会调用此方法
```
@Override  
public void setUserVisibleHint(boolean isVisibleToUser) {  
  super.setUserVisibleHint(isVisibleToUser);  
  if (isVisibleToUser) {  
  //相当于activity的onResume  
  } else {  
  //相当于activity的onPause  
  }  
}  
```