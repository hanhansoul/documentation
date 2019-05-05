# 创建缓冲区

	public abstract class CharBuffer extends Buffer implements CharSequence, Comparable {
		public static CharBuffer allocate (int capacity)
		public static CharBuffer wrap (char[] array)
		public static CharBuffer wrap (char[] array, int offset, int length)
		public final boolean hasArray()
		public final char [] array()
		public final int arrayOffset()
	}

新的缓冲区是由分配或包装操作创建的。分配操作创建一个缓冲区对象并分配一个私有的空间来储存容量大小的数据元素。包装操作创建一个缓冲区对象但是不分配任何空间来储存数据元素。它使用您所提供的数组作为存储空间来储存缓冲区中的数据元素。

相关函数：
- allocate()：Buffer的静态工厂方法，用于在堆中分配给定容量的Buffer。
- wrap()：以一个已有数组作为底层容器创建一个Buffer。

通过allocate()方法，可以分配一个容量为100个char变量的CharBuffer：

	CharBuffer charBuffer = CharBuffer.allocate(100);

通过wrap()方法，可以基于已有char[]数组创建一个CharBuffer：

	char[] myArray = new char[100];
	CharBuffer charbuffer = CharBuffer.wrap(myArray);

## 赋值缓冲区

缓冲区不仅可以管理数组中的外部数据，也能管理其他缓冲区中的外部数据。当一个管理其他缓冲器所包含的数据元素的缓冲器被创建时，这个缓冲器被称为视图缓冲器。大多数的视图缓冲器都是ByteBuffer的视图。在继续前往字节缓冲器的细节之前，我们先将注意力放在所有存储器类型的共同视图上。

相关函数：
- duplicate()：创建了一个与原始缓冲区相似的新缓冲区。两个缓冲区共享数据元素，拥有同样的容量，但每个缓冲区拥有各自的位置，上界和标记属性。对一个缓冲区内的数据元素所做的改变会反映在另外一个缓冲区上。这一副本缓冲区具有与原始缓冲区同样的数据视图。如果原始的缓冲区为只读，或者为直接缓冲区，新的缓冲区将继承这些属性。

## 字节缓冲区

所有非布尔型基本数据类型都有相应的缓冲区类，其中需要特别注意字节缓冲区ByteBuffer。

### 直接缓冲区

字节缓冲区跟其他缓冲区类型最明显的不同在于，它们可以成为通的道源头或目标。通道只接收ByteBuffer作为参数，直接缓冲区被用于与通道和固有I/O程序交互。

若向一个通道中传递一个非直接ByteBuffer对象用于写入，通道可能会在每次调用中隐含地进行下面的操作：

1. 创建一个临时的直接ByteBuffer对象。
2. 将非直接缓冲区的内容复制到临时缓冲中。
3. 使用临时缓冲区执行低层次 I/O 操作。
4. 临时缓冲区对象离开作用域，并最终成为被回收的无用数据。

直接ByteBuffer是通过调用具有所需容量的`ByteBuffer.allocateDirect()`函数产生的，就像我们之前所涉及的allocate()函数一样。注意用一个wrap()函数所创建的被包装的缓冲区总是非直接的。

所有的缓冲区都提供了一个叫做isDirect()的boolean函数，来测试特定缓冲区是否为直接缓冲区。虽然ByteBuffer是唯一可以被直接分配的类型，但如果基础缓冲区是一个直接ByteBuffer，对于非字节视图缓冲区，isDirect()可以是true。

### 视图缓冲区

视图缓冲区通过已存在的缓冲区对象实例的工厂方法来创建。这种视图对象维护它自己的属性，容量，位置，上界和标记，但是和原来的缓冲区共享数据元素。新的缓冲区的容量是字节缓冲区中存在的元素数量除以视图类型中组成一个数据类型的字节数。在切分中任一个超过上界的元素对于这个视图缓冲区都是不可见的。视图缓冲区的第一个元素从创建它的ByteBuffer对象的位置开始(positon()函数的返回值)。具有能被自然数整除的数据元素个数的视图缓冲区是一种较好的实现。

	public abstract class ByteBuffer extends Buffer implements Comparable
	{
		public abstract CharBuffer asCharBuffer();
		public abstract ShortBuffer asShortBuffer();
		public abstract IntBuffer asIntBuffer();
		public abstract LongBuffer asLongBuffer();
		public abstract FloatBuffer asFloatBuffer();
		public abstract DoubleBuffer asDoubleBuffer();
	}

### 数据元素视图

ByteBuffer类提供了一个不太重要的机制来以多字节数据类型的形式存取byte数据组。