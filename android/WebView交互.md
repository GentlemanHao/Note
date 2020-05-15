**Android调用JS代码**

通过`WebView`的`loadUrl()`方法

通过`WebView`的`evaluateJavaScript()`方法

**JS调用Android代码**

通过`WebView`的`addJavaScriptInterface()`方法进行对象映射

通过`WebViewClient`的`shouldOverrideUrlLoading()`方法进行毁掉拦截

通过`WebChromeClient`的`onJsAlert()、onJsConfirm()、onJsPrompt()`方法回调拦截JS对话框消息



**主要类及常用方法**

<img src="https://user-gold-cdn.xitu.io/2018/5/21/163807ca2dcc387f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1" style="zoom:80%;" />