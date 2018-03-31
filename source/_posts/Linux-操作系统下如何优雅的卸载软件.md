title: Linux 操作系统下如何优雅的卸载软件
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:49:00
---
输入如下命令：

sudo apt-get autoremove --purge 要卸载的软件名字

我们来讲解一下这个命令

sudo ———— 获取 root 权限

apt-get ——— 执行安装卸载功能的软件

autoremove — 告诉 apt-get 我们所要做的操作是移除软件

--purge ——— 注意这前面是两个短划线，这个参数是告诉他们要完整的干净的彻底的移除

此方法 Ubuntu系、Debian系 系统可用