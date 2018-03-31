title: ubuntu安装python一些安装包
author: superman
tags:
  - python
categories:
  - linux
date: 2018-02-08 09:42:00
---
sudo apt-get install python-pip
sudo pip install distribute
sudo pip install nose
sudo pip install virtualenv
sudo pip install lpthw.web
windows下面去掉sudo即可
<!--more-->
 
**windows安装pip**
　　pip 是一个安装和管理 Python 包的工具 , 是 easy_install 的一个替换品，使用 pip 使安装、更新和卸载 python 包变得简单。
　　第一步：[https://pypi.python.org/pypi/pip](https://pypi.python.org/pypi/pip)下载，运行python setup.py install即完成安装
　　第二步：设windows环境变量，将C:\Python27\Scripts添加至path，重启cmd窗口
　　第三步：pip使用，如最基本的pip install MODELNAME
 
**windows安装pyQt4**
http://www.riverbankcomputing.com/software/pyqt/download
 
**Qt开发IDE**
https://www.qt.io/zh-hans/download-open-source/
 
**windows安装wxPython界面开发工具**
http://boa-constructor.sourceforge.net/Download.html
 
**windows安装Qt设计师**
安装完pyQt自动安装了好像
 
**把python文件做成exe文件**
　　安装py2exe:在这里下载安装程序安装:http://sourceforge.net/projects/py2exe/files/py2exe/0.6.9/
　　或者cx-freeze:
　　http://sourceforge.net/projects/cx-freeze/files/4.3.2/  
**poster包可以上传文件到服务器**
　　pip install poster