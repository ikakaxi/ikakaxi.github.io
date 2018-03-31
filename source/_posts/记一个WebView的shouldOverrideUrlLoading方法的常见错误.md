title: 记一个WebView的shouldOverrideUrlLoading方法的常见错误
author: liuhc
tags: []
categories:
  - android
date: 2018-03-31 23:35:00
---
平时我们写WebViewClient的时候，会覆盖shouldOverrideUrlLoading方法，代码可能都是下面这种：  

```java
webView.webViewClient = object : WebViewClient() {  
 override fun shouldOverrideUrlLoading(view: WebView, url: String): Boolean {  
 view.loadUrl(url)  
 return true  
 }  
 }  
```

今天看一些资料的时候发现，这个方法直接return false就可以在该WebView里跳转而不是弹出系统的浏览器选择框，网上充斥的都是某篇文章说的必须view.loadUrl(url)才会在自己的程序里跳转页面而不是跳出系统的浏览器选择框。今天才知道这个，惭愧惭愧。。。

总结一下：

1.  如果不设置webView.webViewClient，调用webView.loadUrl(url)就会弹出系统的浏览器选择框让你选一个浏览器来显示。
2.  如果设置webView.webViewClient，就会在自己的WebView里跳转页面。
3.  如果设置webView.webViewClient，并且重写shouldOverrideUrlLoading方法，在方法体里如果return true，那么就需要自己调用view.loadUrl(url)才能显示了，因为return true的时候一般是跳转Activity之类的逻辑。