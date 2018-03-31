title: linux常用软件安装
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:48:00
---
####1.mongodb的conf文件
port=27017 #【代表端口号，如果不指定则默认为   27017   】 
dbpath= /home/username/Soft/mongoDBFiles  #【数据库路径】 
logpath= /home/username/Soft/mongoDBFiles/mongodb.log  #【日志路径】 
logappend=true  #【日志文件自动累加，而不是覆盖】
推荐用配置文件启动mongodb服务，配置文件启动方式:mongod -f [conf文件]

####2.nodejs
官网下载nodejs压缩包后解压，然后用ln -s命令软链接node和npm命令到/usr/local/bin或者/usr/bin目录下

####3.md文件转html
要将markdown文件转换成html文件，可以用discount或python-markdown软件包提供的markdown工具。
$ sudo apt-get install discount

用discount提供的markdown工具转换：
$ markdown -o Release-Notes.html Release-Notes.md

python-markdown用过之后发现生成的html点击链接无法定位描点，但是discount可以，所以不提供python-markdown的使用方式