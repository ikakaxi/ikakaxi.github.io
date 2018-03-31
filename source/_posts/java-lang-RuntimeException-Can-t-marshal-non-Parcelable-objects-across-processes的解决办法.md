---
title: >-
  java.lang.RuntimeException: Can't marshal non-Parcelable objects across
  processes的解决办法
date: 2018-02-08 09:22:56
categories:
- android
tags:
- 异常
---

必须用Bundle传递常规类型数据，否则会报错：
<!--more-->

java.lang.RuntimeException: Can't marshal non-Parcelable objects across processes.

因为Binder事务传递的数据被称为包裹(Parcel)，必须实现Parcelable接口，否则无法在两个应用之间进行通信。之所以用Bundle传递是因为该类实现了Parcelable接口。当然如果要传递类也必须实现该接口。