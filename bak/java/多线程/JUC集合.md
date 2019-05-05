## volatile关键字

http://www.importnew.com/18126.html

https://www.ibm.com/developerworks/cn/java/j-jtp06197.html

http://www.importnew.com/23520.html

volatile
1. 内存可见性
2. 禁止指令重排序

并发编程三个概念
1. 原子性
2. 可见性
3. 有序性

## CopyOnWriteArrayList

CopyOnWriteArrayList相当于线程安全的ArrayList，通过volatile数组与ReentrantLock锁实现线程安全。具有以下特性：
1. 与ArrayList类似，是可变数组。
2. ArrayList是线程不安全的，CopyOnWriteArrayList是线程安全的。
3. CopyOnWriteArrayList适用的场景：List大小通常保持很小，只读操作远多于修改操作，需要保证线程安全。
3. 修改操作性能：CopyOnWriteArrayList修改操作通常需要复制整个底层数组，因此修改操作，如add()、set()和remove()等方法的开销很大。
4. CopyOnWriteArrayList迭代器：迭代器支持hasNext(), next()等不可变操作，但不支持可变 remove()等操作。
5. CopyOnWriteArrayList迭代器性能：使用迭代器进行遍历的速度很快，并且是线程安全的。在构造迭代器时，迭代器依赖于不变的数组快照。
6. CopyOnWriteArrayList返回迭代器不会抛出ConcurrentModificationException异常，即它不是fail-fast机制的。

### 实现原理

**CopyOnWriteArrayList的“动态数组”机制**

CopyOnWriteArrayList内部有个“volatile数组”(array)来保持数据。

在“添加/修改/删除”数据时，会新建一个数组，并将更新后的数据拷贝到新建的数组中，最后再将该数组赋值给“volatile数组”。这就是它叫做CopyOnWriteArrayList的原因。

CopyOnWriteArrayList就是通过这种方式实现的动态数组；由于它在修改操作时都会新建一个数组，所以涉及到修改数据的操作，CopyOnWriteArrayList效率很低；但是单单只是进行遍历查找的话，效率比较高。

**CopyOnWriteArrayList的“线程安全”机制**

CopyOnWriteArrayList的线程安全特性是通过volatile和互斥锁来实现的。

1. CopyOnWriteArrayList是通过“volatile数组”来保存数据的。一个线程读取volatile数组时，总能看到其它线程对该volatile变量最后的写入，通过volatile提供了“读取到的数据总是最新的”这个机制的保证。

2. CopyOnWriteArrayList通过互斥锁来保护数据。在“添加/修改/删除”数据时，会先“获取互斥锁”，再修改完毕之后，先将数据更新到“volatile数组”中，然后再“释放互斥锁”；这样，就达到了保护数据的目的。 

## CopyOnWriteArraySet

CopyOnWriteArraySet相当于线程安全的HashSet。

CopyOnWriteArraySet具有以下特性：

1. CopyOnWriteArraySet和HashSet都继承于共同的父类AbstractSet，HashSet是通过“散列表(HashMap)”实现的，而CopyOnWriteArraySet则是通过“动态数组(CopyOnWriteArrayList)”实现的。
2. HashSet是线程不安全的，CopyOnWriteArraySet是线程安全的。
3. CopyOnWriteArraySet的适用场景：Set大小通常保持很小，只读操作远多于修改操作，需要保证线程安全。
3. 修改操作性能：因为通常需要复制整个基础数组，所以修改操作，如add()、set()和remove()等方法的开销很大。
4. CopyOnWriteArraySet迭代器：迭代器支持hasNext(), next()等不可变操作，但不支持可变remove()操作。
5. CopyOnWriteArraySet迭代器性能：使用迭代器进行遍历的速度很快，并且是线程安全的。在构造迭代器时，迭代器依赖于不变的数组快照。
6. CopyOnWriteArrayList返回迭代器不会抛出ConcurrentModificationException异常，即它不是fail-fast机制的。

### 实现原理

