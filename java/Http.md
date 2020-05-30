### Http请求过程

DNS解析、TCP连接、发送HTTP请求、服务器处理请求并返回HTTP报文、浏览器解析渲染页面、连接结束



### TCP的三次握手和四次挥手

TCP是面向连接的无状态的协议，为了连接的可靠性，每次连接的建立都需要3次握手

<img src="https://img-blog.csdn.net/20180717202520531?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4OTUwMzE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" style="zoom: 80%;" />

第一次握手：客户端发送`SYN(SYN = 1 , seq = x)`包，客户端进入SYN_SEND状态，等待服务端确认

第二次握手：服务端收到客户端SYN包，必须确认客户端SYN包，同时自己也发送一个SYN包，即`ACK (ACK = 1 , ack = x + 1) + SYN (SYN = 1 , seq = y)`，服务端进入SYN_RECV状态

第三次握手：客户端收到ACK + SYN包，向服务端发确认包`ACK(ack = y + 1)`，客户端和服务端进入ESTABLISHED状态

<img src="https://img-blog.csdn.net/20180717204202563?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4OTUwMzE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" style="zoom: 80%;" />

第一次挥手：客户端发送`FIN(FIN = 1 , seq = u)`，用来关闭客户端到服务端的数据传送，客户端进入FIN_WAIT_1状态

第二次挥手：服务端收到FIN后，发送`ACK(ACK = 1 , ack = u + 1)`和自己的序列号(seq = v)给客户端，服务端进入CLOSE_WAIT状态，客户端收到服务端的确认后进入FIN_WAIT_2状态

第三次挥手：服务端发送`FIN(FIN = 1 , seq = w)`(假设服务端有发送了一些数据，此时为w)，用来关闭服务端到客户端的数据传送，服务端进入LAST_ACK状态

第四次挥手：客户端收到FIN后，发送`ACK(ACK = 1 , ack = w + 1)`给服务端，客户端进入TIME_WAIT状态，服务端收到后进入CLOSE状态



### HTTP各版本区别

**Http0.9**：只支持GET请求，没有请求头概念，服务端响应之后立即关闭TCP连接

**Http1.0**：新增POST、DELETE、PUT、HEADER等方式，增加请求头和响应头概念，在通信中指定了HTTP协议版本号，以及状态码、权限、缓存、内容编码等，扩充了传输内容格式，图片、音视频、二进制都可以传输，无状态、无连接

**Http1.1**：长连接，新增Connection字段，可设置keep-alive保持连接；管道化，基于长连接的基础，可以不等第一个请求响应继续发送后面的请求，但响应的顺序还是按照请求的顺序返回；缓存处理，新增cache-control字段；断点续传

**Http2**：二进制分帧；多路复用，在共享TCP链接的基础上同时发送请求和响应；头部压缩；服务器推送，服务器可以额外向客户端推送资源，无需客户端明确请求



### HTTP长连接

Http1.0是短连接，Http1.1默认是长连接，就是默认Connection的值是keep-alive，但长连接的实质是指TCP连接，TCP连接是一个双向的通道，可以保持一段时间不关闭，长连接情况下，多个Http请求可以复用同一个TCP连接，节省了很多TCP连接建立和断开的消耗



### HTTP和HTTPS

HTTPS是一种通过计算机网络进行安全通信的传输协议，HTTPS经由HTTP进行通信，但利用SSL/TLS来加密数据包，提供对网络服务器的身份认证，保护交换数据的隐私和完整性

<img src="https://user-gold-cdn.xitu.io/2020/3/1/17095bb260b74d32?imageView2/0/w/1280/h/960/format/webp/ignore-error/1" style="zoom:56%;" />



























