title: 保存代码到githubPages
author: superman
tags: []
categories:
  - 建站
date: 2018-02-10 11:09:00
---
我的githubPages放到了master分支,所以我新建了一个source分支(名字随意),然后把本地的hexo的文件们都提交到了这个分支,需要注意的是themes下面的文件夹,都是git clone下来的,所以在刚才的source分支里并不存在这些文件,我自己暂时没有更新这些theme的需求,所以我把我自己用的themes/next下面的.git文件夹和.gitignore文件夹删除了,然后再提交,在source分之下就有这些文件夹了,以后换机器直接切换到这个分支就可以继续写文章了