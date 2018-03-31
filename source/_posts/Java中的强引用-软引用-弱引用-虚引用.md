title: 'Java中的强引用,软引用,弱引用,虚引用'
author: superman
tags: []
categories:
  - java
date: 2018-02-08 09:51:00
---
####1.强引用
这个就不用说了,基本上写程序都是强引用,比如
```
Object obj = new Object();
```
####2. 软引用
软引用的特点是内存足够的时候,gc的时候不会回收它,只有内存不足的时候才会回收软引用的对象

####3.弱引用
在垃圾回收器扫描的时候,发现弱引用会把它放到ReferenceQueue中,等下次gc的时候会回收它
<!--more-->

系统为我们提供了WeakHashMap，和HashMap类似，只是其key使用了weak reference。如果WeakHashMap的某个key被垃圾回收器回收，那么entity也会自动被remove。

由于WeakReference被GC回收的可能性较大，因此，在使用它之前，你需要通过weakObj.get()去判断目的对象引用是否已经被回收.

> Reference queque

> 一旦WeakReference.get()返回null，它指向的对象就会被垃圾回收，那么WeakReference对象就没有用了，意味着你应该进行一些清理。比如在WeakHashMap中要把回收过的key从Map中删除掉，避免无用的的weakReference不断增长。
ReferenceQueue可以让你很容易地跟踪dead references。WeakReference类的构造函数有一个ReferenceQueue参数，当指向的对象被垃圾回收时，会把WeakReference对象放到ReferenceQueue中。这样，遍历ReferenceQueue可以得到所有回收过的WeakReference。

####4.虚引用
和soft，weak Reference区别较大，它的get()方法总是返回null。这意味着你只能用PhantomReference本身，而得不到它指向的对象。当WeakReference指向的对象变得弱可达(weakly reachable）时会立即被放到ReferenceQueue中，这在finalization、garbage collection之前发生。理论上，你可以在finalize()方法中使对象“复活”（使一个强引用指向它就行了，gc不会回收它）。但没法复活PhantomReference指向的对象。而PhantomReference是在garbage collection之后被放到ReferenceQueue中的，没法复活。
虚引用适合用来查看对象什么时候被回收,优化内存的时候使用

---

以上是个人在网上学习的时候总结的东西,有错误的地方请指正
