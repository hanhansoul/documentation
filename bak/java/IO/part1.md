# 字节流

## ByteArrayInputStream

ByteArrayInputStream 是字节数组输入流。它继承于InputStream。

ByteArrayInputStream类中是通过一个内部字节数组保存数据的。

1. 通过ByteArrayInputStream(byte buf[]) 或 ByteArrayInputStream(byte buf[], int offset, int length) ，我们可以将buf数组作为ByteArrayInputStream对象的底层字节数组。
2. read()的作用是从字节流中“读取下一个字节”。
3. read(byte[] buffer, int offset, int length)的作用是从字节流读取字节数据，并写入到字节数组buffer中。offset是将字节写入到buffer的起始位置，length是写入的字节的长度。

	byte[] arr = {};
	ByteArrayInputStream bais = new ByteArrayInputStream(arr);
	
	// 依次读取ByteArrayInputStream对象中的字节数组内容
	while(bais.available() >= 0) {
		int tmp = bais.read();
	}

## ByteArrayOutputStream

ByteArrayOutputStream是字节数组输出流。它继承于OutputStream。

ByteArrayOutputStream 中的数据被写入一个字节数组。缓冲区会随着数据的不断写入而自动增长。可使用toByteArray()和 toString()获取数据。

ByteArrayOutputStream对象将字节数据写入到一个“字节数组”中去。

1. 通过ByteArrayOutputStream()创建的“字节数组输出流”对应的字节数组大小是32。
2. 通过ByteArrayOutputStream(int size)创建“字节数组输出流”，它对应的字节数组大小是size。
3. write(int oneByte)的作用将int类型的oneByte换成byte类型，然后写入到输出流中。
4. write(byte[] buffer, int offset, int len)是将字节数组buffer写入到输出流中，offset是从buffer中读取数据的起始偏移位置，len是读取的长度。
5. writeTo(OutputStream out) 将该“字节数组输出流”的数据全部写入到“输出流out”中。

	ByteArrayOutputStream baos = new ByteArrayOutputStream();
	baos.write(0x41);
	baos.write(0x42);
	baos.write(0x43);

## FileInputStream

FileInputStream 是文件输入流，它继承于InputStream。

通常，我们使用FileInputStream从某个文件中获得输入字节。

## FileOutputStream

FileOutputStream 是文件输出流，它继承于OutputStream。

通常，我们使用FileOutputStream 将数据写入 File 或 FileDescriptor 的输出流。

## FilterInputStream && FilterOutputStream

FilterInputStream的子类：
- BufferedInputStream：为输入流提供缓冲功能，以及mark()和reset()功能。
- DataInputStream：用来装饰其它输入流，允许应用程序将底层字节输入流转换为基本数据类型。

FilterOutputStream的子类：
- BufferedOutputStream：为输出流提供缓冲功能。
- DataOutputStream：用来装饰其它输出流，允许应用程序将底层字节输出流转换为基本数据类型。
- PrintStream：用来装饰其它输出流，为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。

### BufferedInputStream

BufferedInputStream是缓冲输入流。它继承于FilterInputStream，用于为另一个输入流提供缓冲功能。

BufferedInputStream本质上是通过一个内部缓冲区数组实现的。例如，在新建某输入流对应的BufferedInputStream后，当我们通过read()读取输入流的数据时，BufferedInputStream会将该输入流的数据分批的填入到缓冲区中。每当缓冲区中的数据被读完之后，输入流会再次填充数据缓冲区。如此反复，直到我们读完输入流数据位置。

创建BufferedInputStream时，通过构造函数指定某个输入流为参数。BufferedInputStream会将该输入流数据分批读取，每次读取一部分到缓冲中，操作完缓冲中的这部分数据之后，再从输入流中读取下一部分的数据。

缓冲可以提高读写效率。缓冲中的数据实际上是保存在内存中，而原始数据可能是保存在硬盘中；从内存中读取数据效率更高。

第一，读取全部的数据所需要的时间可能会很长。第二，内存价格很贵，容量不像硬盘那么大。