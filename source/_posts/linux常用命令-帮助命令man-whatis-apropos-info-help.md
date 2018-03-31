title: 'linux常用命令-帮助命令man,whatis,apropos,info,help'
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:48:00
---
man 命令

<!--more-->
　　man 配置文件,注意这里只需要写文件名称就可以了,不能写文件的绝对路径

　　man既可以查看命令的帮助信息也可以查看配置文件的帮助信息,如果内容太多,可以输入"/内容"查找,按n继续查找下一页

　　man passwd这个命令,有2个帮助文档,分别是passwd命令的帮助文档和passwd配置文件的帮助文档,在whereis命令里学习到,1代表命令的帮助,5代表文档的帮助,

　　　　所以如果要看配置文件的帮助文档,需要输入man 5 passwd

whatis 命令

　　可以查看某命令的简短的帮助信息

apropos 配置文件,注意这里也是只需要写文件名称就可以了,不能写文件的绝对路径

　　可以查看某配置文件的简短的帮助信息

命令 --help

　　列出该命令的常见选项

info 命令或者配置文件

　　类似man,可以显示当前查看文件的进度

help

　　可以查看shell内置命令的帮助,shell内置命令用which或者whereis是找不到命令所在路径的

　　例:help cd