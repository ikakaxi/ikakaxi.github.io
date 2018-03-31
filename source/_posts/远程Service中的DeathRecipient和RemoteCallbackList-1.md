title: 远程Service中的DeathRecipient和RemoteCallbackList
author: superman
date: 2018-02-07 22:12:41
categories:
- android
tags:
- Service
---

DeathRecipient:用这个的原因是担心客户端异常销毁时,服务器收不到消息,造成资源浪费等异常
RemoteCallbackList:同样的,我们在服务端通知客户端消息的时候,也担心 服务端会异常销毁,导致客户端收不到消息

这两个类的使用demo在这里:http://www.cnblogs.com/punkisnotdead/p/5158016.html