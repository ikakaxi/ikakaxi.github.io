title: Activity常用的4种载入方式
author: superman
date: 2018-02-07 22:33:21
categories:
- android
tags:
- activity
---

#一:xml配置
<!--more-->
###1.android:launchMode="standard"
这是默认的方式,每次启动一个Activity都会重新创建一个新的实例,不管这个实例是否已经存在.在这种模式下,谁启动了这个Activity,那么这个Activity就运行在启动它的那个Activity所在的栈中
当我们用ApplicationContext去启动standard模式的Activity的时候会报错,因为standard模式的Activity默认会进入启动它的Activity所属的任务栈中,但是由于非Activity类型的Context(如ApplicationContext)并没有所谓的任务栈,所以这就有问题了.解决这个问题的方法是为待启动的Activity指定FLAG_ACTIVITY_NEW_TASK标记位,这样启动的时候就会为它创建一个新的任务栈

###2.android:launchMode="singleTop"
栈顶复用模式,在这种模式下,如果新Activity已经位于任务栈的栈顶,那么此Activity不会被重新创建,同时它的onNewIntent方法会被回调,调用此方法的参数我们可以取出当前请求的信息.需要注意的是,这个Activity的onCreate,onStart不会被系统调用,因为它并没有发生改变.如果新Activity的实例已存在但不是位于栈顶,那么新Activity仍然会重新创建

###3.android:launchMode="singleTask"
栈内复用模式,这是一种单实例模式,在这种模式下,只要Activity在一个栈中存在,那么多次启动此Activity都不会重新创建实例,和singleTop一样,系统也会回调其onNewIntent.如果被启动的Activity不在栈顶,那么就会把它上面的Activity出栈

###4.android:launchMode="singleInstance"
加强的栈内复用模式,它除了具有singleTask模式的所有特性外,还加强了一点,那就是具有此种模式的Activity只能单独的位于一个任务栈中,换句话说,比如Activity A是singleInstance模式,当A启动后,系统会为它创建一个新的任务栈,然后A独自在这个新的任务栈中,由于栈内复用的特性,后续的请求均不会创建新的Activity,除非这个独特的任务栈被系统销毁了

#二.设置Intent的Flag标志
###1.FLAG_ACTIVITY_NEW_TASK
如果当前app还没有任务栈会创建一个任务栈然后把Activity放进去
如果已经有了就用已存在的任务栈
如果启动Activity的Context没有任务栈并且没有指定此标记位会报错,例如用ApplicationContext启动Activity

###2.FLAG_ACTIVITY_CLEAR_TOP或者FLAG_ACTIVITY_NEW_TASK  | FLAG_ACTIVITY_CLEAR_TOP
和singleTask效果是一样的

    设被启动的Activity的名字为A:
    当A已存在栈中的时候
    1.当A的启动模式是standard的时候,A和它之上的Activity都会出栈然后创建新的A并放入栈中
    2.当A的启动模式是其它3种的时候(singleTask,singleInstance,singleTop),A之上的Activity都会出栈然后调用已存在的A的onNewIntent方法
    当A不存在栈中的时候用FLAG_ACTIVITY_NEW_TASK |FLAG_ACTIVITY_CLEAR_TOP会创建一个A,如果不加FLAG_ACTIVITY_NEW_TASK会报错

###3.FLAG_ACTIVITY_SIGNLE_TOP
和singleTop效果是一样的

###4.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS
具有这个标记的Activity不会出现在历史Activity的列表中,当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用.它等同于在XML中指定Activity的属性android:excludeFromRecents="true"