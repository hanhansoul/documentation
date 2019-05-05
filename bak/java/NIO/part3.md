# 通道

对所有通道来说只有两种共同的操作：检查一个通道是否打开(isOpen())和关闭一个打开的通道(close())。

### 打开通道

通道是访问I/O服务的导管。I/O可以分为广义的两大类别：
1. File I/O
2. Stream I/O

同样的，通道也分为两大类
1. 文件通道
2. 套接字通道

- FileChannel
- SocketChannel
- ServerSocketChannel
- DatagramChannel

套接字通道可以直接通过套接字通道工厂方法创建。

FileChannel对象却只能通过在一个打开的RandomAccessFile、FileInputStream或FileOutputStream对象上调用getChannel()方法来获取。

### 使用通道

通道可以是单向或者双向的。一个channel类可能实现定义read()方法的ReadableByteChannel接口，而另一个channel类实现了WritableByteChannel接口以提供write()方法。实现这两种接口其中之一的类都是单向的，只能在一个方向上传输数据。如果一个类同时实现这两个接口，那么它是双向的，可以双向传输数据。

ByteChannel接口同时继承了ReadableByteChannel和WritableByteChannel接口。

ByteChannel的read()和write()方法使用ByteBuffer对象作为参数。两种方法均返回已传输的字节数，可能比缓冲区的字节数少甚至可能为零。缓冲区的position也会发生与已传输字节相同数量的前移。如果只进行了部分传输，缓冲区可以被重新提交给通道并从上次中断的地方继续传输。该过程重复进行直到缓冲区的hasRemaining()方法返回false值。

通道可以以阻塞(blocking)或非阻塞(nonblocking)模式运行。非阻塞模式的通道永远不会让调用的线程休眠。请求的操作要么立即完成，要么返回一个结果表明未进行任何操作。只有面向流的通道，如sockets和pipes才能使用非阻塞模式。

socket通道类从SelectableChannel引申而来。从SelectableChannel引申而来的类可以和支持有条件的选择器(Selectors)一起使用。将非阻塞I/O和选择器组合起来可以使您的程序利用多路复用I/O。

### 关闭通道

通道不能被重复使用。一个打开的通道即代表与一个特定I/O服务的特定连接并封装该连接的状态。当通道关闭时，那个连接会丢失，然后通道将不再连接任何东西。

### Scatter/Gather

Gather：将多个缓冲区内容输入到同一个通道。
Scatter：一个通道连接多个缓冲区作为输出。

// TO-DO

## 文件通道

我们在3.1.1节中提到，FileChannel对象不能直接创建。一个FileChannel实例只能通过在一个打开的file对象(RandomAccessFile、FileInputStream或FileOutputStream)上调用getChannel()方法获取。调用getChannel()方法会返回一个连接到相同文件的FileChannel对象且该FileChannel对象具有与file对象相同的访问权限。

### 访问文件

	public abstract class FileChannel extends AbstractChannel 
		implements ByteChannel, GatheringByteChannel, ScatteringByteChannel {
	    // 这里仅列出部分API
	    public abstract long position()
	    public abstract void position(long newPosition)
	    public abstract int read(ByteBuffer dst)
	    public abstract int read(ByteBuffer dst, long position)
	    public abstract int write(ByteBuffer src)
	    public abstract int write(ByteBuffer src, long position)
	    public abstract long size()
	    public abstract void truncate(long size)
	    public abstract void force(boolean metaData)
	}

每个FileChannel对象都同一个文件描述符(file descriptor)有一对一的关系。

同底层的文件描述符一样，每个FileChannel都有一个文件position。这个position值决定文件中哪一处的数据接下来将被读或者写。

### 文件锁定

// TO-DO

### 内存映射文件

FileChannel类提供了map()方法，用于在一个打开的文件和一个特殊类型的ByteBuffer之间建立一个虚拟内存映射。

在FileChannel上调用map()方法会创建一个由磁盘文件支持的虚拟内存映射并在那块虚拟内存空间外部封装一个MappedByteBuffer对象。MappedByteBuffer对象的行为在多数方面类似一个基于内存的缓冲区，只不过该对象的数据元素存储在磁盘上的一个文件中。

通过内存映射机制来访问一个文件会比使用常规方法读写高效得多，甚至比使用通道的效率都高。

// TO-DO

### 通道间数据传递

从一个位置将文件数据批量传输到另一个位置，FileChannel类添加了一些优化方法来提高该传输过程的效率。

- transferTo()
- transferFrom()

