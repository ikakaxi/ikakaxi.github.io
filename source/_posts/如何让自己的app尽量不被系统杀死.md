title: 如何让自己的app尽量不被系统杀死
author: superman
date: 2018-02-07 18:14:40
categories:
- android
tags:
---

1.
在Service中重写下面的方法，这个方法有三个返回值， START_STICKY是service被kill掉后自动重写创建
<!--more-->
```
@Override 
public int onStartCommand(Intent intent, int flags, int startId){ 
  return START_STICKY;
}
```
2.
在Service的onDestroy()中重启Service. 
```
@Override
public void onDestroy() { 
　　super.onDestroy();
　　Intent localIntent = new Intent(); 
　　localIntent.setClass(this, MyService.class); //销毁时重新启动Service 
　　this.startService(localIntent); 
}
```
3.
在mf.xml的application的节点中添加android:persistent="true"
这个方法,必须要system app,所以这个基本没用

4.
fork进程的方式做守护,在5.0已经不行了

5.
在Service里创建一个静态内部Service,然后启动外部Service和静态内部Service为前台服务,然后停止这个静态内部Service,这样通知栏虽然不显示通知但是外部Service仍然是前台Service,这是利用android的一个bug,不过高版本应该也修复了,参考

http://zhoujianghua.com/2015/07/28/black_technology_in_alipay/
(最下面有个demo示例)

http://blog.csdn.net/lhd201006/article/details/50920464


> 保活这个东西,我查了很多很多的资料了,在现在这个环境,我觉得保活的控制权各大厂商已经交给了用户,比如在华为EMUI系统和MIUI系统,锁屏后默认都杀死应用,想要应用不被杀死,就得自己开启锁屏不被杀或者开启自启动选项,如果做推送,就用手机厂商自己的推送服务,我个人也觉得这种方式很好,否则,你可以想一下android当初刚出来的时候,各种软件有多么的流氓,就知道现在为什么把控制权交给用户了