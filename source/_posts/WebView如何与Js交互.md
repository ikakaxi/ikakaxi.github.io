title: WebView如何与Js交互
author: superman
date: 2018-02-07 22:38:51
categories:
- android
tags:
- WebView
---

我用桥接的方式实现的:
<!--more-->
```
	@SuppressLint("AddJavascriptInterface")
	protected void initWebView() {
		super.initWebView();
		String ua = webView.getSettings().getUserAgentString();
		webView.getSettings().setUserAgentString(ua + ";abc");
        //关键代码
		webView.addJavascriptInterface(new JSInvokeClass(), "Test");
	}
```
JSInvokeClass名字是自定义的,随便起名
```
    private final class JSInvokeClass {
		@JavascriptInterface
		public String getUserInfo() {
			return "字符串";
		}
	}
```

js直接在按钮的点击事件里写"Test.getUserInfo()"就可以调用了
例如
```
<html>
<body>
<script>

function test(){
 var data = Test.getUserInfo()
 document.getElementById('content').innerHTML = data
}

</script>

<button id="testBtn" type="button" onClick="test()">getUserInfo</button>
<div id="content"/>

</body>
</html>
```