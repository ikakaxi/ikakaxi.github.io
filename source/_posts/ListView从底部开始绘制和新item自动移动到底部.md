title: ListView从底部开始绘制和新item自动移动到底部
author: superman
date: 2018-02-07 18:17:00
categories:
- android
tags:
- View
- ListView
---

今天做聊天,才知道ListView的2个属性,android:stackFromBottom和android:transcriptMode

android:stackFromBottom
<!--more-->

    true的时候,ListView内容就从底部开始显示,如果只有一条内容这条内容就在列表的最底部,默认是false

android:transcriptMode

    设置列表的transcriptMode模式，该模式指定列表添加新的选项的时候，是否自动滑动到底部，显示新的选项。 

    disabled：取消transcriptMode模式，默认的 
    normal：当接受到数据集合改变的通知，并且仅仅当最后一个选项已经显示在屏幕的时候，自动滑动到底部。 
    alwaysScroll：无论当前列表显示什么选项，列表将会自动滑动到底部显示最新的选项。 