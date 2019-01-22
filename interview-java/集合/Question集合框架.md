 Q：最常见的数据结构有哪些，在哪些场景下应用它们？

	- 数组
	- 列表
	- 集合
	- 栈
	- 队列
	- 树
	- 图

Q：你如何自己实现List，Set和Map?

Q：HashMap和TreeMap有什么区别，在性能上有什么样的差别呢？你比较倾向于使用哪一个?

- HashMap基于哈希表实现，TreeMap基于红黑树实现
- HashMap中的键是无序的，TreeMap中的键是有序的
- 接口/抽象基类
- 时间复杂度

Q：怎么实现一个不可变集合？

	Collections.unmodifiableSet

Q：下面的代码的功能是什么呢？其中的LinkedHashSet能用HashSet来取代吗？

	import java.util.ArrayList;
	import java.util.LinkedHashSet;
	import java.util.List;
	 
	public class CollectionFunction {
	    public <e> List<e> function (List <e> list) {
	          return new ArrayList<e>(new LinkedHashSet<e>(list));
	    }
	}

上面的代码代码通过把原有列表传入一个LinkedHashSet来去除重复的元素。在这个情况里，LinkedHashSet可以保持元素原来的顺序。如果这个顺序是不需要的话，那么上面的LinkedHashSet可以用HashSet来替换。

Q：Java集合框架都有哪些最佳实践呢？

根据实际的使用情况选择合适的数据结构，例如固定大小的还是需要增加大小的，有重复元素的还是没有的，需要保持有序还是不需要，遍历是正向的还是双向的，插入是在末尾的还是任意位置的，更多的插入还是更多的读取，是否需要并行访问，是否允许修改，元素类型是相同的还是不同的，等等。另外，还需要尽早考虑多线程，原子性，内存使用量以及性能等因素。

不要假设你的集合里元素的数量一直会保持较小，它也有可能随着时间增长。所以，你的集合最好能够给定一个合适的大小。

针对接口编程优于针对实现编程。例如，可能在某些情况下，LinkedList是最佳的选择，但是后来ArrayList可能因为性能的原因变得更加合适

在取得列表的时候，如果返回的结果是空的话，最好返回一个长度为0的集合或者数组，而不要返回null。因为，返回null的话可能能会导致程序错误。调用你的方法的开发人员可能会忘记对返回为null的情况进行处理。

封装好集合：一般来说，集合都是不可变的对象。所以尽量不要把集合的成员变量暴露给调用者。因为他们的操作一般都不会进行必要的校验。

Q：ArrayList与LinkedList区别？

1. 存储结构上 ArrayList 底层使用数组进行元素的存储，LinkedList 使用双向链表作为存储结构。

2. 两者均与允许存储 null 也允许存储重复元素。

3. 在性能上 ArrayList 在存储大量元素时候的增删效率 平均低于 LinkedList，因为 ArrayList 在增删的是需要拷贝元素到新的数组，而 LinkedList 只需要将节点前后指针指向改变。

4. 在根据角标获取元素的时间效率上ArrayList优于 LinkedList，因为数组本身有存储连续，有 index 角标，而 LinkedList 存储元素离散，需要遍历链表。

5. 不要使用索引方式遍历 LinkedList 因为效率很低。

6. 两者都是线程不安全的，都可以使用 Collections.synchronizedList(List<E> list) 方法生成一个线程安全的 List。

Q：ArrayList 与 Vector 区别（为什么要用Arraylist取代Vector呢？）

1. ArrayList 的扩容机制由于 Vector ， ArrayList 每次 resize 增加 1.5 倍的容量，Vector 每次增加 2倍的容量，在存储大量元素后扩容的时候就能有很大的空间节省。
2. Vector 添加删除方法以及迭代器遍历的方法都是 synchronized 修饰的方法，在线程安全的情况下使用效率低于 ArrayList
3. ArrayList 和 LinkedList 通过Collections.synchronizedList(List<E> list) 的线程同步的集合，迭代器并不同步，需要使用者去加锁。

Q：实现一个键值对不可修改的Map类型。

继承AsbstractMap抽象类，覆盖其中的抽象方法entrySet，返回一个Set对象，不支持add()、remove()方法，其迭代器也不支持remove()方法。