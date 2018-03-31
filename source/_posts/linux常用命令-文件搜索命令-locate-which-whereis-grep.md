title: 'linux常用命令-文件搜索命令-locate,which,whereis,grep'
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:48:00
---
locate 目录或文件名 
<!--more-->
　　-i 查找的时候不区分大小写
这个类似everything,速度比find快很多,因为这个命令搜索的是它维护的文件资料库,文件资料库是var/lib/mlocate/mlocate.db,如果文件没有包含在它的文件资料库,那么是找不到这个文件的,这个时候需要用updatedb命令来更新文件资料库,但是,/tmp这个目录,是不会被文件资料库收录的,即使执行了updatedb也找不到tmp下面的文件
 
which 搜索命令所在目录及别名
　　例:which ls
whereis 搜索命令所在目录及帮助文档路径
　　例:whereis ls
　　注意,这个命令查到的帮助文档的路径中,1代表命令的帮助,5代表文档的帮助,例如whereis passwd中就有man1和man5这两种帮助文档
grep 在文件内容中搜寻子串匹配的行为并输出
　　-i 不区分大小写
　　-v 排除指定字串
　　例:grep mysql /root/install.log
　　　 grep -v ^# /etc/inittab　　把#开头的行排除