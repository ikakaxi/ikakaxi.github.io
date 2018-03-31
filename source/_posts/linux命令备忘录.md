title: linux命令备忘录
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:49:00
---
####1.netstat常用命令
-a：查看所有端口的占用
-t：只列出 TCP 或 UDP 协议的连接
-u：列出 UDP 协议的连接
-n：禁用反向域名解析，加快查询速度
-l：只列出监听中的连接
-p：获取进程名、进程号以及用户 ID
-ep：可以同时查看进程名和用户名

####2.kill -9 pid
杀死pid这个进程

####3.刷新dns
sudo /etc/init.d/networking restart 