transferTo()和transferFrom()方法允许将一个通道与另一个通道直接连接，而不需要通过一个中间缓冲区来传递数据。只有FileChannel类有这两个方法，因此通道间数据传递中的一个通道必须是FileChannel。不能在socket通道之间直接传输数据，不过socket通道实现WritableByteChannel和ReadableByteChannel接口，因此文件的内容可以用transferTo()方法传输给一个socket通道，或者也可以用transferFrom()方法将数据从一个socket通道直接读取到一个文件中。

## socket通道

- ScoketChannel：对应Socket
- DatagramChannel：对应DatagramChannel类。
- ServerSocketChannel：负责监听传入的连接和创建新的SocketChannel对象，本身从不传输数据。对应ServerSocket类。

### 非阻塞模式

Socket通道可以在非阻塞模式下运行。

要把一个socket通道置于非阻塞模式，我们要依靠所有socket通道类的公有超级类：SelectableChannel。

相关函数：
- configureBlocking()：设置通道为阻塞或非阻塞模式。
- isBlocking()：判断通道为阻塞或非阻塞模式。
- blockingLock()：确保在代码的关键部分时socket通道的阻塞模式不会改变以及在不影响其他线程的前提下暂时改变阻塞模式。

有条件选择是一种可以用来查询通道的机制，该查询可以判断通道是否准备好执行一个目标操作，如读或写。非阻塞I/O和可选择性是紧密相连的，这也正是管理阻塞模式的API代码要在SelectableChannel超级类中定义的原因。

### ServerSocketChannel

	public abstract class ServerSocketChannel extends AbstractSelectableChannel {
	    public static ServerSocketChannel open() throws IOException
	    public abstract ServerSocket socket();
	    public abstract ServerSocket accept() throws IOException;
	    public final int validOps()
	}

- open()：静态工厂方法open()创建一个新的ServerSocketChannel对象，并范湖同一个未绑定的ServerSocket关联通道。
- socket()：返回ServerSocketChannel对象关联的ServerSocket对象。需要返回的ServerSocket对象调用bind()方法绑定IP地址与端口号。
- accept()：返回SocketChannel类型的对象，返回的对象能够在非阻塞模式下运行。

如何使用一个非阻塞的accept()方法如下所示：

	public class ChannelAccept {
	    public static final String GREETING = "Hello I must be going.\r\n";
	    
	    public static void main (String [] argv) throws Exception {
	        int port = 1234; //默认端口
	        if (argv.length > 0) {
	            port = Integer.parseInt(argv[0]);
	        }
	        ByteBuffer buffer = ByteBuffer.wrap (GREETING.getBytes());
	        ServerSocketChannel ssc = ServerSocketChannel.open();
	        ssc.socket().bind (new InetSocketAddress(port));
	        ssc.configureBlocking(false);
	        while(true) {
	            System.out.println ("Waiting for connections");
	            SocketChannel sc = ssc.accept();
	            if (sc == null) {
	                // no connections, snooze a while
	                Thread.sleep (2000);
	            } else {
	                System.out.println ("Incoming connection from: " + sc.socket().getRemoteSocketAddress());
	                buffer.rewind();
	                sc.write(buffer);
	                sc.close();
	            }
	        }
	    }
	}

### SocketChannel

	public abstract class SocketChannel extends AbstractSelectableChannel implements ByteChannel, ScatteringByteChannel, GatheringByteChannel {
		// 这里仅列出部分API
		public static SocketChannel open() throws IOException
		public static SocketChannel open(InetSocketAddress remote) throws IOException
		public abstract Socket socket();
		public abstract boolean connect (SocketAddress remote) throws IOException;
		public abstract boolean isConnectionPending();
		public abstract boolean finishConnect() throws IOException;
		public abstract boolean isConnected();
		public final int validOps()
	}

- open()：静态工厂方法用于创建一个新的SocketChannel对象。
- socket()：返回SocketChannel对象上关联的Socket对象，而该Socket对象调用getChannel()则返回关联的SocketChannel对象。
- connect()：连接Socket。
- finishConnect()：完成连接过程，该方法任何时候都可以安全地进行调用。


每个SocketChannel对象创建时都是同一个对等的java.net.Socket对象串联的。静态的open()方法可以创建一个新的SocketChannel对象，而在新创建的SocketChannel上调用socket()方法能返回它对等的Socket对象；在该Socket上调用getChannel()方法则能返回最初的那个SocketChannel。

## pipes

// TO-DO

