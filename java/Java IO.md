### NIO

NIO 主要有三大核心部分： Channel(通道)， Buffer(缓冲区), Selector。传统 IO 基于字节流和字符流进行操作， 而 NIO 基于 Channel 和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。 Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。 NIO 和传统 IO 之间第一个最大的区别是， IO 是面向流的， NIO 是面向缓冲区的。

### NIO和IO区别

IO是面向流的，NIO是面向缓冲区的，IO面向流意味着每次从流中读取一个或多个字节，直至读取所有字节，没有缓存

IO的各种流是阻塞的，NIO是非阻塞的，可以异步使用IO，（当线程从通道读取数据到缓冲区时，还可以进行其他操作，NIO将阻塞交给了后台线程执行，NIO的空余事件可以在其他通道上进行IO，因此一个线程可以管理多个Channel）

NIO支持Seletor，允许一个单独的线程监听多个Channel，使用一个单独的线程来选择通道，使得一个单独的线程很容易管理多个通道


~~~java
    public static void newIOTest() {
        FileInputStream inputStream = null;
        FileOutputStream outputStream = null;
        FileChannel inChannel = null;
        FileChannel outChannel = null;
        try {
            File inFile = new File("C:\\Users\\WangHao\\Desktop\\jianshu.png");
            File outFile = new File("C:\\Users\\WangHao\\Desktop\\js.png");

            inputStream = new FileInputStream(inFile);
            outputStream = new FileOutputStream(outFile);

            inChannel = inputStream.getChannel();
            outChannel = outputStream.getChannel();

            ByteBuffer buffer = ByteBuffer.allocate(256);

            while (inChannel.read(buffer) > 0) {
                buffer.flip();
                outChannel.write(buffer);
                buffer.clear();
            }
        } catch (IOException e) {

        } finally {
            try {
                inChannel.close();
                outChannel.close();
                inputStream.close();
                outputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
~~~

