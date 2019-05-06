## ConcurrentHashMap

ConcurrentHashMap相当于线程安全的HashMap。HashMap, Hashtable, ConcurrentHashMap之间的关联如下：

1. HashMap是非线程安全的哈希表，常用于单线程程序中。

2. Hashtable是线程安全的哈希表，通过synchronized来保证线程安全的，因此在线程竞争激烈时，效率比较低，因为当一个线程访问Hashtable的同步方法时，其它线程就访问Hashtable的同步方法时，可能会进入阻塞状态。

3. ConcurrentHashMap是线程安全的哈希表，它是通过“锁分段”来保证线程安全的。ConcurrentHashMap将哈希表分成许多片段(Segment)，每一个片段除了保存哈希表之外，本质上也是一个“可重入的互斥锁”(ReentrantLock)。多线程对同一个片段的访问，是互斥的；对于不同片段的访问，却是可以同步进行的。

### 实现原理

http://www.importnew.com/29832.html

https://www.cnblogs.com/jiangzhaowei/p/7399522.html

http://www.importnew.com/22007.html

ConcurrentHashMap采用锁分段的机制保证线程安全。

1. ConcurrentHashMap继承于AbstractMap抽象类。
2. Segment是ConcurrentHashMap中的内部类，它就是ConcurrentHashMap中的“锁分段”对应的存储结构。ConcurrentHashMap与Segment是组合关系，每个ConcurrentHashMap对象中包含若干个Segment对象，ConcurrentHashMap类中通过Segment类型数组成员存储每个Segment对象。
3. Segment类继承于ReentrantLock类，所以Segment本质上是一个可重入的互斥锁。
4. HashEntry也是ConcurrentHashMap的内部类，是单向链表节点，存储着key-value键值对。Segment与HashEntry是组合关系，Segment类中包含一个HashEntry类型数组成员，HashEntry数组中的每个HashEntry都是一个单向链表。

对于多线程访问对一个“哈希表对象”竞争资源，Hashtable是通过一把锁来控制并发；而ConcurrentHashMap则是将哈希表分成许多片段，对于每一个片段分别通过一个互斥锁来控制并发。ConcurrentHashMap对并发的控制更加细腻，它也更加适应于高并发场景。

### 并发度

并发度可以理解为程序运行时能够同时更新ConccurentHashMap且不产生锁竞争的最大线程数，实际上就是ConcurrentHashMap中的分段锁个数，即Segment[]的数组长度。

ConcurrentHashMap默认的并发度为16，但用户也可以在构造函数中设置并发度。当用户设置并发度时，ConcurrentHashMap会使用大于等于该值的最小2幂指数作为实际并发度（假如用户设置并发度为17，实际并发度则为32）。

运行时通过将key的高n位（n = 32 – segmentShift）和并发度减1（segmentMask）做位与运算定位到所在的Segment。segmentShift与segmentMask都是在构造过程中根据concurrency level被相应的计算出来。

如果并发度设置的过小，会带来严重的锁竞争问题；如果并发度设置的过大，原本位于同一个Segment内的访问会扩散到不同的Segment中，CPU cache命中率会下降，从而引起程序性能下降。

### 创建分段锁

和JDK6不同，JDK7中除了第一个Segment之外，剩余的Segments采用的是延迟初始化的机制：每次put之前都需要检查key对应的Segment是否为null，如果是则调用ensureSegment()以确保对应的Segment被创建。

ensureSegment可能在并发环境下被调用，但与想象中不同，ensureSegment并未使用锁来控制竞争，而是使用了Unsafe对象的getObjectVolatile()提供的原子读语义结合CAS来确保Segment创建的原子性。代码段如下：

	if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
		// recheck
		Segment<K,V> s = new Segment<K,V>(lf, threshold, tab);
        while ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
            if (UNSAFE.compareAndSwapObject(ss, u, null, seg = s))
                break;
        }
	}


### put/putIfAbsent/putAll

和JDK6一样，ConcurrentHashMap的put方法被代理到了对应的Segment中。与JDK6不同的是，JDK7版本的ConcurrentHashMap在获得Segment锁的过程中，做了一定的优化 - 在真正申请锁之前，put方法会通过tryLock()方法尝试获得锁，在尝试获得锁的过程中会对对应hashcode的链表进行遍历，如果遍历完毕仍然找不到与key相同的HashEntry节点，则为后续的put操作提前创建一个HashEntry。当tryLock一定次数后仍无法获得锁，则通过lock申请锁。

