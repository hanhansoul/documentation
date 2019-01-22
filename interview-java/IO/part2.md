## DataInputStream

DataInputStream是数据输入流。它继承于FilterInputStream。
DataInputStream用于装饰其它输入流，为应用程序提供将底层字节输入流中转换为读取基本 数据类型的功能。应用程序可以使用DataOutputStream(数据输出流)写入由DataInputStream(数据输入流)读取的数据。

DataInputStream 中比较难以理解的函数就只有 readUTF(DataInput in)；下面，对这个函数进行详细的介绍，其它的函数请参考源码中的注释。

## DataOutputStream与PrintStream区别

相同点：都是继承与FileOutputStream，用于包装其它输出流。

不同点：

1. PrintStream和DataOutputStream都可以将数据格式化输出；但它们在“输出字符串”时的编码不同。PrintStream是输出时采用的是用户指定的编码，若没有指定，则采用系统默认的字符编码。而DataOutputStream则采用的是UTF-8。 

2. 它们的写入数据时的异常处理机制不同。DataOutputStream在通过write()向“输出流”中写入数据时，若产生IOException，会抛出。PrintStream在通过write()向“输出流”中写入数据时，不会抛出异常。

3. 构造函数不同。DataOutputStream的构造函数只有一个：DataOutputStream(OutputStream out)。即它只支持以输出流out作为“DataOutputStream的输出流”。而PrintStream的构造函数有许多：和DataOutputStream一样，支持以输出流out作为“PrintStream输出流”的构造函数；还支持以“File对象”或者“String类型的文件名对象”的构造函数。而且，在PrintStream的构造函数中，能“指定字符集”和“是否支持自动flush()操作”。

4. 目的不同。DataOutputStream的作用是装饰其它的输出流，它和DataInputStream配合使用：允许应用程序以与机器无关的方式从底层输入流中读写java数据类型。而PrintStream的作用虽然也是装饰其他输出流，但是它的目的是为其它输出流提供打印各种数据值表示形式，使其它输出流能方便的通过print(), println()或printf()等输出各种格式的数据。