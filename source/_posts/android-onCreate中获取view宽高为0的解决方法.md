title: android onCreate中获取view宽高为0的解决方法
author: superman
date: 2018-02-07 18:13:36
categories:
- android
tags:
- View
---
通过post可以将一个runnable投递到消息队列的尾部，然后等待UI线程Looper调用此runnable的时候，view也已经初始化好了。
<!--more-->

```
view.post(new Runnable() {
    @Override
    public void run() {
        L.i("post(Runnable) : view.getWidth():" + view.getWidth() + "  view.getHeight():" + view.getHeight());
    }
});
```