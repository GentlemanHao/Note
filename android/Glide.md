### Glide

**with**

获取AndroidManifest.xml文件中配置的自定义模块与@GlideModule注解标识的自定义模块，然后进行Glide配置的更改与组件的替换

初始化构建Glide实例需要的各种配置信息，例如线程池、Bitmap池、缓存策略、Engine等，然后利用这些信息来创建具体的Glide

将Glide的请求与Application或者隐藏的Fragment的生命周期进行绑定

**load**

主要是通过前面实例化的Glide和RequestManager来创建RequestBuilder，然后将传进来的参数复制给model

**into**

发起网络请求前判断是否有内存缓存或者磁盘缓存，没有则发起网络请求，成功后将返回的数据存储到内存和磁盘缓存，最后将返回的输入流解码成Drawable显示在View上