title: 用一张图告诉你Android中View的measure过程
author: liuhc
tags:
  - View
categories:
  - android
date: 2018-03-31 23:41:00
---
[![绘制View流程-onMeasure.png](https://upload-images.jianshu.io/upload_images/545982-1a001be2690f4f83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://upload-images.jianshu.io/upload_images/545982-1a001be2690f4f83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 其中MeasureSpec类的说明：

MeasureSpec.EXACTLY(完全)：LayoutParams.MATCH_PARENT或确切的数值。父控件为子View指定确切大小，希望子View完全按照自己给定的尺寸来处理，这时的MeasureSpec一般是父控件根据自身的MeasureSpec跟子View的布局参数来确定的。一般这种情况下size>0，有个确定值。

MeasureSpec.AT\_MOST(至多)：LayoutParams.WRAP\_CONTENT 父控件为子元素指定最大参考尺寸，希望子View的尺寸不要超过这个尺寸。这种模式也是父控件根据自身的MeasureSpec跟子View的布局参数来确定的。

MeasureSpec.UNSPECIFIED(未指定)：父容器不对子View有任何限制，要多大给多大，多见于ListView、GridView、ScrollView。这种MeasureSpec一般是由父控件自身的特性决定的。比如ScrollView，它的子View可以随意设置大小，无论多高，都能滚动显示。

---

#### ViewGroup的`getChildMeasureSpec(int spec, int padding, int childDimension)`方法说明：

1.  不管该ViewGroup的测量模式是什么，只要childDimension>=0，返回的子View的绘制大小就是childDimension，测量模式就是MeasureSpec.EXACTLY

2.  如果该ViewGroup的测量模式是MeasureSpec.EXACTLY

    1.  如果childDimension == LayoutParams.MATCH_PARENT，返回的子View大小为父View大小，模式为EXACTLY
    2.  如果childDimension == LayoutParams.WRAP\_CONTENT，返回的子View决定自己的大小，但最大不能超过父View大小，模式为AT\_MOST
3.  如果该ViewGroup的测量模式是MeasureSpec.AT\_MOST，childDimension == LayoutParams.MATCH\_PARENT或者childDimension == LayoutParams.WRAP\_CONTENT，返回的子View决定自己的大小，但最大不能超过父View大小，模式为AT\_MOST

4.  如果该ViewGroup的测量模式是MeasureSpec.UNSPECIFIED，childDimension == LayoutParams.MATCH\_PARENT或者childDimension == LayoutParams.WRAP\_CONTENT，返回的子View的建议大小是0（小于6.0）或者ViewGroup的大小（6.0和以上），测量模式是MeasureSpec.UNSPECIFIED

---

#### 顶层View的MeasureSpec是谁指定

传递给子View的MeasureSpec是父容器根据自己的MeasureSpec及子View的布局参数所确定的，那么根MeasureSpec是谁创建的呢？我们用最常用的两种Window来解释一下，Activity与Dialog，DecorView是Activity的根布局，传递给DecorView的MeasureSpec是系统根据Activity或者Dialog的Theme来确定的，也就是说，最初的MeasureSpec是直接根据Window的属性构建的，一般对于Activity来说，根MeasureSpec是EXACTLY+屏幕尺寸，对于Dialog来说，如果不做特殊设定会采用AT_MOST+屏幕尺寸。这里牵扯到WindowManagerService跟ActivityManagerService

参考：

https://www.jianshu.com/p/1dab927b2f36
https://www.jianshu.com/p/d16ec64181f2