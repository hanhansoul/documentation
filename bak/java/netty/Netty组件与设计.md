# Netty组件与设计

- Netty的基于Java NIO的异步的和事件驱动的实现，保证了高负载下应用程序性能的最大化和可伸缩性。
- Netty包含了一组设计模式，将应用程序逻辑从网络层解耦，简化了开发过程。
- 最大限度地提高了可测试性、模块化以及代码的可重用性。


## Channel、EventLoop、ChannelFuture

- Channel：Socket
- EventLoop：控制流、多线程处理、并发
- ChannelFuture：异步通知

### Channel

基本I/O操作依赖于底层网络传输所提供的原语。

在基于Java的网络编程中，其基本的构造是Socket。Netty的Channel接口所提供的API，大大地降低了直接使用Socket类的复杂性。

- EmbeddedChannel
- LocalServerChannel
- NioDatagramChannel
- NioSctpChannel
- NioSocketChannel

### EventLoop

EventLoop定义了Netty的核心抽象，用于处理连接的生命周期中所发生的事件。

- 一个EventLoopGroup包含一个或者多个EventLoop；
- 一个EventLoop在它的生命周期内只和一个Thread绑定；
- 所有由EventLoop处理的I/O事件都将在它专有的Thread上被处理；
- 一个Channel在它的生命周期内只注册于一个EventLoop；
- 一个EventLoop可能会被分配给一个或多个Channel。

一个给定Channel的IO操作都是由相同的Thread执行的，实际上消除了对于同步的需要。

### ChannelFuture

Netty中所有的I/O操作都是异步的。因为一个操作可能不会立即返回，所以我们需要一种用于在之后的某个时间点确定其结果的方法。Netty提供了ChannelFuture接口，其addListener()方法注册了一个ChannelFutureListener，以便在某个操作完成时（无论是否成功）得到通知。

## ChannelHandler、ChannelPipeline

### ChannelHandler

从开发人员角度来看，Netty的主要组件是ChannelHandler，充当了所有处理入站和出站数据的应用程序逻辑的容器。

例如将数据从一种格式转换为另外一种格式，或者处理转换过程中所抛出的异常。ChannelInboundHandler接收入站事件和数据，这些数据随后将会被你的应用程序的业务逻辑所处理。

### ChannelPipeline

ChannelPipeline提供了ChannelHandler链的容器，并定义了用于在该链上传播入站和出站事件流的API。每一个Channel会被分配到专属的ChannelPipeline。

ChannelHandler安装到ChannelPipeline中的过程如下所示：
- 一个ChannelInitializer的实现被注册到了ServerBootstrap中；
- 当ChannelInitializer.initChannel()方法被调用时，ChannelInitializer将在ChannelPipeline中安装一组自定义的ChannelHandler；
- ChannelInitializer将它自己从ChannelPipeline中移除。

### 编码器与解码器

## 引导

Netty的引导类为应用程序的网络层配置提供了容器，这涉及将一个进程绑定到某个指定的端口，或者将一个进程连接到另一个运行在某个指定主机的指定端口上的进程。

有两种类型的引导：一种用于客户端（简单地称为Bootstrap），而另一种
（ServerBootstrap）用于服务器。

1. ServerBootstrap将绑定到一个端口，因为服务器必须要监听连接，而Bootstrap则是由想要连接到远程节点的客户端应用程序所使用的。
2. 第二个区别可能更加明显。引导一个客户端只需要一个EventLoopGroup，但是一个ServerBootstrap则需要两个（也可以是同一个实例）。

因为服务器需要两组不同的Channel。第一组将只包含一个ServerChannel，代表服务器自身的已绑定到某个本地端口的正在监听的套接字。而第二组将包含所有已创建的用来处理传入客户端连接（对于每个服务器已经接受的连接都有一个）的Channel。