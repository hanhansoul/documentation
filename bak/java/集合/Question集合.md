https://blog.csdn.net/u010775025/article/details/79315361

https://blog.csdn.net/csdn_terence/article/details/78379878

Q：LinkedHashSet与LinkedHashMap

Q：LinkedHashMap中的accessOrder

Q：集合框架中的泛型有什么优点？

- 编译期检查类型安全检查，避免运行时抛出ClassCastException。
- 使得代码简洁，不需要使用显式转换与instanceOf操作符。
- 不会产生类型检查的字节码指令。

Q：Java集合框架的基础接口有哪些？

 一些其它的接口有Queue、Dequeue、SortedSet、SortedMap和ListIterator。

- Collection
	- List
	- Set
	- Queue
	- Deque
	- SortedSet
	- SortedMap
	- Iterator
	- ListIterator
	- NavigableSet

- Map

Q：为何Collection不从Cloneable和Serializable接口继承？

Collection接口指定一组对象，对象即为它的元素。如何维护这些元素由Collection的具体实现决定。例如，一些如List的Collection实现允许重复的元素，而其它的如Set就不允许。很多Collection实现有一个公有的clone方法。然而，把它放到集合的所有实现中也是没有意义的。这是因为Collection是一个抽象表现。重要的是实现。

当与具体实现打交道的时候，克隆或序列化的语义和含义才发挥作用。所以，具体实现应该决定如何对它进行克隆或序列化，或它是否可以被克隆或序列化。

在所有的实现中授权克隆和序列化，最终导致更少的灵活性和更多的限制。特定的实现应该决定它是否可以被克隆和序列化。

Q：为何Map接口不继承Collection接口？

Map的数据结构、对象操作与List、Set差别较大，无法统一由Collection接口管理。

尽管Map接口和它的实现也是集合框架的一部分，但Map不是集合，集合也不是Map。因此，Map继承Collection毫无意义，反之亦然。

如果Map继承Collection接口，那么元素去哪儿？Map包含key-value对，它提供抽取key或value列表集合的方法，但是它不适合“一组对象”规范。

Q：Iterator是什么？

Iterator接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者在迭代过程中移除元素。

Q：Enumeration和Iterator接口的区别？

Enumeration的速度是Iterator的两倍，也使用更少的内存。Enumeration是非常基础的，也满足了基础的需要。但是，与Enumeration相比，Iterator更加安全，因为当一个集合正在被遍历的时候，它会阻止其它线程去修改集合。

迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者从集合中移除元素，而Enumeration不能做到。为了使它的功能更加清晰，迭代器方法名已经经过改善。

Q：为何没有像Iterator.add()这样的方法，向集合中添加元素？

Q：为何迭代器没有一个方法可以直接获取下一个元素，而不需要移动游标？

它可以在当前Iterator的顶层实现，但是它用得很少，如果将它加到接口中，每个继承都要去实现它，这没有意义。

Q：Iterater和ListIterator之间有什么区别？

1. 我们可以使用Iterator来遍历Set和List集合，而ListIterator只能遍历List。
2. Iterator只可以向前遍历，而LIstIterator可以双向遍历。
3. ListIterator从Iterator接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

Q：遍历一个List有哪些不同的方式？

1. 索引方式
2. 迭代器Iterator
3. for-each语句

Q：通过迭代器fail-fast属性，你明白了什么？

每次我们尝试获取下一个元素的时候，Iterator fail-fast属性检查当前集合结构里的任何改动。如果发现任何改动，它抛出ConcurrentModificationException。Collection中所有Iterator的实现都是按fail-fast来设计的（ConcurrentHashMap和CopyOnWriteArrayList这类并发集合类除外）。

Q：fail-fast与fail-safe有什么区别？

Iterator的fail-fast属性与当前的集合共同起作用，因此它不会受到集合中任何改动的影响。Java.util包中的所有集合类都被设计为fail-fast的。java.util.concurrent中的集合类都为fail-safe的。Fail-fast迭代器抛出ConcurrentModificationException，而fail-safe迭代器从不抛出ConcurrentModificationException。

Q：在迭代一个集合的时候，如何避免ConcurrentModificationException？

在遍历一个集合的时候，我们可以使用并发集合类来避免ConcurrentModificationException，比如使用CopyOnWriteArrayList，而不是ArrayList。

Q：为何Iterator接口没有具体的实现？

Iterator接口定义了遍历集合的方法，但它的实现则是集合实现类的责任。每个能够返回用于遍历的Iterator的集合类都有它自己的Iterator实现内部类。

这就允许集合类去选择迭代器是fail-fast还是fail-safe的。比如，ArrayList迭代器是fail-fast的，而CopyOnWriteArrayList迭代器是fail-safe的。

Q：UnsupportedOperationException是什么？

UnsupportedOperationException是用于表明操作不支持的异常。例如，在集合框架java.util.Collections.UnmodifiableCollection将会在所有add和remove操作中抛出这个异常。

Q：在Java中，HashMap是如何工作的？

Q：hashCode()和equals()方法有何重要性？

HashMap使用Key对象的hashCode()和equals()方法去决定key-value对的索引。当我们试着从HashMap中获取值的时候，这些方法也会被用到。如果这些方法没有被正确地实现，在这种情况下，两个不同Key也许会产生相同的hashCode()和equals()输出，HashMap将会认为它们是相同的，然后覆盖它们，而非把它们存储到不同的地方。同样的，所有不允许存储重复数据的集合类都使用hashCode()和equals()去查找重复，所以正确实现它们非常重要。equals()和hashCode()的实现应该遵循以下规则：

