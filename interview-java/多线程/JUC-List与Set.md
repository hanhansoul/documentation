List与Set

- CopyOnWriteArrayList：线程安全的ArrayList，实现了List接口。
- CopyOnWriteArraySet：线程安全HashSet，继承了AbstractSet类，内部包含一个CopyOnWriteArrayList对象，通过CopyOnWriteArrayList实现。
- ConcurrentSkipListSet

## Map

- ConcurrentHashMap：线程安全的HashMap，继承了AbstractMap类，通过锁分段实现，支持并发。
- ConcurrentSkipListMap：线程安全的TreeMap，继承了AbstractMap类，实现了ConcurrentNavigableMap接口，通过跳表实现，支持并发。
- ConcurrentSkipListSet：线程安全的TreeSet，继承了AbstractSet，实现了NavigableSet接口，ConcurrentSkipListSet通过ConcurrentSkipListMap实现。

## Queue

- ArrayBlockingQueue
- LinkedBlockingQueue
- LinkedBlockingDeque
- ConcurrentLinkedQueue
- ConcurrentLinkedDeque