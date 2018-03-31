title: android内存泄漏的多种方式
author: liuhc
tags:
  - 内存泄漏
categories:
  - android
date: 2018-03-31 23:19:00
---
> 内存泄漏的定义：简单的讲，就是该被释放的对象没有释放，虽然不再被使用却一直被某个或某些实例所持有而导致GC不能回收

因为Java的GC机制，我们并不需要去像C++一样去手动释放内存，但是如果不注意，Java一样会发生内存泄漏，下面列举一些开发中常见的内存泄漏场景：

###### 1. 静态集合：
```java
static Vector v = new Vector(10);  
for (int i = 1; i<100; i++){  
 Object o = new Object();  
 v.add(o);  
 o = null;  
}  
```

在这个例子中，循环申请Object 对象，并将所申请的对象放入一个Vector 中，如果仅仅释放引用本身（o=null），那么Vector 仍然引用该对象，所以这个对象对GC 来说是不可回收的。因此，如果对象加入到Vector 后，还必须从Vector 中删除，最简单的方法就是将Vector对象设置为null。

###### 2. 当集合里面的对象属性被修改后，再调用remove()方法时不起作用。
```java
 data class Person(private val name: String, private val password: String, var age: Int)   

 fun main(args: Array<String>) {  
 val set = HashSet<Person>();  
 val p1 = Person("唐僧", "pwd1", 25)  
 val p2 = Person("孙悟空", "pwd2", 26)  
 val p3 = Person("猪八戒", "pwd3", 27)  
 set.add(p1)  
 set.add(p2)  
 set.add(p3)  
 System.out.println("总共有:" +set.size + " 个元素!"); //结果：总共有:3 个元素!  
 set.forEach {  
 System.out.println(it)  
 }  

 p3.age = 2 //修改p3的年龄,此时p3元素对应的hashcode值发生改变  
 set.remove(p3) //此时remove不掉，造成内存泄漏  
 set.add(p3) //重新添加，居然添加成功  
 System.out.println("总共有:" +set.size + " 个元素!"); //结果：总共有:4 个元素!  
 set.forEach {  
 System.out.println(it)  
 }  
}  
```

这种情况需要我们重写hashCode和equals方法，例如将Person类改为：  

```java
 data class Person(private val name: String, private val password: String, var age: Int) {  
 override fun equals(other: Any?): Boolean {  
 return when {  
 other !is Person -> false  
 name != other.name -> false  
 else -> true  
 }  
 }  

 override fun hashCode(): Int {  
 return name.hashCode()  
 }  
}  
```

这时候结果就正确了，添加的时候不会重复了，可以自己将代码复制到IDE查看结果

###### 3. 监听器

Android很多控件设置监听器是setXXXListener，比如Button，但是有些View设置某些监听是addXXXListener，比如EditText，如果不注意可能就调用了多次addXXXListener方法，造成了内存泄漏和莫名其妙的bug

###### 4. 各种连接

比如数据库连接，网络连接，打开文件流之类的，除非其显式的调用了其close()方法将其连接关闭，否则是不会自动被GC回收的。

###### 5. 非静态内部类

这也是很常见的一种情况，我们写代码时写的最多的就是  
```java
private val mHandler = object : Handler() {  
 override fun handleMessage(msg: Message) {  
 super.handleMessage(msg)  
 }  
}  
```

而在非静态内部类里，是默认持有外部类引用的，举个例子如果我们调用  
```java
mHandler.sendMessageDelayed(Message.obtain(),2000) ```

然后finish这个Activity，这时候就内存泄漏了，因为mHandler持有外部Activity的强引用，所以如果Handler中的任务没有执行完那么这个Activity是不能被回收的，这里的例子比较简单，有可能这不是Handler而是一个Thread之类的，解决办法是我们在Activity的onDestory方法里调用Handler的removeCallbacksAndMessages方法(如果是这里这种情况)，并且尽量将内部类写成静态的内部类，静态内部类是不持有外部类的引用的

> 如果一定要持有外部类的应用，我们可以考虑使用弱应用的方式

###### 6. 单例模式的类持有其他类的引用

举个例子： 
```java
object Util {  
 private lateinit var mContext: Context  
 fun init(context: Context) {  
 mContext = context  
 }  
 fun getXXX(res: Int): String {  
 return mContext.getString(res)  
 }  
}  
```

可能有人想传入一个context，以后使用就不用每次都传入了，但是这时候这个context的内存就发生了泄露，因为这个context被这个单例工具类一直持有，所以这个context就没办法被GC回收了，所以我们应该在这个工具类的每个方法里都传入Context而不是在init里一次传入一个Context想一劳永逸，或者根据你的业务需要传入ApplicationContext，修改如下：  

```java
object Util {  
 //传入ApplicationContext  
 private lateinit var mContext: Context  
 fun init(context: Context) {  
 mContext = context.applicationContext  
 }  
 //或者每个方法传入Context而不是一直持有引用  
 fun getXXX(context:Context,res: Int): String {  
 return context.getString(res)  
 }  
}  
```

###### 7. BraodcastReceiver未及时解绑

广播我们用的比较多，但是很多新手动态注册广播后，并没有及时在Activity的onDestory方法里解绑，导致内存泄漏

###### 8. Bitmap 没调用 recycle()

对于 Bitmap 对象在不使用时,我们应该先调用 recycle() 释放内存，然后才它设置为 null. 因为加载 Bitmap 对象的内存空间，一部分是 java 的，一部分 C 的（因为 Bitmap 分配的底层是通过 JNI 调用的 )。 而这个 recyle() 就是针对 C 部分的内存释放。

比较常见的就是这些