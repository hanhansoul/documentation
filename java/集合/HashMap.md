# HashMap

HashMap继承了AbstractMap，实现了Map、Cloneable、java.io.Serializable接口。

- HashMap不是线程安全的。
- HashMap的key、value都可以为null。
- HashMap中的映射不是有序的。

HashMap的实例有两个参数影响其性能：“初始容量”和“加载因子”。容量是哈希表中桶的数量，初始容量只是哈希表在创建时的容量。

加载因子是哈希表在其容量自动增加之前可以达到多满的一种尺度。当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行rehash操作（即重建内部数据结构），从而哈希表将具有大约两倍的桶数。

通常，默认加载因子是0.75。加载因子过高虽然减少了空间开销，但同时也增加了查询成本（在大多数HashMap类的操作中，包括get和put操作，都反映了这一点）。在设置初始容量时应该考虑到映射中所需的条目数及其加载因子，以便最大限度地减少rehash操作次数。如果初始容量大于最大条目数除以加载因子，则不会发生rehash操作。


## HashMap的实现

HashMap是通过“拉链法”实现的哈希表。它包括几个重要的成员变量：table, size, threshold, loadFactor, modCount。

- table是一个Entry类型数组，而Entry实际上就是一个单向链表。哈希表的"key-value键值对"都是存储在Entry数组中的。 
- size是HashMap的大小，它是HashMap保存的键值对的数量。 
- threshold是HashMap的阈值，用于判断是否需要调整HashMap的容量。threshold的值=“容量\*加载因子”，当HashMap中存储数据的数量达到threshold时，就需要将HashMap的容量加倍。
- loadFactor就是加载因子。 
- modCount是用来实现fail-fast机制的。

### 拉链法

HashMap中的键值对都存储在Entry数组中，而一个Entry是表示一个单向链表，Entry数组实际上就是一个元素为单向链表的数组。

	transient Entry[] table;

HashMap将“key为null”的元素都放在table的位置0处，即table[0]中，“key不为null”的放在table的其他位置。

当链表长度超过8时，会将链表转换为红黑树存储。

## 遍历HashMap

