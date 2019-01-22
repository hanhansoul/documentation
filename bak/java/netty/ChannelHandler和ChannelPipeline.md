# ChannelHandler和ChannelPipeline

## ChannelHandler

### Channel的生命周期

- ChannelUnregistered
- ChannelRegistered
- ChannelActive
- ChannelInactive

当状态发生变化时，将会生成对应的事件，冰杯转发给ChannelPipeline中的ChannelHandler，并对其作出响应。

### ChannelHandler的生命周期

- handlerAdded：当把ChannelHandler添加到ChannelPipeline中时被调用
- handlerRemoved：当从ChannelPipeline中移除ChannelHandler时被调用
- exceptionCaught：当处理过程中在ChannelPipeline中有错误产生时被调用

Netty定义了下面两个重要的ChannelHandler子接口：
1. ChannelInboundHandler：处理入站数据以及各种状态变化；
2. ChannelOutboundHandler：处理出站数据并且允许拦截所有的操作。

### ChannelInboundHandler接口

- channelRegistered：当Channel已经注册到它的EventLoop并且能够处理I/O时被调用；
- channelUnregistered：当Channel从它的EventLoop注销并且无法处理任何I/O时被调用；
- channelActive：当Channel处于活动状态时被调用；Channel已经连接/绑定并且已经就绪；
- channelInactive：当Channel离开活动状态并且不再连接它的远程节点时被调用；
- channelReadComplete：当Channel上的一个读操作完成时被调用；
- channelRead：当从Channel读取数据时被调用；
- ChannelWritabilityChanged：当Channel的可写状态发生改变时被调用。用户可以确保写操作不会完成得太快（以避免发生OutOfMemoryError）或者可以在Channel变为再次可写时恢复写入。可以通过调用Channel的isWritable()方法来检测Channel的可写性。与可写性相关的阈值可以通过Channel.config().setWriteHighWaterMark()和Channel.config().setWriteLowWaterMark()方法来设置；
- userEventTriggered：当ChannelnboundHandler.fireUserEventTriggered()方法被调用时被调用，因为一个POJO被传经了ChannelPipeline。


ChannelInboundHandler实现在重写channelRead()方法时，负责显式地释放与池化ByteBuf实例。Netty提供了ReferenceCountUtil.release()方法。

同样的，可以使用SimpleChannelInboundHandler类，该类会自动释放资源，因此不应该存储指向任何消息引用供将来使用。

### ChannelOutboundHandler接口

出站操作和数据将由ChannelOutboundHandler处理。它的方法将被Channel、Channel-Pipeline以及ChannelHandlerContext调用。

ChannelOutboundHandler的一个强大的功能是可以按需推迟操作或者事件，这使得可以通过一些复杂的方法来处理请求。

ChannelOutboundHandler的方法
1. bind(ChannelHandlerContext, SocketAddress, ChannelPromise)
2. connect(ChannelHandlerContext, SocketAddress, SocketAddress, ChannelPromise)
3. disconnent(ChannelHandlerContext, ChannelPromise)
4. close(ChannelHandlerContext, ChannelPromise)
5. deregister(ChannelHandlerContext, ChannelPromise)
6. read(ChannelHandlerContext)
7. flush(ChannelHandlerContext)
8. write(ChannelHandlerContext, Object, ChannelPromise)


ChannelPromise与ChannelFuture 

ChannelOutboundHandler中的大部分方法都需要一个ChannelPromise参数，以便在操作完成时得到通知。ChannelPromise是ChannelFuture的一个子类，其定义了一些可写的方法，如setSuccess()和setFailure()，从而使ChannelFuture不可变。

### ChannelHandler适配器

可以使用ChannelInboundHandlerAdapter和ChannelOutboundHandlerAdapter类作为自己的ChannelHandler的起始点。这两个适配器分别提供了ChannelInboundHandler和ChannelOutboundHandler的基本实现。

ChannelHandlerAdapter还提供了实用方法isSharable()。如果其对应的实现被标注为Sharable，那么这个方法将返回true，表示它可以被添加到多个ChannelPipeline中。

在ChannelInboundHandlerAdapter和ChannelOutboundHandlerAdapter中所提供的方法体调用了其相关联的ChannelHandlerContext上的等效方法，从而将事件转发到了ChannelPipeline中的下一个ChannelHandler中。

每当通过调用ChannelInboundHandler.channelRead()或者ChannelOutboundHandler.write()方法来处理数据时，你都需要确保没有任何的资源泄漏。

## ChannelPipeline接口

- ChannelPipeline保存了与Channel相关联的ChannelHandler；
- ChannelPipeline可以根据需要，通过添加或者删除ChannelHandler 来动态地修改；
- ChannelPipeline有着丰富的API用以被调用，以响应入站和出站事件。

## ChannelHandlerContext接口

ChannelHandlerContext代表了ChannelHandler和ChannelPipeline之间的关联，每当有ChannelHandler添加到ChannelPipeline中时，都会创建ChannelHandlerContext。

ChannelHandlerContext的主要功能是管理它所关联的ChannelHandler和在同一个ChannelPipeline中的其他ChannelHandler之间的交互。

# 异常处理

## 处理入站异常

## 处理出站异常