1. 如果o1.equals(o2)，那么o1.hashCode() == o2.hashCode()总是为true的。
2. 如果o1.hashCode() == o2.hashCode()，并不意味着o1.equals(o2)会为true。

Q：我们能否使用任何类作为Map的key？

我们可以使用任何类作为Map的key，然而在使用它们之前，需要考虑以下几点：

1. 如果类重写了equals()方法，它也应该重写hashCode()方法。
2. 类的所有实例需要遵循与equals()和hashCode()相关的规则。请参考之前提到的这些规则。
3. 如果一个类没有使用equals()，你不应该在hashCode()中使用它。
4. 用户自定义key类的最佳实践是使之为不可变的，这样，hashCode()值可以被缓存起来，拥有更好的性能。不可变的类也可以确保hashCode()和equals()在未来不会改变，这样就会解决与可变相关的问题了。

Q：Map接口提供了哪些不同的集合视图？

 Map接口提供三个集合视图：

 - Set keySet()
 - Collection values()
 - Set<Map.Entry<K,V>> entrySet()

Q：HashMap和HashTable有何不同？

1. HashMap允许key和value为null，而HashTable不允许。
2. HashTable是同步的，而HashMap不是。所以HashMap适合单线程环境，HashTable适合多线程环境。
3. 在Java1.4中引入了LinkedHashMap，HashMap的一个子类，假如你想要遍历顺序，你很容易从HashMap转向LinkedHashMap，但是HashTable不是这样的，它的顺序是不可预知的。
4. HashMap提供对key的Set进行遍历，因此它是fail-fast的，但HashTable提供对key的Enumeration进行遍历，它不支持fail-fast。
5. HashTable被认为是个遗留的类，如果你寻求在迭代的时候修改Map，你应该使用CocurrentHashMap。

Q：如何决定选用HashMap还是TreeMap？

Q：ArrayList和Vector有何异同点？

ArrayList和Vector在很多时候都很类似。

1. 两者都是基于索引的，内部由一个数组支持。
2. 两者维护插入的顺序，我们可以根据插入顺序来获取元素。
3. ArrayList和Vector的迭代器实现都是fail-fast的。
4. ArrayList和Vector两者允许null值，也可以使用索引值对元素进行随机访问。

以下是ArrayList和Vector的不同点。

1. Vector是同步的，而ArrayList不是。然而，如果你寻求在迭代的时候对列表进行改变，你应该使用CopyOnWriteArrayList。
2. ArrayList比Vector快，它因为有同步，不会过载。
3. ArrayList更加通用，因为我们可以使用Collections工具类轻易地获取同步列表和只读列表。

Q：Array和ArrayList有何区别？什么时候更适合用Array？

- Array可以容纳基本类型和对象，而ArrayList只能容纳对象。
- Array是指定大小的，而ArrayList是动态变化的。
- Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。尽管ArrayList明显是更好的选择，但也有些时候Array比较好用。

如果列表的大小已经指定，大部分情况下是存储和遍历它们。

对于遍历基本数据类型，尽管Collections使用自动装箱来减轻编码任务，在指定大小的基本类型的列表上工作也会变得很慢。

如果你要使用多维数组，使用[][]比List<List<>>更容易。

Q：ArrayList和LinkedList有何区别？

ArrayList和LinkedList两者都实现了List接口，但是它们之间有些不同。

1. ArrayList是由Array所支持的基于一个索引的数据结构，所以它提供对元素的随机访问，复杂度为O(1)，但LinkedList存储一系列的节点数据，每个节点都与前一个和下一个节点相连接。所以，尽管有使用索引获取元素的方法，内部实现是从起始点开始遍历，遍历到索引的节点然后返回元素，时间复杂度为O(n)，比ArrayList要慢。

2. 与ArrayList相比，在LinkedList中插入、添加和删除一个元素会更快，因为在一个元素被插入到中间的时候，不会涉及改变数组的大小，或更新索引。

3. LinkedList比ArrayList消耗更多的内存，因为LinkedList中的每个节点存储了前后节点的引用。

Q：哪些集合类提供对元素的随机访问？

ArrayList、HashMap、TreeMap和HashTable类提供对元素的随机访问。

Q：EnumSet是什么？

 java.util.EnumSet是使用枚举类型的集合实现。当集合创建时，枚举集合中的所有元素必须来自单个指定的枚举类型，可以是显示的或隐示的。EnumSet是不同步的，不允许值为null的元素。它也提供了一些有用的方法，比如copyOf(Collection c)、of(E first,E…rest)和complementOf(EnumSet s)。

Q：并发集合类是什么？

Q：BlockingQueue是什么？

Q：队列和栈是什么，列出它们的区别？

Q：Collections类是什么？

Q：Comparable和Comparator接口是什么？

Q：Comparable和Comparator接口有何区别？

Q：我们如何对一组对象进行排序？

- Arrays.sort()
- Collection.sort()

Q：当一个集合被作为参数传递给一个函数时，如何才可以确保函数不能修改它？

Collections.unmodifiableCollection(Collection c)创建一个只读集合，这将确保改变集合的任何操作都会抛出UnsupportedOperationException。

Q：我们如何从给定集合那里创建一个synchronized的集合？

Collections.synchronizedCollection(Collection c)

Q：集合框架里实现的通用算法有哪些？

Q：与Java集合框架相关的有哪些最好的实践？

