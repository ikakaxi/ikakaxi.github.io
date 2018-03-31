title: Android 进程间的通信
author: superman
date: 2018-02-07 22:12:09
categories:
- android
tags:
- 跨进程
---

在 Android 世界里，默认的每个 APP 是一个单独的进程。其实这样的描述是不严格的，因为咱们要研究 Android 的进程间通信，肯定除了和其他的 APP 通信外，还可能和同一个 APP 下的其他进程通信。在 Java 里，每一个虚拟机是一个进程，Android 也是虚拟机的机制，你每启动一个 APP，默认会启动一个虚拟机上，一个虚拟机就是一个进程。在 APP 里，有还被运行创建另外的进程，在主进程结束后，这个进程还可以独立运行。
<!--more-->

咱们这里不讨论怎么创建进程，咱们先讨论怎么让进程通信。

Android 里有四个基础组件，Activity，Service，Broastcast，Content Provider。

####Activity
Activity 跨进程通信其实咱们很经常用，但是却忽略了。咱们通过实例化一个 Intent，然后 startActivity ，是不是把一个意图，也就是 Intent 发送出去了？那么最终被 start 的 Activity 完全可能是在另外一个进程里的啊。比如你发送微博，你在你的 APP 里，通过 Intent 把数据发送给了微博客户端，微博客户端发完微博又回到了你的 APP，这个时候你需要 startActivityForResult 和 onActivityResult 就解决了你的进程和微博客户端进程的通信。

####Service
Service 是最复杂的。一般情况下，Service 和应用同在一个进程下，并且是主线程的。所以，一般 Service 也叫本地Service。既然有本地的，就有外地的，叫做远程 Service，remote service。如果一个 Service 是 remote service 的话，那么这个 Service 就会运行在一个独立的进程里。

既然跨进程了，就需要了解一个东西，叫 AIDL ， Android Interface Definition Language。它是一个定义语言，说白了，你可以理解它是一个中间的桥梁，进程A得知道进程B的接口（也就是方法），才可以调用，传递参数，获取返回值。

####Content Provider
Android 里，使用SQLite 数据库来存储数据，一般使用 SQLiteOpenHelper 创建的数据库是私有的，不希望它被其他的应用程序读取，甚至写入的，这个机制能保护你应用的数据安全。但是有时候你又需要对外提供数据，如果说电话本，短信等等，其他的应用都可以获取到的。Content Provider 其实也是对 SQLite 的另外一种封装而已，它提供了另外一种数据的访问方式。这个时候，你就需要理解什么是 URI ，统一资源路径。URI 就相当于官话，你懂我懂大家懂，而私有数据库就相当于方言，别人一般听不懂，hacker 例外。

Content Provider 可以在不同的应用之间共享数据。

####Broadcast
广播也很好理解了，系统广播一句“狼来啦”，然后大家都知道手机快没电了。A 广播一个消息（其实也是一个Intent），然后其他的应用程序可以接收到这个广播（当然得注册监听这个广播）。

广播虽然好用，但是有些局限性，通过 Intent 来携带数据，一般不允许携带复杂的数据，特别是一些大对象。另外，广播的频率也是一个问题，小喇叭嘴太欠的话，会遭人恨的。

####Bound Services
先来一段官方的解释：A bound service is the server in a client-server interface. A bound service allows components (such as activities) to bind to the service, send requests, receive responses, and even perform interprocess communication (IPC).

一个　Bound Service　可以和其他组件（当然也包括　Service　本身了）进行交互，也包括咱们要说的跨进程通信了。

一说到　Android　的跨进程通信，大家都想到了　AIDL，其实不仅是　AIDL，包括咱们上面说的，可以通过　Intent　的发送，来进行跨进程的通信，除此之外，用　bindService 的方式，也不仅仅是 AIDL。

从创建 Bound Service 开始。一般咱们使用 bindService 来获取一个 iBinder 对象，然后通过 iBinder 对象来与 Service 进行通信。

