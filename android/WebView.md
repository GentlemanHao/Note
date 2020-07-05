### WebSettings

~~~java
webSetting.setSupportZoom(boolean support) //设置是否支持控件和手势缩放
webSetting.setMediaPlaybackRequiresUserGesture(boolean require) //设置是否需要手势来播放媒体
webSetting.setBuiltInZoomControls(boolean enabled) //设置是否使用其内置缩放机制
webSetting.setDisplayZoomControls(boolean enabled) //设置是否显示屏幕缩放控件
webSetting.setAllowFileAccess(boolean allow) //设置是否允许文件访问
webSetting.setAllowContentAccess(boolean allow) //设置是否允许内容URL访问
webSetting.setLoadWithOverviewMode(boolean overview) //设置是否以概述模式加载页面
webSetting.setTextZoom(int textZoom) //设置页面的文本缩放百分比，默认值为100
webSetting.setUseWideViewPort(boolean use) //设置是否启用对viewport标记的支持
webSetting.setSupportMultipleWindows(boolean support) //设置是否支持多个窗口
webSetting.setLayoutAlgorithm(LayoutAlgorithm l) //设置基础布局算法，会导致对WebView重新布局
webSetting.setLoadsImagesAutomatically(boolean flag) //设置是否应加载图像资源
webSetting.setBlockNetworkImage(boolean flag )//设置是否禁止从网络加载图像资源
webSetting.setBlockNetworkLoads(boolean flag) //设置是否禁止从网络加载资源
webSetting.setJavaScriptEnabled(boolean flag) //设置是否执行JavaScript
webSetting.setAppCacheEnabled(boolean flag) //设置是否启用应用程序缓存
webSetting.setAppCachePath(String appCachePath) //设置应用程序缓存文件的路径
webSetting.setCacheMode(@CacheMode int mode) //设置WebView缓存模式
webSetting.setDatabaseEnabled(boolean flag) //设置是否启用数据库存储
webSetting.setDomStorageEnabled(boolean flag) //设置是否启用DOM存储
webSetting.setGeolocationEnabled(boolean flag) //设置是否启用地理定位
webSetting.setJavaScriptCanOpenWindowsAutomatically(boolean flag) //JavaScript是否可以自动打开窗口
webSetting.setDefaultTextEncodingName(String encoding) //设置解码html时使用的编码， 默认值为UTF-8
webSetting.setUserAgentString(@Nullable String ua) //设置WebView的用户代理字符串
webSetting.setMixedContentMode(int mode) //当尝试从不安全来源加载资源时，配置WebView的行为
webSetting.setSafeBrowsingEnabled(boolean enabled) //设置是否启用安全浏览
~~~



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

通知客户端显示警告、确认、信息弹框，返回true表示客户端处理弹框，并通过JsResult、JsPromptResult回调结果

~~~java
public boolean onJsAlert(WebView view, String url, String message, JsResult result)
public boolean onJsConfirm(WebView view, String url, String message, JsResult result)
public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result)
~~~

通知客户端进入或退出全屏模式

~~~java
public void onShowCustomView(View view, CustomViewCallback callback)
public void onHideCustomView()
~~~

通知客户端创建或关闭WebView

~~~java
public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg)
public void onCloseWindow(WebView window)
~~~

通知客户端获取焦点

~~~java
public void onRequestFocus(WebView view)
~~~

通知客户端Web正在请求或取消访问权限，可以通过`PermissionRequest`的`grant(String[] resources)`和`deny()`进行权限请求结果的回调

~~~java
public void onPermissionRequest(PermissionRequest request)
public void onPermissionRequestCanceled(PermissionRequest request)
    
public void onGeolocationPermissionsShowPrompt(String origin, GeolocationPermissions.Callback callback)
public void onGeolocationPermissionsHidePrompt()    
~~~

向客户端报告JS的控制台消息

~~~java
public boolean onConsoleMessage(ConsoleMessage consoleMessage)
~~~

请求客户端视频播放的默认海报和加载时的加载视图

~~~java
public Bitmap getDefaultVideoPoster()
public View getVideoLoadingProgressView()
~~~

获取所有历史访问列表，用于链接着色

~~~java
public void getVisitedHistory(ValueCallback<String[]> callback)
~~~

通知客户端显示文件选择器，并通过`filePathCallback`进行回调

~~~java
public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback,
            FileChooserParams fileChooserParams)
~~~



### WebViewClient

**`WebView`通过`setWebViewClient`设置**

当有新的URL加载的时候，让客户端控制是否对其进行拦截，返回true表示拦截WebView加载，返回false表示允许WebView加载

~~~java
public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request)
~~~

通知客户端页面加载的开始与完成

~~~java
public void onPageStarted(WebView view, String url, Bitmap favicon)
public void onPageFinished(WebView view, String url)
~~~

通知客户端WebView加载指定的资源

~~~java
public void onLoadResource(WebView view, String url)
~~~

通知客户端进行资源请求，客户端可以返回数据给WebView使用，如果返回null，WebView将使用默认方式加载资源

~~~java
public WebResourceResponse shouldInterceptRequest(WebView view, WebResourceRequest request)
~~~

通知客户端WebView加载资源时出错，无法连接服务器、Http错误、SSL错误

~~~java
public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error)
public void onReceivedHttpError(WebView view, WebResourceRequest request, WebResourceResponse errorResponse)
public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error)
~~~

通知客户端更新链接访问历史数据库

~~~java
public void doUpdateVisitedHistory(WebView view, String url, boolean isReload)
~~~

通知客户端以处理SSL证书请求、Http身份认证请求

~~~java
public void onReceivedClientCertRequest(WebView view, ClientCertRequest request)
public void onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host, String realm)
~~~

给客户端机会处理WebView的按键事件、未处理按键事件、未处理输入事件

~~~java
public boolean shouldOverrideKeyEvent(WebView view, KeyEvent event)
public void onUnhandledKeyEvent(WebView view, KeyEvent event)
public void onUnhandledInputEvent(WebView view, InputEvent event)
~~~

通知客户端缩放比例改变

~~~java
public void onScaleChanged(WebView view, float oldScale, float newScale)
~~~

通知客户端已处理自动登录请求

~~~java
public void onReceivedLoginRequest(WebView view, String realm, @Nullable String account, String args)
~~~



