title: IDEA的一个bug
author: superman
tags:
  - IDE
categories:
  - kotlin
date: 2018-02-08 09:33:00
---
今天测试一段代码,代码如下
<!--more-->
```
class Person14(var age: Int)

fun main(args: Array<String>) {
    val p1 = Person14(1)
    val p2 = Person14(2)
    val p14age = p1::age
    val p14Age = Person14::age//TODO 这句话有异常,是kotlin的问题?
    println(p14age)
    println(p14Age)
}
```

发现会报
```
Exception in thread "main" java.lang.NoClassDefFoundError: Kt14Kt$main$p14age$1 (wrong name: Kt14Kt$main$p14Age$1)
```
异常
经测试发现是IDEA的问题,因为在https://glot.io/ 输入以上代码却没有问题