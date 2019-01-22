# Queue

Queue是队列接口。

- add()：添加元素。
- offer()：添加元素，同add()
- element()：返回队列第一个元素
- peek()：返回队列第一个元素，同element()
- remove()：删除队列第一个元素
- poll()：返回并删除队列第一个元素

## Deque

Deque是双端队列接口，继承了Queue，能够为栈或队列提供API接口。

- 添加元素
	- add()
	- offer()
	- push()
	- addFirst()
	- addLast()
	- offerFirst()
	- offerLast()
- 获取元素
	- peek()
	- element()
	- getFirst()
	- getLast()
	- peekFirst()
	- peekLast()
- 删除元素
	- pop()
	- remove()
	- poll()
	- removeFirst()
	- removeLast()
	- pollFirst()
	- pollLast()
- 删除元素
	- removeFirstOccurrence()
	- removeLastOccurrence()

实现了Deque接口的队列类：

- LinkedList
- ArrayDeque
- BlockingDeque
- ConcurrentLinkedDeque

实现了AbstractQueue接口的队列

- ArrayBlockingQueue
- LinkedBlockingDeque
- LinkedBlockingQueue
- ConcurrentLinkedQueue
- PriorityBlockingQueue
- PriorityQueue

1、没有实现的阻塞接口的LinkedList： 实现了java.util.Queue接口和java.util.AbstractQueue接口
　　内置的不阻塞队列： PriorityQueue 和 ConcurrentLinkedQueue
　　PriorityQueue 和 ConcurrentLinkedQueue 类在 Collection Framework 中加入两个具体集合实现。 
　　PriorityQueue 类实质上维护了一个有序列表。加入到 Queue 中的元素根据它们的天然排序（通过其 java.util.Comparable 实现）或者根据传递给构造函数的 java.util.Comparator 实现来定位。
　　ConcurrentLinkedQueue 是基于链接节点的、线程安全的队列。并发访问不需要同步。因为它在队列的尾部添加元素并从头部删除它们，所以只要不需要知道队列的大 小，　　　　    　　ConcurrentLinkedQueue 对公共集合的共享访问就可以工作得很好。收集关于队列大小的信息会很慢，需要遍历队列。


2)实现阻塞接口的：
　　java.util.concurrent 中加入了 BlockingQueue 接口和五个阻塞队列类。它实质上就是一种带有一点扭曲的 FIFO 数据结构。不是立即从队列中添加或者删除元素，线程执行操作阻塞，直到有空间或者元素可用。
五个队列所提供的各有不同：
　　* ArrayBlockingQueue ：一个由数组支持的有界队列。
　　* LinkedBlockingQueue ：一个由链接节点支持的可选有界队列。
　　* PriorityBlockingQueue ：一个由优先级堆支持的无界优先级队列。
　　* DelayQueue ：一个由优先级堆支持的、基于时间的调度队列。
　　* SynchronousQueue ：一个利用 BlockingQueue 接口的简单聚集（rendezvous）机制。

## 非并发队列

### LinkedList

LinkedList是一个基于链表的双端队列，内部通过Node链表节点保存数据。

LinkedList继承了Deque接口。

### ArrayDeque

ArrayDeque是一个基于动态数组实现的双端队列，内部通过一个Object[]数组存储数据。

ArrayDeque是线程不安全的。

ArrayDeque的默认初始容量为16，每次扩容为原来的2倍。

ArrayDeque的容量必须为2^n。

ArrayDeque内部的数组队列是环形的，通过head和tail确定元素位置。

ArrayDeque不支持null元素。

## PriorityQueue

PriorityQueue是一个基于二叉堆实现的优先队列，内部通过一个Object[]动态数组保存数据。

## 并发队列

并发队列中又分为阻塞队列与非阻塞队列。

非阻塞队列：
- ConcurrentLinkedQueue
- ConcurrentLinkedDeque

阻塞队列：
- BlockingQueue：阻塞队列接口
- BlockingDeque：阻塞双端队列接口
- ArrayBlockingQueue
- LinkedBlockingQueue
- LinkedBlockingDeque
- PriorityBlockingQueue

### ConcurrentLinkedQueue

ConcurrentLinkedQueue是线程安全队列，是一个基于链表的无界线程安全的FIFO队列，队列元素中不可以放置null元素。

ConcurrentLinkedQueue是通过volatile来实现多线程对竞争资源的互斥访问的。

### ConcurrentLinkedDeque

ConcurrentLinkedQueue是线程安全双端队列。

## PriorityBlockingQueue

BlockingDeque是双端阻塞队列。


