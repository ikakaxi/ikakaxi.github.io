title: Retrofit2的一些坑
author: superman
date: 2018-02-07 22:32:18
categories:
- android
tags:
- 第三方框架
---

现在都在说Retrofit2和RxJava2,作为一个程序猿自然不能落伍,然后就试用了一下,遇到一些坑,在这里记录一下
<!--more-->
#####1.
```io.reactivex.exceptions.OnErrorNotImplementedException: closed```
和
```Caused by: java.lang.IllegalStateException: closed```

这两个异常今天研究了好几个小时...  网上都是说Response.body().string()方法不能调用2次,但是在我的代码里这个方法并没有调用2次,一个Response的ResponseBody属性我只调用了1次string()方法,后来终于发现,在我的判断里有不同的流程,其中一个流程就没有问题,然后发现流程A的Response我直接return了,但是流程B的Response我调用了它的body().string()方法,我判断了里面的返回结果字符串,发现如果没有错误就把这个流程B的Response对象return了,但是!!! 因为我return的这个Response已经被我调用过body().string()方法了,Retrofit2把结果返回上层应用的时候,在convert的时候(因为retrofit调用了addConverterFactory方法)就出错了,因为这个Response的ResponseBody属性已经close了,所以在返回的时候,需要一个新的Response,并设置其中的body为新的body,代码如下:
```
Response response = chain.proceed(newRequest);//执行新请求
String responseString = response.body().string();
//对responseString...执行一些判断之类的操作
return response.newBuilder().body(ResponseBody.create(response.body().contentType(), responseString)).build();
```
###注意:
>1.上面的这个问题,如果你添加了自己的log拦截器,在你的log拦截器里如果调用了Response的body属性的string()方法,那么返回的response对象你也要这么处理,否则在其他类里时候的时候,虽然response不是一个对象,但是里面的body是同一个对象(不信的可以debug查看log拦截器里面你返回的response的body属性内存地址和其他拦截器里同请求的这个response的body属性的内存地址),然后其他类调用response.body().string()的时候会报close错误,所以只要用到了response.body().string()方法,在返回这个response的时候就要像上面那样返回才可以避免close问题

>2.如果不是string()方法,你用的是response.body().bytes()方法再转字符串是一样的,在方法内部都close了,所以仍然需要像上面那样处理

#####2.
```Caused by: java.lang.IllegalStateException: network interceptor xxx must call proceed() exactly once```
和
```io.reactivex.exceptions.OnErrorNotImplementedException: network interceptor xxx must call proceed() exactly once```

这是因为你的```OkHttpClient.Builder```调用的```addNetworkInterceptor```方法添加的拦截器,这种方式添加的拦截器里面的```Chain```只能调用一次```proceed```方法,如果想调用多次,你的拦截器就要用```addInterceptor```方法添加,就没问题了