CopyOnWriteArraySet包含CopyOnWriteArrayList对象，它是通过CopyOnWriteArrayList实现的。而CopyOnWriteArrayList本质是个动态数组队列，所以CopyOnWriteArraySet相当于通过通过动态数组实现的“集合”！。

CopyOnWriteArrayList中允许有重复的元素，而CopyOnWriteArraySet是不包含重复元素集合。因此CopyOnWriteArraySet通过CopyOnWriteArrayList提供的addIfAbsent()和addAllAbsent()实现只有当元素不存在时才执行添加操作。

CopyOnWriteArraySet的“线程安全”机制，通过CopyOnWriteArrayList的volatile和互斥锁实现的。

## ConcurrentHashMap

ConcurrentHashMap相当于线程安全的HashMap。HashMap, Hashtable, ConcurrentHashMap之间的关联如下：

1. HashMap是非线程安全的哈希表，常用于单线程程序中。

2. Hashtable是线程安全的哈希表，通过synchronized来保证线程安全的，因此在线程竞争激烈时，效率比较低，因为当一个线程访问Hashtable的同步方法时，其它线程就访问Hashtable的同步方法时，可能会进入阻塞状态。

3. ConcurrentHashMap是线程安全的哈希表，它是通过“锁分段”来保证线程安全的。ConcurrentHashMap将哈希表分成许多片段(Segment)，每一个片段除了保存哈希表之外，本质上也是一个“可重入的互斥锁”(ReentrantLock)。多线程对同一个片段的访问，是互斥的；对于不同片段的访问，却是可以同步进行的。

### 实现原理

https://www.cnblogs.com/jiangzhaowei/p/7399522.html

http://www.importnew.com/22007.html

ConcurrentHashMap采用锁分段的机制保证线程安全。

1. ConcurrentHashMap继承于AbstractMap抽象类。
2. Segment是ConcurrentHashMap中的内部类，它就是ConcurrentHashMap中的“锁分段”对应的存储结构。ConcurrentHashMap与Segment是组合关系，每个ConcurrentHashMap对象中包含若干个Segment对象，ConcurrentHashMap类中通过Segment类型数组成员存储每个Segment对象。
3. Segment类继承于ReentrantLock类，所以Segment本质上是一个可重入的互斥锁。
4. HashEntry也是ConcurrentHashMap的内部类，是单向链表节点，存储着key-value键值对。Segment与HashEntry是组合关系，Segment类中包含一个HashEntry类型数组成员，HashEntry数组中的每个HashEntry都是一个单向链表。

对于多线程访问对一个“哈希表对象”竞争资源，Hashtable是通过一把锁来控制并发；而ConcurrentHashMap则是将哈希表分成许多片段，对于每一个片段分别通过一个互斥锁来控制并发。ConcurrentHashMap对并发的控制更加细腻，它也更加适应于高并发场景。

**创建**

ConcurrentHashMap是链式哈希表，它是通过“拉链法”来解决哈希冲突的。

**获取**

**添加**

**删除**

### JDK8

http://www.importnew.com/22007.html

JDK8中，ConcurrentHashMap摒弃了Segment（锁段）的概念，而是启用了一种全新的方式实现,利用CAS算法。它沿用了与它同时期的HashMap版本的思想，底层依然由数组+链表+红黑树的方式思想，但是为了做到并发，又增加了很多辅助的类，例如TreeBin，Traverser等对象内部类。

JDK6,7中的ConcurrentHashmap主要使用Segment来实现减小锁粒度，把HashMap分割成若干个Segment，在put的时候需要锁住Segment，get时候不加锁，使用volatile来保证可见性，当要统计全局时（比如size），首先会尝试多次计算modcount来确定，这几次尝试中，是否有其他线程进行了修改操作，如果没有，则直接返回size。如果有，则需要依次锁住所有的Segment来计算。

jdk7中ConcurrentHashmap中，当长度过长碰撞会很频繁，链表的增改删查操作都会消耗很长的时间，影响性能,所以jdk8中完全重写ConcurrentHashmap,代码量从原来的1000多行变成了6000多 行，实现上也和原来的分段式存储有很大的区别。

