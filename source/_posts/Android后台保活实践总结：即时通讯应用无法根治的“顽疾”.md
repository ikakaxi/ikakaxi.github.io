title: Android后台保活实践总结：即时通讯应用无法根治的“顽疾”
author: superman
date: 2018-02-07 22:31:07
categories:
- android
tags:
---

>前言
<!--more-->

Android进程和Service的保活，是困扰Android开发人员的一大顽疾。因涉及到省电和内存管理策略，各厂商基于自家的理解，在自已ROOM发布于都对标准Android发行版作为或多或少的改动，使得应用层程序在处理进程和Service保活问题上变的异常复杂，且很难兼容，因为说不定哪款手机或者哪个版本的省电策略发生改变，那么随之而来的就是进程和Service保活的差异。

在应用场景上，由于即时通讯应用（包括IM聊天应用、消息推送服务等）为了保证消息的全时、实时送达能力，必须要实现进程或Service的保活。而就这一看似不起眼的问题，实际处理起来，因为众多Android手机和Android系统版本的差异，让问题的处理充满了不确定性。

本文基于作者的实践以及相关资料的整理，总结了自已对Android进程和Service保活的理解，希望能为你的应用开发带来启发。
>概述

近期做了一个Android项目，涉及到了后台进程和Service保活的问题，网上找了很多资料，基本的保活方法都测试了。结果是：不同的手机，不同的Android版本保活效果各有差异。最难绕过的是个厂商对“后台程序保活”管理。

本文主要把相应的实践结果和保活方法进行总结。然而，因笔者可用的测试真机有限，可能存有不完整的地方，还请及时提出指正并补充，大家共同进步。
>手机QQ、微信这样的大型IM是如何解决保活问题的？

