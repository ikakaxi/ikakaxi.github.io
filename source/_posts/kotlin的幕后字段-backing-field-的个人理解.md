title: kotlin的幕后字段(backing field)的个人理解
author: superman
tags: []
categories:
  - kotlin
date: 2018-02-08 09:38:00
---
有关kotlin的幕后字段,在 https://zhuanlan.zhihu.com/p/27493630 这里讲得很好,我总结了一下,如果让一个kotlin的类属性有幕后字段,需要满足以下条件
<!--more-->

- 不管是var或者val字段,只要有默认的setter/getter方法,就有幕后字段
- 如果没有默认的setter/getter方法,只要在setter/getter方法里使用了field,也会生成幕后字段
- 接口不能有幕后字段
- 类的幕后字段必须初始化,或者显式声明需要延迟初始化,如果该字段没有默认的setter/getter方法说明它并不是幕后字段