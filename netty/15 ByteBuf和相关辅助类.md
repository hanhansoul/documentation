# ByteBuf和相关辅助类

## ByteBuf功能说明

在数据传输时，最常用的数据缓冲区时NIO提供的Buffer类，除Boolean类型外的其他基础类型都有相应的缓冲区实现。

NIO常用的是ByteBuffer缓冲类，完全能够满足NIO编程需要，但也有其局限性。

1. ByteBuffer长度固定，一旦分配完成，容量不能动态扩展或收缩，当对象大于ByteBuffer容量时，会抛出越界异常；
2. ByteBuffer只有一个标识位置的指针position，读写时需要手工调用flip()和rewind()等方法；
3. ByteBuffer的API功能有限，不支持某些高级特性。

## ByteBuf的工作原理

ByteBuf是一个Byte数组缓冲区，基本功能与ByteBuffer一致，提供以下基本功能：
1. 7中java基本类型、byte数组、ByteBuffer等的读写；
2. 缓冲区自身的copy和slice功能；
3. 设置网络字节序；
4. 构造缓冲区实例
5. 操作位置指针方法。

ByteBuf通过两个位置指针来协助缓冲区的读写操作，读操作使用readerIndex，写操作使用writerIndex。

动态扩展

### ByteBuf功能介绍

#### 顺序读操作

ByteBuf的read操作类似于ByteBuffer的get操作。

## ByteBuf源码分析

从内存分配的角度来看，ByteBuf可以分为两类。

1. 堆内存字节缓冲区：特点是内存的分配和回收速度快，可以被JVM自动回收；缺点是如果进行socket的读写操作，需要额外依次内存复制，将堆内存对应的缓冲区复制到内核Channel中。
2. 直接内存字节缓冲区：非堆内存，在堆外进行内存分配，相比于堆内存，分配和回收速度会慢一些，但将它写入或从Channel中读取时，由于少一次内存复制，速度比堆内存快。

ByteBuf最佳实践表明，在I/O通信线程的读写缓冲区使用DirectByteBuf，后端业务消息的编解码模块使用HeapByteBuf，能够使性能达到最优。

从内存回收角度看，ByteBuf也分为两类：
1. 基于对象池的ByteBuf：可以重用ByteBuf对象，自己维护一个内存池，循环利用创建的ByteBuf，提高内存使用效率，降低由于高负载导致的频繁GC。但是管理与维护更加复杂。
2. 普通ByteBuf。

### AbstractByteBuf

### AbstractReferenceCountedByteBuf

对引用进行计数，类似于JVM内存回收的对象引用计数器，用于跟踪对象的分配和销毁，做自动内存回收。

### UnpooledHeapByteBuf

基于堆内存进行内存分配的字节缓冲区，没有基于对象池技术实现，每次I/O操作都会创建一个新的UnpooledHeapByteBuf，相比于堆外内存的申请与释放，成本会低一些。

1. ByteBufAllocator：内存分配；
2. byte[]：缓冲区；
3. ByteBuffer：实现ByteBuf到ByteBuffer的转换。



## Facade设计模式

// TODO