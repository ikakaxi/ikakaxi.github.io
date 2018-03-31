title: WebView中JavaScript与Java交互流程与WebView的漏洞处理
author: liuhc
tags:
  - WebView
categories:
  - android
date: 2018-03-31 23:26:00
---
现在Android开发很多都是混合开发了，WebView也不再只是显示一个网页而已而是与Native App有很多交互，所以熟悉JavaScript与Java交互很有必要。  

# 1. JavaScript调用Java

## 1.1.1 Java注册

```java
// 设置与Js交互的权限  
webSettings.setJavaScriptEnabled(true);  
webView.addJavascriptInterface(new JSInvokeClass(), "JSInvokeClassName");  
...  
private static final class JSInvokeClass {  
 // 被JS调用的方法必须加入@JavascriptInterface注解  
 @JavascriptInterface  
 public String methodName(String param) {  
 ...  
 return "{error:'error'}";  
 }  
}  
```

## 1.1.2 JavaScript调用

```html
//用一个Html的button测试  
<button onclick="JSInvokeClassName.methodName('aabbcc')">调用Java方法</button>  
```

JSInvokeClassName和methodName分别是JavaScript调用在Java代码里注册的类名映射和方法，名字随便起只要对应上就可以

但是这种方法有很多漏洞，需要解决如下问题：

1.  防止XSS

2.  android 4.2之前的版本不要使用javainterface，可使用拦截prompt的方式

3.  移除android 4.2之前的默认接口

```java
	removeJavascriptInterface(“searchBoxJavaBridge_”)  
	removeJavascriptInterface(“accessibility”)  
	removeJavascriptInterface(“accessibilityTraversal”)  
```
4.  防止不安全的访问

```java
    setAllowFileAccessFromFileURLs(false);  
    setAllowUniversalAccessFromFileURLs(false);  
```
5.  使用https或者http2.0交互

解决参考

https://blog.csdn.net/carson_ho/article/details/64904635
https://kotlintc.com/articles/5224?fr=email

## 1.2通过 WebViewClient 的方法shouldOverrideUrlLoading ()回调拦截 url

优点：不存在方式1.1的漏洞；

缺点：JS获取Android方法的返回值复杂，需要Java调用JavaScript的方法然后在JavaScript的这个方法里再调用1.1.1中注册的方法

示例如下：
```java
// 假定传入进来的 url = "js://webview?arg1=111&arg2=222"（同时也是约定好的需要拦截的）  

Uri uri = Uri.parse(url);  
// 步骤1：根据协议的参数，判断是否是所需要的url（一般根据scheme（协议格式） & authority（协议名）判断）  
// 如果url的协议 = 预先约定的 js 协议就解析往下解析参数  
if (uri.getScheme().equals("js")) {  
 // 步骤2：如果 authority  = 预先约定协议里的 webview，即代表都符合约定的协议  
 if (uri.getAuthority().equals("webview")) {  
 // 步骤3：执行JS所需要调用的逻辑  
 System.out.println("js调用了Android的方法");  
 // 可以在协议上带有参数并传递到Android上  
 HashMap<String, String> params = new HashMap<>();  
 Set<String> collection = uri.getQueryParameterNames();  
 }  
 return true;  
}  
```

## 1.3通过 WebChromeClient 的onJsAlert()、onJsConfirm()、onJsPrompt（）方法回调拦截JS对话框alert()、confirm()、prompt（） 消息

一般使用onJsPrompt方法，因为该方法可以返回值给JavaScript，既没有1.1的漏洞也能方便的接收JavaScript的值和返回给JavaScript值

示例如下：
```java
// 假定传入进来的 url = "js://webview?arg1=111&arg2=222"（同时也是约定好的需要拦截的）  

Uri uri = Uri.parse(url);  
// 步骤1：根据协议的参数，判断是否是所需要的url（一般根据scheme（协议格式） & authority（协议名）判断）  
// 如果url的协议 = 预先约定的 js 协议就解析往下解析参数  
if (uri.getScheme().equals("js")) {  
 // 步骤2：如果 authority  = 预先约定协议里的 webview，即代表都符合约定的协议  
 if (uri.getAuthority().equals("webview")) {  
 // 步骤3：执行JS所需要调用的逻辑  
 System.out.println("js调用了Android的方法");  
 // 可以在协议上带有参数并传递到Android上  
 HashMap<String, String> params = new HashMap<>();  
 Set<String> collection = uri.getQueryParameterNames();  
 ...  
 //参数result:代表消息框的返回值(输入值)  
 result.confirm("js调用了Android的方法成功啦");  
 }  
 return true;  
}  
```

---

# 2. Java调用JavaScript

> 这时候有**两种**方法

先上HTML代码
```html
<!DOCTYPE html>  
<html>  
 <head>  
 <meta charset="utf-8">  
 <script>  
 function callJS(){  
 alert("Android调用了JS的callJS方法");  
 }  
 </script>  
 </head>  
</html>  
```

> 特别注意：Java调用JavaScript代码调用一定要在 onPageFinished（） 回调之后才能调用，否则不会调用。onPageFinished()属于WebViewClient类的方法，主要在页面加载结束时调用。

## 2.1 loadUrl

```java
// 设置与Js交互的权限  
webSettings.setJavaScriptEnabled(true);  
webView.loadUrl("javascript:callJS()");  
```

## 2.2 evaluateJavascript

```java
webSettings.setJavaScriptEnabled(true);  
webView.evaluateJavascript（"javascript:callJS()", new ValueCallback<String>() {  
 @Override  
 public void onReceiveValue(String value) {  
 //此处为 js 返回的结果  
 }  
 });  
```

优点：该方法比第一种方法效率更高、使用更简洁。因为该方法的执行不会使页面刷新，而第一种方法（loadUrl ）的执行则会。该方法在Android 4.4 后才可使用。

#### 使用建议：两种方法混合使用，即Android 4.4以下使用方法2.1，Android 4.4以上方法2.2，示例如下：
```java
// Android版本变量  
final int version = Build.VERSION.SDK_INT;  
// 因为该方法在 Android 4.4 版本才可使用，所以使用时需进行版本判断  
if (version < 18) {  
 webView.loadUrl("javascript:callJS()");  
} else {  
 webView.evaluateJavascript（"javascript:callJS()", new ValueCallback<String>() {  
 @Override  
 public void onReceiveValue(String value) {  
 //此处为 js 返回的结果  
 }  
 });  
}  
```

参考：https://blog.csdn.net/carson_ho/article/details/64904691