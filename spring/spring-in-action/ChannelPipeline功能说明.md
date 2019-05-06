## ChannelPipeline功能说明

### ChannelPipeline的事件处理

1. SocketChannel.read()方法读取ByteBuf：触发ChannelRead事件。由I/O线程NioEventLoop调用ChannelPipeline的fireChannelRead(Object msg)方法传输到ChannelPipeline中。

netty中的事件分为inbound事件和outbound事件。inbound事件通常由I/O线程触发，例如TCP链路建立事件、链路关闭事件、读事件、异常通知事件等。

1. ChannelHandlerContext.bind()：绑定本地地址事件
2. ChannelHandlerContext.connect()：连接服务端事件
3. ChannelHandlerContext.write()：发送事件
4. ChannelHandlerContext.flush()：刷新事件
5. ChannelHandlerContext.read()：读事件
6. ChannelHandlerContext.disconnect()：断开连接事件
7. ChannelHandlerContext.close()：关闭当前Channel事件

### 自定义拦截器

### 构建pipeline

使用ServerBootstrap或者Bootstrap启动服务端或者客户端时，netty会为每一个Channel连接创建一个独立的pipeline，只需要将自定义的拦截器加入到pipeline中。

### ChannelPipeline的主要特性

ChannelPipeline支持运行时动态地添加或者删除ChannelHandler。

ChannelPipeline是线程安全的，N个业务线程可以并发地操作ChannelPipeline。但ChannelHandler却不是线程安全的，需要用户自己保证ChannelHandler的线程安全。

### ChannelPipeline的inbound事件

当发生某个I/O事件的时候，事件在pipeline中得到传播和处理。

pipeline中以fire命名的方法都是I/O线程流向用户业务Handler的inbound事件。

### ChannelPipeline的outbound事件

由用户线程或者代码发起的I/O操作被称为outbound事件。

## ChannelHandler功能说明

CHannelHandler支持的注解：
1. Sharable：多个ChannelPipeline共用同一个ChannelHandler；
2. Skip：被Skip注解的方法不会被调用。

### ChannelHandlerAdapter功能说明

ChannelHandlerAdapter实现了ChannelHandler所有的方法，当用户ChannelHandler只关心某些事件时，只需要覆盖ChannelHandlerAdapter对应的方法即可。

### ByteToMessageDecoder功能说明

ByteToMessageDecoder解码器：负责将读取到的字节数组或者字节缓冲区解码为业务可以使用的POJO对象。

用户的解码器继承ByteToMessageDecoder，只需要实现decode()抽象方法即可。

### MessageToMessageDecoder功能说明

MessageToMessageDecoder负责将一个对象二次解码为其他对象。

如在HTTP+XML协议栈中，第一次解码将字节数组解码为HttpRequest对象，然后对HttpRequest消息中的消息体字符串进行二次解码，将XML字符串解码为POJO对象。

### LengthFieldBasedFrameDecoder功能说明

根据长度实现的半包解码器。

### MessageToByteEncoder功能说明

### MessageToMessageEncoder功能说明

### LengthFieldPrepender

将发送消息的长度写入到ByteBuf的前2个字节重新编码为新消息。

### ChannelHandler源码分析

### ChannelHandler的类继承关系图

系统ChannelHandler分类：
1. ChannelPipeline的系统ChannelHandler，用于I/O操作和对事件进行预处理，对于用户不可见，包括HeadHandler和TailHandler；
2. 编解码ChannelHandler，包括ByteToMessageCodec、MessageToMessageDecoder等；
3. 其他系统功能性ChannelHandler，包括流程整型Handler、读写超时Handler、日志Handler等。

### ByteToMessageDecoder

### MessageToMessageDecoder