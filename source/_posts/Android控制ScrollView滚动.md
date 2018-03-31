title: Android控制ScrollView滚动
author: superman
date: 2018-02-07 18:09:05
categories:
- android
tags:
- View
- ScrollView
---

有两种办法:

第一种，使用scrollTo(),这个方法不需要handler,直接调用就行

第二种方式，使用fullScrol(),下面我们看一下这个函数:
<!--more-->
```
　　scrollView.fullScroll(ScrollView.FOCUS_DOWN);滚动到底部
　　scrollView.fullScroll(ScrollView.FOCUS_UP);滚动到顶部
```

　　需要注意的是，该方法不能直接被调用
　　因为Android很多函数都是基于消息队列来同步，所以需要异步操作，
　　addView完之后，不等于马上就会显示，而是在队列中等待处理，虽然很快，但是如果立即调用fullScroll， view可能还没有显示出来，所以会失败
　　应该通过handler在新线程中更新