以小米手机为例，MIUI的神隐模式让很多IM和推送开发同行纠结不已：在MIUI深度休眠之后,默认会彻底断开后台应用的socket。但微信、QQ这样的应用，MIUI官方的帖子说了:给这2个应用特殊照顾。好吧，特殊照顾，普通的APP只能继续折腾了。（关于MIUI的神隐模式的讨论，见此贴的回复：
[http://www.52im.net/thread-354-1-1.html](http://www.52im.net/thread-354-1-1.html)）
>本文实践涉及到的真机型号和版本

手机：三星9100-4.1.2，三星9300-4.3，华为G730-4.1.2，华为TL00H-EMUI3.1(android 5.1.1)，魅族MX4-Flyme4.2.8.2c(android 4.4.2)。

手头能用的测试机就这些了。主要测试的service是一个最基本的service，在相应的生命周期的触发函数上做了输出。测试时都没有添加到后台保护中，注：三星的机子没找到有后台保护设置的地方。
>为什么我们的后台进程/Service会被结束掉？

我想到的是有三个方面：
1. Android系统内存回收机制；
2. 各厂商对后台程序的一个管理制度（就是允许程序后台运行那个）；
3. 第三方软件的清理(360什么的)。

其中有的后台程序保护把程序结束的同时会把程序弄成停止状态，导致无法接收广播！
>我们的保活方案有哪些？

#####1.控制onStartCommand函数的返回值：
我对这个函数的理解是：当服务被异常终止时，是否重启服务?有些文章里面在用这个做保活时，修改的是flag，在我实际测试中是无效。有效的做法是直接返回参数。另外默认的flags值为0，是START_STICKY_COMPATIBILITY。
######具体代码如下：
```
@Override
publicintonStartCommand(Intent intent,intflags,intstartId) {
    // TODO Auto-generated method stub
    return START_STICKY;
    //return super.onStartCommand(intent, flags, startId);
}
```
######测试结果：
魅族的机子无效，不管默认还是修改参数，在DDMS里面直接结束进程后都不会重启服务。其它三台机子（9100没测）：默认参数的情况下就会重启服务，return START_STICKY 会重启，return START_NOT_STICKY 不会重启。

另外：用360一键清理，或者360超级ROOT的手机优化，会杀死进程，过会儿还是会重启，只是会慢很多，大概是在排队重启服务。
#####2.在service 的onDestory里面重启服务：
这个在所有能触发onDestory的情况下都是有效的。4台测试机都测试过。直接startService 或者发送广播重启都可以 。

但能触发onDestory的情况，我不知道内存回收会不会触发。另外两种情况（2，3）是不触发的。我的测试方法是在“设置”-> 应用管理-> 正在运行-> 停止服务。（这个是正常停止服务，会触发onDestory，所以上面的onStartCommand效果不会触发。）
#####3.提高服务的优先级：
这个主要是针对第一种kill服务的情况，内存回收机制。由于这个测试比较难搭建。360清理什么把后台的进程都杀的，体现不出优先级这样的概念。我的建议是能提高就提高，下面几个实验。

######[1] 前台service：
创建一个通知使自己成为前台service
测试结果：
360一键清理和手机优化，不会把该service结束掉。

######[2] 对于后台保护：
华为G730不结束service，魅族和华为TL00H都会结束service。通知栏的保活效果还是可以的，一般的应用要求基本能满足了。

######[3] 若有root权限：
android:persistent="true",并放入system/app中
测试结果：
效果一般，三星9100上用360等清理工具杀不掉进程，在华为G730上没什么效果.（这个测试跟onStartCommand有点干扰）。
#####4.守护进程：
双服务：360会同时杀掉两个服务，分两个apk也一样。native守护进程：360不会杀掉native的守护进程，但在魅族和华为TL00H中待机一段时间后还是会被杀掉。

**结论和待续：**
1. 一般的应用添加到后台保护进程后，改个onStartCommand返回值，再加个通知。基本上大部分都能保活了。
2. 双服务我觉得没有native守护进程来的好，虽然360，微信什么的都有几个进程服务，但如果不添加到后台保活的话，效果一样不能保活，也会进入停止状态。
3. 但是.360手机助手会创建双natice守护进程做相互的看守。存活的效果会高一点点。“没添加到后台保活”一般只会杀一次，（魅族是屏幕关闭后5分钟，华为TL00H是屏幕关闭时）。

附个native守护进程：利用socket来判断服务是否存在，需要在被保活的服务里创建一个监听socket。调试信息会在SD卡目录下创建一个daemon.log。使用方法：NDKFork port包名/.服务名。具体下载链接：[http://download.csdn.net/detail/pvlking/9412815](http://download.csdn.net/detail/pvlking/9412815)
>Android应用实现保活的基本原理总结

都是通过双进程互拉以及设置进程的重要性，除非你root后，把自己的进程设置成系统进程。

**互拉的方式有很多种：**
1. 可以通过监听系统广播来把自己拉起来
1. 可以多个app相互拉
1. 可以把自己的服务搞成前台服务
1. 在service的onstart方法里返回 STATR_STICK
1. 添加Manifest文件属性值为android:persistent=“true”
1. 覆写Service的onDestroy方法
1. 服务互相绑定
1. 设置闹钟，定时唤醒
1. 自己的app在native层fork一个子进程来与主进程互拉。

综上所述，总结下来就是，目前实现Android后台保活没有完美实现，只能针对不同的机型综合使用上面列举的方法，同时祈祷自已APP的用户不要遇到奇葩机型的保活问题。
>推荐一个开源的解决方案

**源码托管地址**
源码托管地址是：[https://github.com/52im/MarsDaemon](https://github.com/52im/MarsDaemon)。

**实现原理**
**原理：
**使用Jni,在 c端 fork进程，检测Service是否存活，若Service已被杀死，则进行重启Service.  至于检测方式，可以轮询获取子进程Pid,若为1, 则说明子进程被Init进程所领养,已经成为了孤儿进程.    但是这种方式比较消耗电量，并且由于不同手机系统定制的改变，当应用被强制停止时，父进程并不一定被真正杀死，因此在一些特定机型上是无法通过此方式进行判断. 这里推荐使用liunx socket的方式进行类似心跳包的检测，并且当触发检测Service是否被杀死之前，需要判断应用是否已经被卸载，如果应用已经被卸载，则不再进行检测Service行为，直接调用exit(0)退出子进程，避免浪费系统资源和消耗电量.

**注意:
** 目前在Android 5.0系统上会把fork出来的进程放到一个进程组里， 当程序主进程挂掉后，也会把整个进程组杀掉,因此用fork的方式也无法在Android5.0及以上系统实现守护进程. 这个是系统层面的限制，当然也是为了优化整个的系统环境,守护进程给手机带来的体验并不好

**具体见源码：
**[http://androidxref.com/5.0.0_r2/ ... /ProcessRecord.java](http://androidxref.com/5.0.0_r2/xref/frameworks/base/services/core/java/com/android/server/am/ProcessRecord.java)

![](http://upload-images.jianshu.io/upload_images/545982-d765c5e38993d112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**好消息：**
Android5.0 以上目前已在 [https://github.com/52im/MarsDaemon](https://github.com/52im/MarsDaemon) 中被黑科技攻克，部分机型可能无法起到作用，但思路很值得借鉴，代码结构也不错, 具体方案请见源码哦。

原文:http://www.52im.net/thread-429-1-1.html
因为原文已经无法打开,所以将内容复制到了这里

---------------
在android5.0及以上,谷歌出了一个JobService,经测试可以在原生系统里定时执行任务,所以在三星这种机器上,可以用JobService拉起自己的Service