需要注意的是，由于在并发环境下，其他线程的put，rehash或者remove操作可能会导致链表头结点的变化，因此在过程中需要进行检查，如果头结点发生变化则重新对表进行遍历。而如果其他线程引起了链表中的某个节点被删除，即使该变化因为是非原子写操作（删除节点后链接后续节点调用的是Unsafe.putOrderedObject()，该方法不提供原子写语义）可能导致当前线程无法观察到，但因为不影响遍历的正确性所以忽略不计。

之所以在获取锁的过程中对整个链表进行遍历，主要目的是希望遍历的链表被CPU cache所缓存，为后续实际put过程中的链表遍历操作提升性能。

在获得锁之后，Segment对链表进行遍历，如果某个HashEntry节点具有相同的key，则更新该HashEntry的value值，否则新建一个HashEntry节点，将它设置为链表的新head节点并将原头节点设为新head的下一个节点。新建过程中如果节点总数（含新建的HashEntry）超过threshold，则调用rehash()方法对Segment进行扩容，最后将新建HashEntry写入到数组中。

put方法中，链接新节点的下一个节点（HashEntry.setNext()）以及将链表写入到数组中（setEntryAt()）都是通过Unsafe的putOrderedObject()方法来实现，这里并未使用具有原子写语义的putObjectVolatile()的原因是：JMM会保证获得锁到释放锁之间所有对象的状态更新都会在锁被释放之后更新到主存，从而保证这些变更对其他线程是可见的。

### rehash

相对于HashMap的resize，ConcurrentHashMap的rehash原理类似，但rehash做了一定的优化，避免让所有的节点都进行复制操作：由于扩容是基于2的幂指来操作，假设扩容前某HashEntry对应到Segment中数组的index为i，数组的容量为capacity，那么扩容后该HashEntry对应到新数组中的index只可能为i或者i+capacity，因此大多数HashEntry节点在扩容前后index可以保持不变。基于此，rehash方法中会定位第一个后续所有节点在扩容后index都保持不变的节点，然后将这个节点之前的所有节点重排即可。

### remove

和put类似，remove在真正获得锁之前，也会对链表进行遍历以提高缓存命中率。


### get与containsKey

get与containsKey两个方法几乎完全一致：他们都没有使用锁，而是通过Unsafe对象的getObjectVolatile()方法提供的原子读语义，来获得Segment以及对应的链表，然后对链表遍历判断是否存在key相同的节点以及获得该节点的value。

但由于遍历过程中其他线程可能对链表结构做了调整，因此get和containsKey返回的可能是过时的数据，这一点是ConcurrentHashMap在弱一致性上的体现。如果要求强一致性，那么必须使用Collections.synchronizedMap()方法。

### size、containsValue

这些方法都是基于整个ConcurrentHashMap来进行操作的，他们的原理也基本类似：首先不加锁循环执行以下操作：循环所有的Segment（通过Unsafe的getObjectVolatile()以保证原子读语义），获得对应的值以及所有Segment的modcount之和。如果连续两次所有Segment的modcount和相等，则过程中没有发生其他线程修改ConcurrentHashMap的情况，返回获得的值。

当循环次数超过预定义的值时，这时需要对所有的Segment依次进行加锁，获取返回值后再依次解锁。值得注意的是，加锁过程中要强制创建所有的Segment，否则容易出现其他线程创建Segment并进行put，remove等操作。

一般来说，应该避免在多线程环境下使用size和containsValue方法。

注1：modcount在put, replace, remove以及clear等方法中都会被修改。

注2：对于containsValue方法来说，如果在循环过程中发现匹配value的HashEntry，则直接返回true。

注3：与HashMap不同的是，ConcurrentHashMap并不允许key或者value为null，按照Doug Lea的说法，这么设计的原因是在ConcurrentHashMap中，一旦value出现null，则代表HashEntry的key/value没有映射完成就被其他线程所见，需要特殊处理。

## JDK8中的ConcurrentHashMap

http://www.importnew.com/22007.html

JDK8中，ConcurrentHashMap摒弃了Segment（锁段）的概念，而是启用了一种全新的方式实现,利用CAS算法。它沿用了与它同时期的HashMap版本的思想，底层依然由数组+链表+红黑树的方式思想，但是为了做到并发，又增加了很多辅助的类，例如TreeBin，Traverser等对象内部类。

JDK6,7中的ConcurrentHashMap主要使用Segment来实现减小锁粒度，把HashMap分割成若干个Segment，在put的时候需要锁住Segment，get时候不加锁，使用volatile来保证可见性，当要统计全局时（比如size），首先会尝试多次计算modcount来确定，这几次尝试中，是否有其他线程进行了修改操作，如果没有，则直接返回size。如果有，则需要依次锁住所有的Segment来计算。

