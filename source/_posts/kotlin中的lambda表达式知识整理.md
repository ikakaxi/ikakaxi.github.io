title: kotlin中的lambda表达式知识整理
author: superman
tags:
  - lambda
categories:
  - kotlin
date: 2018-02-08 09:40:00
---
#####如何定义lambda表达式(将表达式赋值给一个常量或变量):
<!--more-->
- ######如果有小括号
1. 如果需要参数(没有参数可以写()),就在小括号里写明参数类型,参数名可以省略,然后小括号后面加上->{},
2. ->后面如果没有返回值就写Unit,
3. 然后在大括号{}里面写上具体的方法实现

       1. 在大括号{}的方法实现里,如果只有一个参数,可以用it指定而不需要再写x->...这种代码,例如
       val add1: (Int) -> Int = { it + 1 }
       2. 如果方法实现里有多个参数,则需要x:Int,y:Int->...这种方式指明->后面的自定义参数名,例如
       val add: (Int,Int) -> Int = { x:Int,y:Int->x+y }
      
       x,y的参数类型可以不写,因为在前面的小括号()里面定义了,所以可以写成x,y->...这种方式,改进后
       val add: (Int,Int) -> Int = { x,y->x+y }

       这里有个参数顺序问题,lambda表达式的参数顺序,是按照大括号{}方法体里面的参数顺序传参的,
       而不是按照定义函数时小括号()里的顺序传参的,如下面的代码
       val f:(x:Int, y:Int) -> Int = {y: Int, x: Int -> x - y}
       如果调用f(2,1),则参数会按照大括号里的顺序传参,所以上面的y==2,x==1.而不是按照小括号里的(x:Int, y:Int)的顺序传参的

- ######如果没有小括号,可以直接写大括号里的方法体
1. 写方法体的时候,因为前面没有在小括号()里定义参数类型,就需要在方法体里指明参数类型,例如下面需要指明x的参数类型
    ```
    val add1 = { x:Int->x + 1 }
    ```

#####如何定义lambda表达式(直接给方法传参,读者可以联想view.setOnClickListener(new OnClickListener...)):
1. 作为参数传递给高阶函数的时候,只需要写大括号{}方法体

>因为是作为参数传递给高阶函数,而在高阶函数里已经定义了方法类型和返回值,所以大括号{}方法体里的参数类型可以省略
2. 这时候不可以再写小括号()去定义函数了,只需要写大括号{}里的方法实现,因为在高阶函数里已经定义好了函数的参数类型和返回值,
>这就好像android用java代码给View设置OnClickListener点击监听,这时候只需要给View传递一个匿名类即可,例如
```view.setOnClickListener(new OnClickListener(View view){...});```
而不再需要
```view.setOnClickListener(OnClickListener onClickListener = new OnClickListener(View view){...});```
否则上面的方式会报错

       //举个例子:
       //定义高阶函数
       fun invoke(add:(x:Int,y:Int)->Int):Int{
           return add(1,2)
       }
       //调用高阶函数
       invoke { x, y -> x+y }
     