ByteBuf

下面是一些ByteBuf API的优点：
- 它可以被用户自定义的缓冲区类型扩展；
- 通过内置的复合缓冲区类型实现了透明的零拷贝；
- 容量可以按需增长（类似于StringBuilder）；
- 在读和写这两种模式之间切换不需要调用ByteBuffer的flip()方法；
- 读和写使用了不同的索引；
- 支持方法的链式调用；
- 支持引用计数；
- 支持池化。

## 

ByteBuf维护两个不同的索引，一个用于读取，一个用于写入。

## ByteBuf的模式

### 堆缓冲区

ByteBuf将数据存储在JVM的堆空间中，这种模式被称为支撑数组。

### 直接缓冲区

ByteBuffer类允许JVM实现通过本地调用来分配内存。这主要是为了避免在每次调用本地I/O 操作之前（或者之后）将缓冲区的内容复制到一个中间缓冲区。

直接缓冲区的主要缺点是，相对于基于堆的缓冲区，它们的分配和释放都较为昂贵。

### 复合缓冲区

为多个ByteBuf提供了一个聚合视图CompositeByteBuf。

NIO中聚合多个ByteBuffer时需要分配与复制操作，效率低下而且笨拙。

## 派生缓冲区

派生缓冲区为ByteBuf提供了以专门的方式来呈现其内容的视图。

## ByteBufHolder

## ByteBuf分配

### 按需分配：ByteBufAllocator接口

### Unpooled缓冲区

Unpooled工具类，提供了静态的辅助方法用于创建未池化的ByteBuf实例。

### ByteBufUtil类

ByteBufUtil类提供了用于操作ByteBuf的静态辅助方法。