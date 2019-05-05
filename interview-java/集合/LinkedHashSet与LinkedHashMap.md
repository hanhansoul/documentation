# LinkedHashSet与LinkedHashMap

https://blog.csdn.net/justloveyou_/article/details/71713781

https://blog.csdn.net/learningcoding/article/details/79983248

https://blog.csdn.net/learningcoding/article/details/79983248

## LinkedHashMap

LinkedHashMap继承了HashMap，按照键值对的插入顺序进行排序。

LinkedHashMap内部维护了一个双向链表，将所有Entry节点链入这个双向链表，用于按照需要记录元素的顺序。

与HashMap相比，LinkedHashMap增加了两个属性用于保证迭代顺序，分别是双向链表头结点header和 标志位accessOrder(值为true时，表示按照访问顺序迭代；值为false时，表示按照插入顺序迭代)，accessOrder默认值为false。

根据链表中元素的顺序可以将LinkedHashMap分为：保持插入顺序的LinkedHashMap和保持访问顺序的LinkedHashMap，其中LinkedHashMap的默认实现是按插入顺序排序的。

LinkedHashMap的默认初始容量为16，默认负载因子为0.75。

### accessOrder

当我们要用LinkedHashMap实现LRU算法时，就需要调用该构造方法并将accessOrder置为true。

当accessOrder标志位为true时，表示双向链表中的元素按照访问的先后顺序排列。虽然Entry插入链表的顺序依然是按照其put到LinkedHashMap中的顺序，但put和get方法均有调用recordAccess方法（put方法在key相同时会调用）。

当标志位accessOrder的值为true时，则将当前访问的Entry（put进来的Entry或get出来的Entry）移到双向链表的尾部（key不相同时，put新Entry时，会调用addEntry，它会调用createEntry，该方法同样将新插入的元素放入到双向链表的尾部，既符合插入的先后顺序，又符合访问的先后顺序，因为这时该Entry也被访问了）。

当标志位accessOrder的值为false时，表示双向链表中的元素按照Entry插入LinkedHashMap到中的先后顺序排序，即每次put到LinkedHashMap中的Entry都放在双向链表的尾部，这样遍历双向链表时，Entry的输出顺序便和插入的顺序一致，这也是默认的双向链表的存储顺序。因此，当标志位accessOrder的值为false时，虽然也会调用recordAccess方法，但不做任何操作。

因此，当accessOrder为true，LinkedHashMap在调用get()或put()操作时，都会改变LinkedHashMap的结构，因而此时对触发fail-fast机制。
	
	Map<Integer, Integer> map = new LinkedHashMap(10, (float) 0.75, true);

	for (Integer key : map.keySet()) {
        System.out.println(map.get(key));
    }

由于map.get(key)改变了LinkedHashMap的结构，因此会抛出ConcurrentModificationException异常。

## LinkedHashSet

LinkedHashSet继承了HashSet，其内部实现是基于LinkedHashMap的。
