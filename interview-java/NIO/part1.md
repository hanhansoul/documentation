http://www.365mini.com/page/java-nio-course-3.htm

## 缓冲区操作

缓冲区，以及缓冲区如何工作，是所有I/O的基础。所谓[输入/输出]讲的无非就是把数据移进或移出缓冲区。

进程执行I/O操作就是向操作系统发出请求，让它要么把缓冲区里的数据排干(写)，要么用数据把缓冲区填满(读)。

## 用户空间和内核空间

- 用户空间是常规进程所在区域。JVM就是常规进程，驻守于用户空间。用户空间是非特权区域：比如，在该区域执行的代码就不能直接访问硬件设备。

- 内核空间是操作系统所在区域。内核代码有特别的权力：它能与设备控制器通讯，控制着用户区域进程的运行状态。最重要的是，所有I/O都直接或间接通过内核空间。当进程请求I/O操作的时候，它执行一个系统调用将控制权移交给内核。当内核以这种方式被调用，它随即采取任何必要步骤，找到进程所需数据，并把数据传送到用户空间内的指定缓冲区。内核试图对数据进行高速缓存或预读取，因此进程所需数据可能已经在内核空间里了。如果是这样，该数据只需简单地拷贝出来即可。如果数据不在内核空间，则进程被挂起，内核着手把数据读进内存。

## 虚拟内存

虚拟内存意为使用虚拟地址取代物理内存地址。这样做有两个好处： 
1. 一个以上的虚拟地址可指向同一个物理内存地址。
2. 虚拟内存空间可大于实际可用的硬件内存。

## 内存页面调度

为了支持虚拟内存的第二个特性(寻址空间大于物理内存)，就必须进行虚拟内存分页(经常称为交换，虽然真正的交换是在进程层面完成，而非页层面)。

// TO-DO

# 缓冲区

一个Buffer对象是固定数量的数据的容器。其作用是一个存储器，或者分段运输区，在这里数据可被存储并在之后用于检索。

对于每种非布尔原始数据类型都有一个缓冲区类。尽管缓冲区作用于它们存储的原始数据类型，但缓冲区十分倾向于处理字节。非字节缓冲区可以在后台执行字节与非字节间的互相转换，这取决于缓冲区是如何创建的。

缓冲区Buffer是与通道Channel紧密联系的。Channel是数据传输的出入口，而Buffer是数据传输的来源或目标。对于离开Buffer的数据，Buffer中数据会被传送到Channel。对于到达Buffer的数据，一个Channel将数据放置所提供的BUffer中。

- Buffer
	- ByteBuffer
		- MappedByteBuffer
	- CharBuffer
	- IntBuffer
	- DoubleBuffer
	- ShortBuffer
	- LongBuffer
	- FloatBuffer

缓冲区属性
- 容量（capacity）：缓冲区能够容纳的数据元素的最大数量。
- 上界（limit）：缓冲区的第一个不能被读或写的元素。
- 位置（position）：下一个要被读或写的元素的索引。
- 标记（mark）：一个备忘位置。

缓冲区属性满足：

	0 <= mark <= position <= limit <= capacity

缓冲区API：

	package java.nio;
	public abstract class Buffer {
	    public final int capacity();
	    public final int position();
	    public final Buffer position (int newPosition);
	    public final int limit();
	    public final Buffer limit (int newLimit);
	    public final Buffer mark();
	    public final Buffer reset();
	    public final Buffer clear();
	    public final Buffer flip();
	    public final Buffer rewind();
	    public final int remaining();
	    public final boolean hasRemaining();
	    public abstract boolean isReadOnly();
	}

## 存取

Buffer保存固定数量的数据元素，需要通过位置属性position确定元素添加时的位置与元素检索时的位置。

Buffer通过get()方法返回缓冲区中指定位置的数据元素，通过put()方法向缓冲区中指定位置添加元素。

不同的Buffer类，get()和put()方法接口的参数类型并不相同，如ByteBuffer中这两个方法声明如下：

	public abstract class ByteBuffer 
		extends Buffer 
		implements Comparable {

		// 返回position位置的数据，之后position递增1。
		public abstract byte get();
		// 返回index位置的数据。
		public abstract byte get (int index);
		// 向position位置添加数据b，之后position递增1。
		public abstract ByteBuffer put (byte b);
		// 向index位置添加数据b。
		public abstract ByteBuffer put (int index, byte b);
	}

