### WebChromeClient

**`WebView`通过`setWebChromeClient`设置**

页面加载进度变化，newProgress in 0..100

~~~java
public void onProgressChanged(WebView view, int newProgress)
~~~

标题变化、Icon变化

~~~java
public void onReceivedTitle(WebView view, String title)
public void onReceivedIcon(WebView view, Bitmap icon)
public void onReceivedTouchIconUrl(WebView view, String url, boolean precomposed)
~~~

~~~java
public boolean onJsAlert(WebView view, String url, String message, JsResult result)
public boolean onJsConfirm(WebView view, String url, String message, JsResult result)
public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result)
~~~

