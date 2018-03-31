title: linux常用命令-文件搜索命令-find
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:47:00
---
find [目录] [选项] 文件名或者正则表达式

<!--more-->
　　-name 根据文件名搜索

　　-iname 搜索文件名的时候忽略大小写

　　　　例:find /etc -name init

　　    　　find /etc -iname init*

　　-size +n:大于n数据块 -n:小于n数据块 n:等于n数据块

　　　　　　1个数据块等于512字节,也就是0.5k,换算的时候,需要当前kb乘以2就是n的大小

　　　　例: find / -size +204800 搜索大于100M的文件,100M=102400kb=204800数据块

　　-user 查找所有者为user的文件

　　　　例:find /home -user superman

　　-group 查找所有者为group组的文件

　　　　例:find /home -group work

 

　　-amin 根据访问时间查找

 

　　-cmin 根据文件属性查找
　　　　例:find /etc -cmin -5 在etc下查找5分钟内被修改过属性的文件和目录

　　　　　　-n:n分钟之内　　+n:超过n分钟

 

　　-mmin 根据文件内容查找

 

　　-a 两个条件同时满足

　　-o 两个条件满足一个即可

 

　　-type f 文件

　　　　　d 目录

　　　　　l 软链接文件

　　-inum 根据i节点查找

　　-exec或者/ok 命令 {} \; 对搜索结果执行某命令,区别是exec不会询问,而ok会每个都询问,用y或者n确认.

　　　　　　　　　　　{} \;是固定的格式

　　　　例:find /etc -name inittab -exec ls -l {} \;