####Extending the Binder class
最一般的方式，就是继承于 Binder（Binder implements IBinder），然后写你想要的方法，在 Service 的 onBind 的方法里返回一个 Binder 对象。在其他组件里（比如 Activity）通过 bindService 来获取这个 Binder 对象，然后就可以和 Service 进行交互了。

这种方式适用于调用 Service 的组件和 Service 在同一个进程里。也就是没有跨进程啥事。

官方文档有这么一句话：The only reason you would not create your interface this way is because your service is used by other applications or across separate processes.也就是说，除非你真的需要跨进程，不然这个方式已经够用了，不要瞎搞跨进程。

####Using a Messenger
messenger 的翻译是报信者，送信者，信使。

这种方式，需要你在 Service 里实现一个 Handler 的子类，跟普通的 Handler 一样一样的。然后还需要一个 Messenger 对象，在 onBind 方法，通过 Messenger.getBinder() 获取一个 Binder。

(Messenger底层实现原理就是AIDL，它对AIDL做了一次封装，所以使用方法会比AIDL简单，由于它的效率比较低，一次只能处理一次请求，所以不存在线程同步的问题。)

Activity 通过 bindService 获取到 Binder 后，new Messenger(binder) ，Messenger 有个 send 方法，可以把一个 Message 对象发送出去，Service 的 Handler 就会收到这个 Message。跟咱们平常使用的 Handler 和 Message 的方式基本一样，只不过它是跨进程的。

不过到目前为止，这个通信是单向的，由 Activity 向 Service 发送，如果 Service 执行完某些操作后，需要给个响应呢，这个时候，需要 Activity 也实现一个 Messenger ，然后在 send() 的时候需要把这个 Messenger 也传过去。这样在 Service 执行完任务后，也会发送一个 Message 给 Activity。这样就实现了双方的通信。

这个消息队列是在一个线程里去管理的，所以你的 Service 是线程安全的，你不需要额外的设计来保证线程安全。

**使用这种方式需要注意, Messenger发送的Message里,传递的数据要保存在message的data里,例如message.getData().xxx,如果保存在obj里会报错**

This is the simplest way to perform interprocess communication(IPC), because the Messenger queues all requests into a single thread so that you don't have to design your service to be thread-safe.

####AIDL
下面就开始 AIDL 了，这是大家很期待的，但是又是 Android 官方特别不推荐的方式。

AIDL (Android Interface Definition Language) performs all the work to decompose objects into primitives that the operating system can understand and marshall them across processes to perform IPC. The previous technique, using a Messenger, is actually based on AIDL as its underlying structure. As mentioned above, the Messenger creates a queue of all the client requests in a single thread, so the service receives requests one at a time. **If, however, you want your service to handle multiple requests simultaneously, then you can use AIDL directly.** In this case, your service must be capable of multi-threading and be built thread-safe.

看粗体的字。然后再看这段话下面还有一段话：

**Note:** Most applications **should not** use AIDL to create a bound service, because it may require multithreading capabilities and can result in a more complicated implementation. As such, AIDL is not suitable for most applications and this document does not discuss how to use it for your service. If you're certain that you need to use AIDL directly, see the AIDL document.

这段话的粗体不是我整的，是文档上的。意思就是说大部分应用不需要 AIDL 的。主要的问题出在，如果你使用 AIDL，你必须处理好多线程，并且保证线程安全。

这里不具体描述怎么使用 AIDL，大概就是需要写一个 aidl 文件，然后显示一个 Stub 的子类，其实 Stub 是 extends Binder，然后在 Service 的 onBind() 方法里返回一个 Stub 对象。其他组件还是通过 bindService 的方式获取这个 Binder，并且可以直接调用。

就是因为这里是直接调用，所以就调用者可以在任意线程里，任意时间调用，所以你需要处理好多线程，处理好线程安全。

以上就是 Android 里跟线程有关的内容。随时补充。

--- EOF --- 

转载:http://www.binkery.com/archives/489.html