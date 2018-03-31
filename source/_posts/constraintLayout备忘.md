title: constraintLayout备忘
author: superman
date: 2018-02-07 22:38:10
categories:
- android
tags:
- View
---

#####1. 相对位置
<!--more-->
```
layout_constraintLeft_toLeftOf
layout_constraintLeft_toRightOf
layout_constraintRight_toLeftOf
layout_constraintRight_toRightOf
layout_constraintTop_toTopOf
layout_constraintTop_toBottomOf
layout_constraintBottom_toTopOf
layout_constraintBottom_toBottomOf
layout_constraintBaseline_toBaselineOf
layout_constraintStart_toEndOf
layout_constraintStart_toStartOf
layout_constraintEnd_toStartOf
layout_constraintEnd_toEndOf
```
以layout_constraintLeft_toLeftOf 为例，其中 layout_ 部分是固定格式，主要的信息包含在下面两部分：
constraintXXX：指定当前控件需要设置约束的属性部分。如 constraintLeft 表示对当前控件的 左边 进行约束设置。
toXXXOf：其指定的内容是作为当前控件设置约束需要依赖的控件或父容器（可以理解为设置约束的参照物）。并通过 XXX 指定被依赖对象用于参考的属性。如 toLeftOf="parent" ：表示当前控件相对于父容器的左边进行约束设置。

#####2. 边距
   - 普通边距属性
android:layout_marginStart
android:layout_marginEnd
android:layout_marginLeft
android:layout_marginTop
android:layout_marginRight
android:layout_marginBottom

   - 被依赖控件GONE之后的边距属性
layout_goneMarginStart
layout_goneMarginEnd
layout_goneMarginLeft
layout_goneMarginTop
layout_goneMarginRight
layout_goneMarginBottom

#####3. 居中
   - 水平居中：相对一个控件或者父容器左右对齐
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent
   - 垂直居中：相对一个控件或者父容器左右对齐
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintBottom_toBottomOf="parent"

#####4. 偏移
layout_constraintHorizontal_bias // 水平偏移
layout_constraintVertical_bias   // 垂直偏移

#####5. 可见性
可见性这个属性大家应该很熟悉，但是约束布局的可见性属性和其它布局相比，存在以下区别：
   - 当控件设为GONE时，被认为尺寸为0。可以理解为布局上的一个点。
   - 若GONE的控件对其它控件有约束，则约束保留并生效，但所有的边距（margin）会清零。

#####6. 尺寸

几种设置方式：

   - 设置固定尺寸，如123dp
   - 使用 wrap_content ，根据内容计算合适大小
   - match_parent ，填充满父布局，此时设置的约束都不生效了。（早之前的约束布局版本貌似不允许在其子view中使用match_parent属性，但是我写文章的时候发现也是可以用上去的）
   - 设置0dp，相当于MATCH_CONSTRAINT属性，基于约束最终确定大小

MATH_CONSTRAINT

- layout_constraintWidth_min 和 layout_constraintHeight_min ：设置最小值
- layout_constraintWidth_max 和 layout_constraintHeight_max ：设置最大值
- layout_constraintWidth_percent 和 layout_constraintHeight_percent ：设置控件相对于父容器的百分比大小（1.1.0开始支持）。使用之前需要先设置为百分比模式，然后设置设置宽高值为0～1之间。

设置为百分比模式的属性：
```
app:layout_constraintWidth_default="percent"
app:layout_constraintHeight_default="percent"
```
- 强制约束
当一个控件设为wrap_content时，再添加约束尺寸是不起效果的。如需生效，需要设置如下属性为true:
```
app:layout_constrainedWidth=”true|false”     
app:layout_constrainedHeight=”true|false”
```

#####7. 比例
控件可以定义两个尺寸之间的比例，目前支持宽高比。
前提条件是至少有一个尺寸设置为0dp，然后通过 layout_constraintDimentionRatio 属性设置宽高比。设置方式有以下几种：
- 直接设置一个float值，表示宽高比
- 以” width：height”形式设置
- 通过设置前缀W或H，指定一边相对于另一边的尺寸，如”H, 16:9”，高比宽为16:9

如果宽高都设置为0dp，也可以用ratio设置。这种情况下控件会在满足比例
约束的条件下，尽可能填满父布局。

#####8. 链
链这个概念是约束布局新提出的，它提供了在一个维度（水平或者垂直），管理一组控件的方式。

#####9. Guideline
可以理解为布局辅助线，用于布局辅助，不在设备上显示。
有垂直和水平两个方向（android:orientation=“vertical/horizontal”）
- 垂直：宽度为0，高度等于父容器
- 水平：高度为0，宽度等于父容器

有三种放置Guideline的方式：
- 给定距离左边或顶部一个固定距离（layout_constraintGuide_begin）
- 给定距离右边或底部一个固定距离（layout_constraintGuide_end）
- 给定宽高一个百分比距离（layout_constraintGuide_percent）

参考:http://www.qingpingshan.com/rjbc/az/359115.html