主要设计上的变化有以下几点:

1. 不采用segment而采用node，锁住node来实现减小锁粒度。
2. 设计了MOVED状态 当resize的中过程中，线程2还在put数据，线程2会帮助resize。
3. 使用3个CAS操作来确保node的一些操作的原子性，这种方式代替了锁。
4. sizeCtl的不同值来代表不同含义，起到了控制的作用

## ConcurrentSkipListMap

ConcurrentSkipListMap相当于线程安全的TreeMap，适用于高并发的场景。

ConcurrentSkipListMap和TreeMap
1. 它们的线程安全机制不同，TreeMap是非线程安全的，而ConcurrentSkipListMap是线程安全的。
2. ConcurrentSkipListMap是通过跳表实现的，而TreeMap是通过红黑树实现的。

关于跳表(Skip List)，它是平衡树的一种替代的数据结构，但是和红黑树不相同的是，跳表对于树的平衡的实现是基于一种随机化的算法的，这样也就是说跳表的插入和删除的工作是比较简单的。

### 跳表

跳表是平衡树的随机替代方案。

下面说说Java中ConcurrentSkipListMap的数据结构。
1. ConcurrentSkipListMap继承于AbstractMap类，也就意味着它是一个哈希表。
2. Index是ConcurrentSkipListMap的内部类，它与“跳表中的索引相对应”。HeadIndex继承于Index，ConcurrentSkipListMap中含有一个HeadIndex的对象head，head是“跳表的表头”。
3. Index是跳表中的索引，它包含“右索引的指针(right)”，“下索引的指针(down)”和“哈希表节点node”。node是Node的对象，Node也是ConcurrentSkipListMap中的内部类。

## ConcurrentSkipListSet

ConcurrentSkipListSet相当于线程安全的TreeSet，适用于高并发的场景。

ConcurrentSkipListSet和TreeSet
1. 它们的线程安全机制不同，TreeSet是非线程安全的，而ConcurrentSkipListSet是线程安全的。
2. ConcurrentSkipListSet是通过ConcurrentSkipListMap实现的，而TreeSet是通过TreeMap实现的。

3. ConcurrentSkipListSet继承于AbstractSet。因此，它本质上是一个集合。
4. ConcurrentSkipListSet实现了NavigableSet接口。因此，ConcurrentSkipListSet是一个有序的集合。
5. ConcurrentSkipListSet是通过ConcurrentSkipListMap实现的。它包含一个ConcurrentNavigableMap对象m，而m对象实际上是ConcurrentNavigableMap的实现类ConcurrentSkipListMap的实例。ConcurrentSkipListMap中的元素是key-value键值对；而ConcurrentSkipListSet是集合，它只用到了ConcurrentSkipListMap中的key。

## ArrayBlockingQueue

ArrayBlockingQueue是数组实现的线程安全的有界的阻塞队列。

ArrayBlockingQueue内部通过“互斥锁”保护竞争资源，实现了多线程对竞争资源的互斥访问。

ArrayBlockingQueue对应的数组容量是有界限的。

阻塞队列是指在多线程访问竞争资源时，当竞争资源已被某线程获取时，其它要获取该资源的线程需要阻塞等待；而且，ArrayBlockingQueue是按FIFO原则对元素进行排序，元素都是从尾部插入到队列，从头部开始返回。

ArrayBlockingQueue不同于ConcurrentLinkedQueue，ArrayBlockingQueue是数组实现的，并且是有界限的；而ConcurrentLinkedQueue是链表实现的，是无界限的。

