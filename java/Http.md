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































