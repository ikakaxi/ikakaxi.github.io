title: source tree 配置 外部差异比对工具为beyond compare
author: superman
tags: []
categories:
  - 其他
date: 2018-02-08 09:54:00
---
1、首先，安装好beyond compare后，创建一个快捷方式 
```
ln -s /Applications/Beyond\ Compare.app/Contents/MacOS/bcomp /usr/local/bin/
```

2、打开source tree，在系统偏好设置里面，找到diff，按照下面的进行配置：
```
Visual Diff Tool: Other
Diff Command:/usr/local/bin/bcomp
Parameters:-ro $LOCAL $REMOTE
Merge Tool: Other
Merge Command:/usr/local/bin/bcomp
Parameters:$LOCAL $REMOTE $BASE $MERGED
```