1. ArrayBlockingQueue继承于AbstractQueue，并且它实现了BlockingQueue接口。
2. ArrayBlockingQueue内部是通过固定容量的Object[]数组保存数据的，也就是说ArrayBlockingQueue本质上是通过数组实现的。ArrayBlockingQueue的大小，即数组的容量是创建ArrayBlockingQueue时指定的。
3. ArrayBlockingQueue与ReentrantLock是组合关系，ArrayBlockingQueue中包含一个ReentrantLock对象(lock)。ReentrantLock是可重入的互斥锁，ArrayBlockingQueue就是根据该互斥锁实现“多线程对竞争资源的互斥访问”。而且，ReentrantLock分为公平锁和非公平锁，关于具体使用公平锁还是非公平锁，在创建ArrayBlockingQueue时可以指定；而且，ArrayBlockingQueue默认会使用非公平锁。
4. ArrayBlockingQueue与Condition是组合关系，ArrayBlockingQueue中包含两个Condition对象(notEmpty和notFull)。而且，Condition又依赖于ArrayBlockingQueue而存在，通过Condition可以实现对ArrayBlockingQueue的更精确的访问
	- 若某线程(线程A)要取数据时，数组正好为空，则该线程会执行notEmpty.await()进行等待；当其它某个线程(线程B)向数组中插入了数据之后，会调用notEmpty.signal()唤醒“notEmpty上的等待线程”。此时，线程A会被唤醒从而得以继续运行。
	- 若某线程(线程H)要插入数据时，数组已满，则该线程会它执行notFull.await()进行等待；当其它某个线程(线程I)取出数据之后，会调用notFull.signal()唤醒“notFull上的等待线程”。此时，线程H就会被唤醒从而得以继续运行。

### 实现原理

// TO-DO

## LinkedBlockQueue

LinkedBlockingQueue是一个基于单向链表的阻塞队列。该队列按FIFO排序元素，新元素插入到队列的尾部，并且队列获取操作会获得位于队列头部的元素。链接队列的吞吐量通常要高于基于数组的队列，但是在大多数并发应用程序中，其可预知的性能要低。

此外，LinkedBlockingQueue还是可选容量的(防止过度膨胀)，即可以指定队列的容量。如果不指定，默认容量大小等于Integer.MAX_VALUE。

1. LinkedBlockingQueue继承于AbstractQueue，它本质上是一个FIFO(先进先出)的队列。
2. LinkedBlockingQueue实现了BlockingQueue接口，它支持多线程并发。当多线程竞争同一个资源时，某线程获取到该资源之后，其它线程需要阻塞等待。
3. LinkedBlockingQueue是通过单链表实现的。
	1. head是链表的表头。取出数据时，都是从表头head处插入。
	2. last是链表的表尾。新增数据时，都是从表尾last处插入。
	3. count是链表的实际大小，即当前链表中包含的节点个数。
	4. capacity是列表的容量，它是在创建链表时指定的。
	5. putLock是插入锁，takeLock是取出锁；notEmpty是“非空条件”，notFull是“未满条件”。通过它们对链表进行并发控制。

LinkedBlockingQueue在实现“多线程对竞争资源的互斥访问”时，对于“插入”和“取出(删除)”操作分别使用了不同的锁。对于插入操作，通过“插入锁putLock”进行同步；对于取出操作，通过“取出锁takeLock”进行同步。

此外，插入锁putLock和“非满条件notFull”相关联，取出锁takeLock和“非空条件notEmpty”相关联。通过notFull和notEmpty更细腻的控制锁。

### 实现原理

// TO-DO

## PriorityBlockingQueue

PriorityBlockingQueue是一个基于二叉堆实现的无界有序的阻塞队列，与PriorityQueue类似。在take()操作时增加了阻塞操作。PriorityBlockingQueue不支持插入null元素，同时不支持插入非comparable的对象，使用迭代器遍历时不保持任何顺序。

PriorityBlockingQueue是不稳定排序。

由于这是一个优先级队列所以有个比较器comparator用来比较元素大小。lock独占锁对象用来控制同时只能有一个线程可以进行入队出队操作。notEmpty条件变量用来实现take方法阻塞模式。因为这是无界队列因此put操作是非阻塞的，所以没有notFull条件变量。

内部通过一个PriorityQueue来实现排序。