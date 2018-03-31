title: 用一张图告诉你Android中的事件传递机制
author: liuhc
tags: []
categories:
  - android
date: 2018-03-31 23:38:00
---
[![事件传递机制_gaitubao_com_watermark.png](https://upload-images.jianshu.io/upload_images/545982-e1b0bea3b5de2844.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://upload-images.jianshu.io/upload_images/545982-e1b0bea3b5de2844.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，如果有步骤返回false，就会把该事件传递给上层，如果全部是false，最终会把该事件交给Activity来处理