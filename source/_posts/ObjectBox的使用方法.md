title: ObjectBox的使用方法
author: superman
date: 2018-02-07 22:38:38
categories:
- android
tags:
- 第三方框架
---
官网:http://objectbox.io/

ObjectBox目前是Android上速度最快的数据库,有关ObjectBox和GreenDAO,Room,Real的对比在官网就有
<!--more-->


**gradle配置**
1. root project下面的gradle.build文件配置
```
// In your root build.gradle file:
buildscript {
    ext.objectboxVersion = '1.3.3'
    repositories {
        jcenter()
        maven { url "http://objectbox.net/beta-repo/" }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.3'
        classpath "io.objectbox:objectbox-gradle-plugin:$objectboxVersion"
    }
}
 
allprojects {
    repositories {
        jcenter()
        maven { url "http://objectbox.net/beta-repo/" }
    }
}
```

2. module下面的gradle.build文件配置
```
// In your (app) module build.gradle file:
apply plugin: 'com.android.application'
apply plugin: 'io.objectbox'
```
注意要将io.objectbox写到application下面,不止这个ObjectBox,只要是第三方都要写到application下面

在module的dependencies里配置
```
dependencies {
    // 如果只是用java开发
    // compile "io.objectbox:objectbox-android:$objectboxVersion"
    // apt
    // annotationProcessor "io.objectbox:objectbox-processor:$objectboxVersion"

    // 如果支持kotlin开发
    // objectbox-kotlin包含objectbox-android
    compile "io.objectbox:objectbox-kotlin:$objectboxVersion"
    // 当使用Kotlin时使用kapt(kapt包含apt功能):
    kapt "io.objectbox:objectbox-processor:$objectboxVersion"
}
```

这样配置就完成了,然后在Application里初始化MyObjectBox

**这里有个需要注意的地方**
MyObjectBox这个类是编译生成的,所以必须先定义一个数据表,然后执行android studio的rebuild project,才会出现这个类,所以第一次使用的时候,先不要写```MyObjectBox.builder().androidContext(this).build()```,先定义一个数据表,比如下面这样
```
//java代码
@Entity
class A {
    @Id
    public Long name = 0L;
}
```
或者
```
//kotlin代码
@Entity data class B(@Id var age:Long = 0)
```
然后执行rebuild project,这时候在build/generated/source/katp下面就生成了MyObjectBox类和你刚才定义的表,然后就可以在Application里去初始化了