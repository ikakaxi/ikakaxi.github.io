title: windows下android studio在git push时失败的解决方法
author: superman
tags: []
categories:
  - git
date: 2018-02-08 09:54:00
---
网上查了很多资料,今天无意中看到一个git命令大全,找到了解决办法,只要在你的用户目录下的.ssh文件夹里创建pub公钥然后填到你的git服务器就可以了,git有个方便的工具不用自己敲命令,在git项目里打git citool命令,然后运行
![QQ截图20161220094526.png](http://upload-images.jianshu.io/upload_images/545982-5fc34fd8c1765436.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后生成ssh就可以了