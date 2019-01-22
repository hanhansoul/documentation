[40个Java集合面试问题和答案](http://www.importnew.com/15980.html)

Q：Java集合框架是什么？说出一些集合框架的优点？

Q：集合框架中的泛型有什么优点？

1. 不需要使用显式转换和instanceOf操作符。
2. 编译器类型检查。
3. 不会产生类型检查的字节码指令。

Q：Java集合框架的基础接口有哪些？

1. Collection
2. List
3. Set
4. Map
5. Queue / Deque

Q：为何Collection不从Cloneable和Serializable接口继承？

Cloneable和Serializable接口由Collection的具体实现类定义，以决定应该如何进行克隆或序列化。

Q：为何Map接口不继承Collection接口？

Map与Collection接口实现的方法接口不同。

## 迭代器

Q：Iterator是什么？

迭代器接口，用于遍历任何Collection接口。迭代器取代了Java集合框架中的Enumeration。

Q：Enumeration和Iterator接口的区别？

**函数接口不同**

Enumeration只有2个函数接口。通过Enumeration，我们只能读取集合的数据，而不能对数据进行修改。

Iterator只有3个函数接口。Iterator除了能读取集合的数据之外，也能数据进行删除操作。

**fail-fast机制**

Iterator支持fail-fast机制，而Enumeration不支持。
        
Enumeration为Vector、Hashtable等类提供遍历接口。Enumeration本身并没有支持同步，而在Vector、Hashtable实现Enumeration时，添加了同步。

Iterator为了HashMap、ArrayList等集合提供遍历接口。Iterator是支持fail-fast机制的：当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。

Q：为何没有像Iterator.add()这样的方法，向集合中添加元素？

Q：Iterater和ListIterator之间有什么区别？

1. 我们可以使用Iterator来遍历Set和List集合，而ListIterator只能遍历List。
2. Iterator只可以向前遍历，而LIstIterator可以双向遍历。
3. ListIterator从Iterator接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

Q：为何Iterator接口没有具体的实现？

Iterator接口定义了遍历集合的方法，但它的实现则是集合实现类的责任。每个能够返回用于遍历的Iterator的集合类都有它自己的Iterator实现内部类。

## fail-fast与fail-safe机制

Q：遍历一个List有哪些不同的方式？

使用迭代器更加线程安全，因为它是fail-fast的。

Q：fail-fast机制的含义

fail-fast机制是指，在遍历一个集合时，若集合的结构被改变了，则会抛出ConcurrentModificationException。

fail-fast机制是通过集合中变量modCount与expectModCount进行比较实现，每当修改集合结构时，便会增加modCount的值，若modCount和expectModCount不相等，则抛出异常。

单线程中，若在遍历过程中修改了集合结构，则抛出异常。但remove()方法modCount与expectModCount值相等，不会抛出异常。

多线程中，若当一个线程在遍历集合，另一个线程对这个集合的结构进行了修改。

注意，迭代器的快速失败行为无法得到保证。

Q：fail-safe机制的含义

fail-safe机制是指，任何对集合结构的修改都会在一个复制的集合上进行修改，因此不会抛出ConcurrentModificationException。

fail-safe机制
1. 首先需要复制集合，产生大量的无效对象，开销大。
2. 无法保证读取的数据是目前原始数据结构中的数据。

Q：fail-fast与fail-safe有什么区别？

Iterator的fail-fast属性与当前的集合共同起作用，因此它不会受到集合中任何改动的影响。Java.util包中的所有集合类都被设计为fail-fast的，而java.util.concurrent中的集合类都为fail-safe的。Fail-fast迭代器抛出ConcurrentModificationException，而fail-safe迭代器从不抛出ConcurrentModificationException。

Q：在迭代一个集合的时候，如何避免ConcurrentModificationException？

使用满足fail-safe的并发集合类，如CopyOnWriteArrayList。

## HashMap

Q：HashMap的原理。

HashMap在Map.Entry静态内部类实现中存储key-value对。HashMap使用哈希算法，在put和get方法中，它使用hashCode()和equals()方法。

当我们通过传递key-value对调用put方法的时候，HashMap使用hashCode()和哈希算法来找出存储key-value对的索引。

Entry存储在LinkedList中，所以如果存在entry，它使用equals()方法来检查传递的key是否已经存在，如果存在，它会覆盖value，如果不存在，它会创建一个新的entry然后保存。

当我们通过传递key调用get方法时，它再次使用hashCode()来找到数组中的索引，然后使用equals()方法找出正确的Entry，然后返回它的值。

其它关于HashMap比较重要的问题是容量、负荷系数和阀值调整。HashMap默认的初始容量是32，负荷系数是0.75。阀值是为负荷系数乘以容量，无论何时我们尝试添加一个entry，如果map的大小比阀值大的时候，HashMap会对map的内容进行重新哈希，且使用更大的容量。容量总是2的幂，所以如果你知道你需要存储大量的key-value对，比如缓存从数据库里面拉取的数据，使用正确的容量和负荷系数对HashMap进行初始化是个不错的做法。

Q：hashCode()和equals()方法的含义与关系。

1. 若某个类没有覆盖equals()方法，当它的通过equals()比较两个对象时，实际上是比较两个对象是不是同一个对象。这时，等价于通过“==”去比较这两个对象。
2. 我们可以覆盖类的equals()方法，来让equals()通过其它方式比较两个对象是否相等。通常的做法是：若两个对象的内容相等，则equals()方法返回true；否则，返回fasle。

java对equals()方法的要求：
1. 对称性：如果x.equals(y)返回是"true"，那么y.equals(x)也应该返回是"true"。
2. 反射性：x.equals(x)必须返回是"true"。
3. 类推性：如果x.equals(y)返回是"true"，而且y.equals(z)返回是"true"，那么z.equals(x)也应该返回是"true"。
4. 一致性：如果x.equals(y)返回是"true"，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是"true"。
5. 非空性，x.equals(null)，永远返回是"false"；x.equals(和x不同类型的对象)永远返回是"false"。

Java对象的eqauls方法和hashCode方法是这样规定的：
1. 相等（相同）的对象必须具有相等的哈希码（或者散列码）。
2. 如果两个对象的hashCode相同，它们并不一定相同。

Q：我们能否使用任何类作为Map的key？

1. 如果类重写了equals()方法，它也应该重写hashCode()方法。
2. 类的所有实例需要遵循与equals()和hashCode()相关的规则。
3. 如果一个类没有使用equals()，你不应该在hashCode()中使用它。
4. 用户自定义key类的最佳实践是使之为不可变的，这样，hashCode()值可以被缓存起来，拥有更好的性能。不可变的类也可以确保hashCode()和equals()在未来不会改变，这样就会解决与可变相关的问题了。

Q：Map接口提供了哪些不同的集合视图？

- keySet()
- entrySet()
- values()

Q：HashMap和Hashtable区别

1. HashMap允许key和value为null，而HashTable不允许。
2. HashTable是同步的，而HashMap不是。所以HashMap适合单线程环境，HashTable适合多线程环境。
3. HashMap的迭代器Iterator时fail-fast的，而Hashtable的迭代器Enumeration不是fail-fast的。
4. HashTable被认为是性能较低的同步类，如果需要线程安全的Map类，应该使用CocurrentHashMap。

Q：HashMap与TreeMap区别

## List

Q：ArrayList和Vector

ArrayList和Vector在很多时候都很类似。
1. 两者内部都是通过一个动态数组实现。
2. 两者维护插入的顺序，我们可以根据插入顺序来获取元素。
3. ArrayList和Vector的迭代器实现都是fail-fast的。
4. ArrayList和Vector两者允许null值，也可以使用索引值对元素进行随机访问。

以下是ArrayList和Vector的不同点。
1. Vector是同步的，而ArrayList不是。
2. ArrayList比Vector快，它因为有同步，不会过载。
3. ArrayList在扩容时每次为原容量的3/2倍，vector每次为原容量的2倍。
4. ArrayList使用Iterator或ListIterator进行遍历，而Vector还可以使用Enumeration进行遍历。

Q：Array和ArrayList有何区别？什么时候更适合用Array？

1. Array是java原生类型，可以通过[]访问数据；而ArrayList是java的集合类，需要通过一组方法访问数据。
2. Array的容量是固定的，而ArrayList容量是动态变化的。
3. Array可以包含原生基本类型或对象类型，而ArrayList只能包含对象类型。

Q：ArrayList与LinkedList

Q：哪些集合类提供对元素的随机访问？

- ArrayList
- Vector
- CopyOnWriteArrayList

## Set

Q：EnumSet

## 多线程

Q：哪些集合类是线程安全的？

Q：并发集合类是什么？

java.util.concurrent包中包含的线程安全集合类。如CopyOnWriteArrayList、 ConcurrentHashMap、CopyOnWriteArraySet。

Q：BlockingQueue

Java.util.concurrent.BlockingQueue是一个队列，在进行检索或移除一个元素的时候，它会等待队列变为非空；当在添加一个元素时，它会等待队列中的可用空间。BlockingQueue接口是Java集合框架的一部分，主要用于实现生产者-消费者模式。我们不需要担心等待生产者有可用的空间，或消费者有可用的对象，因为它都在BlockingQueue的实现类中被处理了。Java提供了集中BlockingQueue的实现，比如ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue,、SynchronousQueue等。

Q：队列和栈是什么，列出它们的区别？

Q：Comparable和Comparator接口是什么？

如果我们想使用Array或Collection的排序方法时，需要在自定义类里实现Java提供Comparable接口。Comparable接口有compareTo(T OBJ)方法，它被排序方法所使用。我们应该重写这个方法，如果“this”对象比传递的对象参数更小、相等或更大时，它返回一个负整数、0或正整数。但是，在大多数实际情况下，我们想根据不同参数进行排序。比如，作为一个CEO，我想对雇员基于薪资进行排序，一个HR想基于年龄对他们进行排序。这就是我们需要使用Comparator接口的情景，因为Comparable.compareTo(Object o)方法实现只能基于一个字段进行排序，我们不能根据对象排序的需要选择字段。Comparator接口的compare(Object o1, Object o2)方法的实现需要传递两个对象参数，若第一个参数比第二个小，返回负整数；若第一个等于第二个，返回0；若第一个比第二个大，返回正整数。

Q：Comparable和Comparator接口有何区别？

Comparable和Comparator接口被用来对对象集合或者数组进行排序。

1. 实现了Comparable接口的类可以用于排序，需要实现compareTo(T t)方法，用于对this对象与t对象进行比较。
2. 实现了Comparator接口的类用于为需要排序的对象提供一个外部比较器，需要实现compare(T t1, T t2)方法，用于比较t1和t2的大小关系。