## ChannelHandler

### Channel生命周期

Channel生命周期的四种状态

- ChannelUnregistered：Channel已经被创建，未被注册到EventLoop；
- ChannelRegistered：Channel已经被注册到EventLoop；
- ChannelActive：Channel处于活动状态，它现在可以接收和发送数据了；
- ChannelInactive：Channel没有连接到远程节点。

### ChannelHandler 的生命周期

ChannelHandler的生命周期方法

- handlerAdded：当把ChannelHandler添加到ChannelPipeline中时被调用；
- handlerRemoved：当从ChannelPipeline中移除ChannelHandler时被调用；
- exceptionCaught：当处理过程中在ChannelPipeline 中有错误产生时被调用。

Netty定义了下面两个重要的ChannelHandler子接口：

- ChannelInboundHandler：处理入站数据以及各种状态变化；
- ChannelOutboundHandler：处理出站数据并且允许拦截所有的操作。