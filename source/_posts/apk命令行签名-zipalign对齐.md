title: 'apk命令行签名,zipalign对齐'
author: superman
date: 2018-02-07 22:26:13
categories:
- android
tags:
- 签名
---

#jarsigner
<!--more-->
#####使用示例:
jarsigner -verbose -keystore ~/Documents/WorkSpace/signkey.keystore -signedjar ./要生成的文件 ./源文件 签名文件的别名

给apk包签名的方式有很多种，我们推荐您使用JDK自带的jarsigner工具来完成签名。

#####jarsigner的命令格式

jarsigner -verbose -keystore [您的私钥存放路径] -signedjar [签名后文件存放路径] [未签名的文件路径] [签名文件的别名]

#####jarsigner的参数说明

-keystore 参数指定您的私钥的绝对路径，例如：c:\mykeystore

-signedjar 参数指定签名后apk文件存放绝对的路径，例如 c:\signed.apk

[未签名的文件路径] 指定要签名apk文件的绝对路径，也就是您从我们这里下载到的，例如 c:\unsigned.apk

[您的证书名称] 是指您创建密钥时，您设置的别名,也就是alias

#zipalign对齐
zipalign -v 4 源文件 要生成的文件
#####检查apk有没有zipalign对齐:
zipalign -c -v 4 被检查的apk文件

#先签名再对齐,否则先对齐再签名会破坏对齐