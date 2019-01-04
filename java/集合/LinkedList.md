# LinkedList

LinkedList是双向链表，可以当做堆栈、队列或双端队列进行操作。

- AbstractSequentialList
- List接口：队列操作接口。
- Deque接口：双端队列操作接口。
- Cloneable接口：覆盖clone()方法，可以克隆。
- Serializable接口：支持序列化。

LinkedList是线程不安全的。

## AbstractSequentialList

实现了get()、set()、add()、remove()函数，提供了随机访问List功能。

## 

LinkedList实现了List接口，包含了get(int location)、remove(int location)等支持根据索引值操作节点的函数。LinkedList是通过计数索引值将双向链表和索引值联系起来的。

当我们调用get(int location)时，首先会比较双向链表长度的中间位置，若location大于中间位置，则从链表尾部开始向前查找，否则从链表头部开始向后查找。


