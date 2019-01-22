# 选择器

- 选择器：选择器类管理着一个被注册的通道集合的信息和它们的就绪状态。通道是和选择器一起被注册的，并且使用选择器来更新通道的就绪状态。
- 可选择通道：这个抽象类提供了实现通道的可选择性所需要的公共方法。它是所有支持就绪检查的通道类的父类。FileChannel是不可选择的，而套接字通道是可选择的。
- 选择键：选择键封装了特定的通道与特定的选择器的注册关系。

相关函数：
- register()：将通道注册到一个选择器上，在通道被注册到一个选择器上之前，必须先设置为非阻塞模式。


SelectionKey：
- OP_READ
- OP_WRITE
- OP_CONNECT
- OP_ACCEPT

## 建立选择器

创建了一个新的选择器，然后将三个已存在的socket通道注册到选择器上，每个通道注册的选择键各不相同。

	Selector selector = Selector.open();
	channel1.register(selector, SelectionKey.OP_READ);
	channel2.register(selector, SelectionKey.OP_WRITE);
	channel3.register(selector, SelectionKey.OP_READ | SelectionKey.OP_WRITE);
	readyCount = selector.select(10000);

相关函数：
- open()：Selector对象通过今天工厂方法open()实例化。
- close()：关闭选择器。

## 使用选择器

### 选择过程

Selector对象维护三个键的集合：
- 已注册的键的集合key()：与选择器关联的已经注册的键的集合，但并不是所有注册过的键都仍然有效。
- 已选择的键的集合selectedKeys()：已注册的键的集合的子集。这个集合的每个成员都是相关的通道被选择器(在前一个选择操作中)判断为已经准备好的，并且包含于键的interest集合中的操作。
- 已取消的键的集合：已注册的键的集合的子集，这个集合包含了cancel()方法被调用过的键(这个键已经被无效化)，但它们还没有被注销。私有成员，无法直接访问。