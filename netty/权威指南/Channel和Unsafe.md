# Channel和Unsafe

Channel用于异步I/O操作和其他相关操作。

Unsafe是一个内部接口，聚合在Channel中协助进行网络读写相关的操作，作为Channel的内部辅助类。

## Channel工作原理

1. JDK的SocketChannel和ServerSocketChannel没有统一的Channel接口供业务开发者使用，对于用户而言，没有统一的操作视图，使用起来不方便。
2. JDK的SocketChannel和ServerSocketChannel是SPI类接口，具体实现由虚拟机厂家来提供，所以通过继承SPI功能类来扩展其功能的难度很大；直接实现ServerSocketChannel和SocketChannel抽象类，其工作量和重新开发一个新的Channel功能类是差不多的。
3. Netty的Channel需要能够跟Netty的整体架构融合在一起，例如I/O模型，基于ChannelPipeline的定制模型，以及基于元数据描述配置化的TCP参数等，这些JDK的SocketChannel和ServerSocketChanel都没有提供，需要重新封装。
4. 自定义的Channel，功能实现更加灵活。

主要设计理念：

1. 在Channel接口层，采用Facade模式进行统一封装，将网络I/O操作，网络I/O相关的其他操作封装起来，统一对外提供。
2. Channel接口的定义尽量大而全，为SocketChannel和ServerSocketChannel提供统一的视图，由不同子类实现不同的功能，公共功能在抽象父类中实现，最大程度地实现功能和接口的重用。
3. 具体实现采用聚合而非包含的方式，将相关的功能类聚合在Channel中，由Channel统一分配和调度，功能实现更加灵活。

	Channel read();

	ChannelFuture write(Object msg);

	ChannelFuture write(Object msg, ChannelPromise promise);

	ChannelFuture writeAndFlush(Object msg, ChannelPromise promise);

	ChannelFuture writeAndFlush(Object msg);

	Channel flush();

	ChannelFuture close();

	ChannelFuture connect();

	ChannelFuture bind();

#### 其他常用API功能说明

eventLoop()：获取Channel注册的EventLoop，EventLoop本质上是处理网络读写事件的Reactor线程。不仅用于处理网络事件，也可以用来执行定时任务和用户自定义NioTask等任务。

metadata()：获取当前Channel的TCP参数配置。

parent()：获取客户端Channel的

### Channel

Channel接口中包含一个Unsafe接口。

### AbstractChannel

AbstractChannel聚合了所有Channel使用到的能力对象，由AbstractChannel提供初始化和统一的封装，如果功能与子类强相关，则定义为抽象方法由子类具体实现。

网络I/O操作会触发ChannelPipeline中对应的事件方法。基于事件驱动可以理解为当Channel进行I/O操作时会产生对应的I/O事件，然后驱动事件在ChannelPipeline中传播，由对应的ChannelHandler处理。采用事件驱动可以通过事件定义来划分事件拦截切面，方便业务的定制和功能扩展，相比于AOP，性能更高。

Channel中的网络I/O操作直接调用DefaultChannelPipeline中的方法实现。

- connect()
- bind()
- read()
- write()
- close()
- register()

### AbstractNioChannel

- doRegitster()
- doDeregister()
- doConnect()
- doFinishConnect()
- doClose()

### AbstractNioByteChannel


### AbstractNioMessageChannel

---

Channel -> AbstractChannel -> AbstractNioChannel / AbstractServerChannel

AbstractNioMessageChannel / AbstractNioByteChannel