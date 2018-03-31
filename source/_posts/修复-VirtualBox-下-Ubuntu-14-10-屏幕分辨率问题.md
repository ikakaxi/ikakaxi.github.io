title: 修复 VirtualBox 下 Ubuntu 14.10 屏幕分辨率问题
author: superman
tags: []
categories:
  - linux
date: 2018-02-08 09:42:00
---
在 Windows 7 下使用 VirtualBox 安装了一个 Ubuntu 14.10 后，碰到了一个 640×480 屏幕分辨率的问题。 在 ‘Display Settings' 设置界面的 ‘Detect Displays' 按钮无法点击到，因为 640x480 的分辨率的确太小了。
<!--more-->

**解决办法**
你需要安装一个 VirtualBox 的额外组件到你的 Ubuntu-Guest 中，可运行如下命令：
安装完毕需要重启虚拟机就可以。