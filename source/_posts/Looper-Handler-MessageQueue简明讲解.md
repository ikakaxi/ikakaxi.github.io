title: 'Looper,Handler,MessageQueue简明讲解'
author: liuhc
tags:
  - handler
categories:
  - android
date: 2018-03-31 23:17:00
---
这个Looper，Handler，MessageQueue机制其实很简单，网上也有很多教程，有很多讲的很详细，废话不多说，我按照我的理解来简单的讲解一下，详细的可以在网上找教程

1.  Looper类用ThreadLocal来保证一个线程里面一个Looper，在prepare方法里，调用ThreadLocal.get()来获取当前线程的Looper，如果没有的话就set一个Looper对象进去，在Looper的构造函数里会创建一个MessageQueue对象

*   在Looper类的loop方法里，调用ThreadLocal.get()获取当前线程的Looper，然后获取当前Looper的MessageQueue，然后用一个死循环来一直从里面取Message，取到以后就调用Message的target——也就是Handler，来执行Handler的dispatchMessage方法，这个target哪里来的呢，可以查看一下Handler的sendMessage的那几个方法，它们最终都会调用Handler的enqueueMessage方法，在这个方法里把Message的target设置为了Handler自己，然后把该Message加入到了MessageQueue，那么MessageQueue又是哪里来的呢，可以看Handler的几个构造函数，最终都会调用`Handler(Callback callback, boolean async)`这个构造函数，在这个构造函数里调用`Looper.myLooper()`获取了当前线程的Looper，然后从这个Looper获取了MessageQueue

讲完了，就上面这几个步骤，至于新创建的线程想直接操作控件需要先执行Looper.prepare方法再怎样怎样，可以自己思考一下原因，看一下我上面说的几个步骤就明白为什么了

这个Looper机制，java层很简单，主要的地方在native层，为什么loop方法是一个死循环而程序不会卡住呢？这就涉及到linux的pipe（管道）方面的知识了

> 关于管道，简单来说，管道就是一个文件。  
> 在管道的两端，分别是两个打开文件文件描述符，这两个打开文件描述符都是对应同一个文件，其中一个是用来读的，别一个是用来写的。  
> 一般的使用方式就是，一个线程通过读文件描述符中来读管道的内容，当管道没有内容时，这个线程就会进入等待状态，而另外一个线程通过写文件描述符来向管道中写入内容，写入内容的时候，如果另一端正有线程正在等待管道中的内容，那么这个线程就会被唤醒。这个等待和唤醒的操作是如何进行的呢，这就要借助Linux系统中的epoll机制了。 Linux系统中的epoll机制为处理大批量句柄而作了改进的poll，是Linux下多路复用IO接口select/poll的增强版本，它能显著减少程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

参考文章：

[https://www.zhihu.com/question/34652589](https://www.zhihu.com/question/34652589)  
[http://wangkuiwu.github.io/2014/08/26/MessageQueue/](http://wangkuiwu.github.io/2014/08/26/MessageQueue/)  
[http://www.bijishequ.com/detail/381606](http://www.bijishequ.com/detail/381606)