jdk7中ConcurrentHashMap中，当长度过长碰撞会很频繁，链表的增改删查操作都会消耗很长的时间，影响性能，所以jdk8中完全重写ConcurrentHashMap,代码量从原来的1000多行变成了6000多行，实现上也和原来的分段式存储有很大的区别。

主要设计上的变化有以下几点:

1. 不采用segment而采用node，锁住node来实现减小锁粒度。
2. 设计了MOVED状态，当在resize过程中，线程2还在put数据，线程2会帮助resize。
3. 使用3个CAS操作来确保node的一些操作的原子性，这种方式代替了锁。
4. sizeCtl的不同值来代表不同含义，起到了控制的作用

### 实现原理

首先来看几个重要的属性，与HashMap相同的就不再介绍了，这里重点解释一下sizeCtl这个属性。它是一个控制标识符，在不同的地方有不同用途，而且它的取值不同，也代表不同的含义。

- 负数代表正在进行初始化或扩容操作
- -1代表正在初始化
- -N表示有N-1个线程正在进行扩容操作
- 正数或0代表hash表还没有被初始化，这个数值表示初始化或下一次进行扩容的大小，这一点类似于扩容阈值的概念。还后面可以看到，它的值始终是当前ConcurrentHashMap容量的0.75倍，这与loadfactor是对应的。

### 重要的类

**Node**

Node是最核心的内部类，它包装了key-value键值对，所有插入ConcurrentHashMap的数据都包装在这里面。它与HashMap中的定义很相似，但是有一些差别，它对value和next属性设置了volatile同步锁（与JDK7的Segment相同），它不允许调用setValue方法直接改变Node的value域，它增加了find方法辅助map.get()方法。

**TreeNode**

树节点类，另外一个核心的数据结构。当链表长度过长的时候，会转换为TreeNode。但是与HashMap不相同的是，它并不是直接转换为红黑树，而是把这些结点包装成TreeNode放在TreeBin对象中，由TreeBin完成对红黑树的包装。而且TreeNode在ConcurrentHashMap集成自Node类，而并非HashMap中的集成自LinkedHashMap.Entry<K,V>类，也就是说TreeNode带有next指针，这样做的目的是方便基于TreeBin的访问。

**TreeBin**

这个类并不负责包装用户的key、value信息，而是包装的很多TreeNode节点。它代替了TreeNode的根节点，也就是说在实际的ConcurrentHashMap“数组”中，存放的是TreeBin对象，而不是TreeNode对象，这是与HashMap的区别。另外这个类还带有了读写锁。

这里仅贴出它的构造方法。可以看到在构造TreeBin节点时，仅仅指定了它的hash值为TREEBIN常量，这也就是个标识为。同时也看到我们熟悉的红黑树构造方法

**ForwardingNode**

一个用于连接两个table的节点类。它包含一个nextTable指针，用于指向下一张表。而且这个节点的key value next指针全部为null，它的hash值为-1。这里面定义的find的方法是从nextTable里进行查询节点，而不是以自身为头节点进行查找。

### Unsafe与CAS

在ConcurrentHashMap中，随处可以看到U, 大量使用了U.compareAndSwapXXX的方法，这个方法是利用一个CAS算法实现无锁化的修改值的操作，他可以大大降低锁代理的性能消耗。这个算法的基本思想就是不断地去比较当前内存中的变量值与你指定的一个变量值是否相等，如果相等，则接受你指定的修改的值，否则拒绝你的操作。因为当前线程中的值已经不是最新的值，你的修改很可能会覆盖掉其他线程修改的结果。这一点与乐观锁，SVN的思想是比较类似的。

### ConcurrentHashMap与Hashtable

HashTable虽然性能上不如ConcurrentHashMap，但并不能完全被取代，两者的迭代器的一致性不同的，HashTable的迭代器是强一致性的，而ConcurrentHashMap是弱一致的。ConcurrentHashMap的get、clear、iterator都是弱一致性的。

get方法是弱一致的，是什么含义？可能你期望往ConcurrentHashMap底层数据结构中加入一个元素后，立马能对get可见，但ConcurrentHashMap并不能如你所愿。换句话说，put操作将一个元素加入到底层数据结构后，get可能在某段时间内还看不到这个元素，若不考虑内存模型，单从代码逻辑上来看，却是应该可以看得到的。