## 填充

	buffer.put((byte)'H').put((byte)'e').put((byte)'l').put((byte)'l').put((byte)'o');

因为我们存放的是字节而不是字符。java内部字符都是以Unicode码表示的，每个Unicode字符占16位。本章节的例子使用包含ascii字符集数值的字节。通过将char强制转换为byte，我们删除了前八位来建立一个八位字节值。这通常只适合于拉丁字符而不能适合所有可能的 Unicode字符。为了让事情简化，我们暂时故意忽略字符集的映射问题。

## 翻转

相关函数：
- flip()：使Buffer进入数据读取状态。
- rewind()：重新读取已经读取过的内容。

当需要将Buffer数据传递给一个Channel时，需要调用flip()方法重新设定position与limit的值，以便能够将确定Buffer可用数据的索引范围。在调用flip()方法后，可知Buffer中可用数据的范围为[position, limit)。

	buffer.flip();

等价于

	buffer.limit(buffer.position()).position(0);

flip()函数将一个能够添加数据的填充数据状态的Buffer翻转成一个准备读出元素的释放状态。

rewind()函数与flip()相似，但不影响limit，而只将position设回0。通过rewind()可以重读已经被翻转的缓冲区中的数据。

## 释放

相关函数：
- hasRemaining()：释放缓冲区时是否达到缓冲区的上界，即`position != limit`。
- remaining()：返回从position当前位置到limit，即`limit - position`。
- clear()：将缓冲区重置为空状态。并不改变缓冲区中的数据元素，而是将limit设为capacity，并把position设为0。

	for (int i = 0; buffer.hasRemaining(), i++) {
	    myByteArray[i] = buffer.get();
	}

缓冲区不是线程安全的。如果想以多线程同时存取特定的缓冲区，需要在存取缓冲区之前进行同步。

一旦缓冲区对象完成填充并释放，它就可以被重新使用了。

## 压缩

相关函数：
- compact()：仅从Buffer中释放一部分数据，然后Buffer重新进入填充状态。未读取的数据元素会被移动至Buffer的初始位置，position将重置为下一个数据填充位置，limit将设为capacity。

## 标记

相关函数：
- mark()：调用时将标记设置为当前position位置。
- reset()：设置标记后，调用reset()后position将设为标记位置值。

rewind()、clear()与flip()等函数会抛弃标记。如果新设定的值比当前的标记小，调用limit()或 position()带有索引参数的版本会抛弃标记。

## 比较

需要比较两个缓冲区所包含的数据是否相等时，所有的Buffer都提供了一个常规的equals()函数用以测试两个缓冲区的是否相等，以及一个compareTo()函数用以比较缓冲区。

	if (buffer1.equals(buffer2)) {
	    doSomething();
	}

两个缓冲区被认为相等的充要条件是：

- 两个对象类型相同。包含不同数据类型的buffer永远不会相等，而且buffer绝不会等于非buffer对象。
- 两个对象都剩余同样数量的元素。Buffer的容量不需要相同，而且缓冲区中剩余数据的索引也不必相同。但每个缓冲区中剩余元素的数目(从position到limit)必须相同。说明了两个属性不同的缓冲区也可以相等。
- 在每个缓冲区中应被get()函数返回的剩余数据元素序列必须一致。

如果不满足以上任意条件，就会返回false。

缓冲区也支持用compareTo()函数以词典顺序进行比较。

## 批量移动

Buffer类提供了批量移动数据元素的函数。如CharBuffer所示：

	public abstract class CharBuffer extends Buffer implements CharSequence, Comparable
	{
		public CharBuffer get (char [] dst)
		public CharBuffer get (char [] dst, int offset, int length)

		public final CharBuffer put (char[] src)
		public CharBuffer put (char [] src, int offset, int length)
		public CharBuffer put (CharBuffer src)
		public final CharBuffer put (String src)
		public CharBuffer put (String src, int start, int end)
	}