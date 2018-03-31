title: linux常用命令-压缩解压命令
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:48:00
---
gzip [文件]
　　压缩后文件格式 .gz,这个命令只能压缩文件,不能压缩目录.并且这个命令压缩后不保留源文件
<!--more-->
gunzip [文件] 或者 gzip -d [文件]
　　解压缩.gz的压缩文件
tar [-zcvf] [压缩后文件名] [目录]
　　打包目录
　　压缩后文件格式 .tar.gz
　　-c 打包
　　-x 解压缩
　　-v 显示详细信息
　　-f 指定文件名
　　-z 打包同时压缩
tar [-zxvf] [解压缩文件名] [目录]
 
zip [压缩后文件名] [文件]:压缩文件,这个命令没有gzip压缩比例高
　　[-r] [压缩后文件名] [目录]:压缩目录
　　压缩后文件格式 .zip
　　注意:如果压缩目录的时候没有加-r,那么压缩后的文件只有根目录文件名,而没有子目录和里面的内容
 
bzip2 [-k] [压缩文件] 压缩比例最高
　　-k 压缩后保留源文件
　　配合tar使用示例: tar -cjf testfile.tar.bz2 testfile,把z换成了f, 请留意,在 f 之后要立即接文件或者目录名,不要再加参数,否则会出错,例如tar -cfj testfile.tar.bz2 testfile是错误的
bunzip2 [-k] [压缩文件]
　　-k 压缩后保留源文件
　　配合tar使用示例:tar -xjf testfile.tar.bz2, 把z换成了f,请留意,在 f 之后要立即接文件或者目录名,不要再加参数,否则会出错,例如tar -cfj testfile.tar.bz2 testfile是错误的