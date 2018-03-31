title: Class.isAssignableFrom与 instanceof 区别
author: superman
tags: []
categories:
  - java
date: 2018-02-08 09:50:00
---
isAssignableFrom 是用来判断一个类Class1和另一个类Class2是否相同或是另一个类的超类或接口。 
<!--more-->  
  通常调用格式是   
        Class1.isAssignableFrom (Class2)   
  调用者和参数都是   java.lang.Class   类型。   
      
  而   instanceof   是用来判断一个对象实例是否是一个类或接口的或其子类子接口的实例。   
    格式是：   o   instanceof   TypeName     
    第一个参数是对象实例名，第二个参数是具体的类名或接口名 
具体例子如下：
```
public class TestCase {  
    public static void main(String[] args) {  
        TestCase test = new TestCase();  
        test.testIsAssignedFrom1();  
        test.testIsAssignedFrom2();  
        test.testIsAssignedFrom3();  
        test.testInstanceOf1();  
        test.testInstanceOf2();  
    }  
  
    public void testIsAssignedFrom1() {  
        System.out.println(String.class.isAssignableFrom(Object.class));  
    }  
  
    public void testIsAssignedFrom2() {  
        System.out.println(Object.class.isAssignableFrom(Object.class));  
    }  
  
    public void testIsAssignedFrom3() {  
        System.out.println(Object.class.isAssignableFrom(String.class));  
    }  
  
    public void testInstanceOf1() {  
        String s = "";  
        System.out.println(s instanceof Object);  
    }  
  
    public void testInstanceOf2() {  
        Object o = new Object();  
        System.out.println(o instanceof Object);  
    }  
  
}  
```
打印结果： 
```
false  
true  
true  
true  
true  
```