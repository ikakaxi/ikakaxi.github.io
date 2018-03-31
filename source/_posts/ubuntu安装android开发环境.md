title: ubuntu安装android开发环境
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:43:00
---
**1.安装oracle-jdk**
打开终端，使用下面的命令：
java -version
如果你看到像下面的输出，这就意味着你并没有安装过Java:
<!--more-->

The program ‘java’ can be found in the following packages:
\* default-jre
\* gcj-4.6-jre-headless
\* openjdk-6-jre-headless
\* gcj-4.5-jre-headless
\* openjdk-7-jre-headless
Try: sudo apt-get install
 
使用下面的命令安装，只需一些时间，它就会下载许多的文件，所及你要确保你的网络环境良好：
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
sudo apt-get install oracle-java8-set-default

**2.安装genymotion**
安装genymotion需要先安装virtualbox，命令如下：
sudo apt-get install virtualbox
然后下载genymotion，chmod +x geny.....bin 增加权限后执行，会解压缩，然后双击即可打开