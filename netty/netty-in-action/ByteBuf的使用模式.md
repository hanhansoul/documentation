### ByteBuf的使用模式

#### 堆缓冲区

#### 直接缓冲区

#### 复合缓冲区

## 字节级操作

### 随机访问索引

### 顺序访问索引

### 可丢弃字节

	discardReadBytes()

### 可读字节

	readBytes(ByteBuf dest);

### 可写字节

	writeBytes(ByteBuf dest);

### 索引管理

	readerIndex(int)
	writerIndex(int)
	clear()

### 查找操作

### 派生缓冲区

派生缓冲区为ByteBuf 提供了以专门的方式来呈现其内容的视图。

- duplicate()
- slice()
- slice(int, int)
- Unpooled.unmodifiableBuffer()
- order(ByteOrder)
- readSlice(int)

### 读/写操作

1. get()和set()操作，从给定的索引开始，并且保持索引不变；
2. read()和write()操作，从给定的索引开始，并且会根据已经访问过的字节数对索
引进行调整。

## ByteBuf分配

### 按需分配：ByteBufAllocator接口

PooledByteBufAllocator
UnpooledByte

### Unpooled缓冲区

### ByteBufUtil类

