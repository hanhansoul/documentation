# Map

- Map接口
- AbstractMap抽象类：实现了Map接口，实现了Map中大部分API。
- SortedMap接口：实现了Map接口，通过比较器对键值对进行排序。
- NavigableMap接口：继承了SortedMap接口，提供索引方法。
- TreeMap类：继承了AbstractMap，实现了NavigableMap接口。表示有序键值对。
- HashMap类：继承了AbstractMap，表示无序的键值对。
- Hashtable类：继承了Dictionary，表示无序的键值对。
- WeakHashMap类：继承了AbstractMap，其键为弱键。

## Map接口

1. Map是一个键值对映射接口。Map映射中不能包含重复的键；每个键最多只能映射到一个值。
2. Map接口提供三种collection视图，允许以键集、值集或键-值映射关系集的形式查看某个映射的内容。
3. Map映射顺序。有些实现类，可以明确保证其顺序，如TreeMap；另一些映射实现则不保证顺序，如 HashMap 类。
4. Map的实现类应该提供2个“标准的”构造方法：第一个，void（无参数）构造方法，用于创建空映射；第二个，带有单个 Map 类型参数的构造方法，用于创建一个与其参数具有相同键-值映射关系的新映射。实际上，后一个构造方法允许用户复制任意映射，生成所需类的一个等价映射。尽管无法强制执行此建议（因为接口不能包含构造方法），但是 JDK 中所有通用的映射实现都遵从它。

- containsKey(Object key)/containsValue(Object value)：是否包含键/值
- get(Object key)：返回键对应的值
- put(K key, V value)/putAll(Map map)：添加一个或多个元素
- remove(Object key)：删除键对应的键值对
- entrySet()：返回所有键值对的Set集合
- keySet()：返回键的Set集合
- values()：返回值的Collection集合

## Map.Entry

Map.Entry是Map的一个内部接口，表示键值对。

- getKey()
- getValue()
- setValue(V object)

## AbstractMap

AbstractMap抽象类实现了Map接口中的大部分方法。

- 要实现不可修改的映射，编程人员只需扩展此类并提供 entrySet 方法的实现即可，该方法将返回映射的映射关系 set 视图。通常，返回的 set 将依次在 AbstractSet 上实现。此 set 不支持 add() 或 remove() 方法，其迭代器也不支持 remove() 方法。

- 要实现可修改的映射，编程人员必须另外重写此类的 put 方法（否则将抛出 UnsupportedOperationException），entrySet().iterator() 返回的迭代器也必须另外实现其 remove 方法。

## SortedMap

SortedMap是一个继承于Map接口的接口。它是一个有序的SortedMap键值映射。

SortedMap的排序方式有两种：自然排序 或者 用户指定比较器。 插入有序 SortedMap 的所有元素都必须实现 Comparable 接口（或者被指定的比较器所接受）。

	abstract Comparator<? super K>     comparator()
	abstract K                         firstKey()
	abstract K                         lastKey()
	abstract SortedMap<K, V>           headMap(K endKey)
	abstract SortedMap<K, V>           subMap(K startKey, K endKey)
	abstract SortedMap<K, V>           tailMap(K startKey)

## NavigableMap

NavigableMap是继承于SortedMap的接口。它是一个可导航的键-值对集合，具有了为给定搜索目标报告最接近匹配项的导航方法。
NavigableMap分别提供了获取“键”、“键-值对”、“键集”、“键-值对集”的相关方法。

	abstract Entry<K, V>             ceilingEntry(K key)
	abstract Entry<K, V>             firstEntry()
	abstract Entry<K, V>             floorEntry(K key)
	abstract Entry<K, V>             higherEntry(K key)
	abstract Entry<K, V>             lastEntry()
	abstract Entry<K, V>             lowerEntry(K key)
	abstract Entry<K, V>             pollFirstEntry()
	abstract Entry<K, V>             pollLastEntry()
	abstract K                       ceilingKey(K key)
	abstract K                       floorKey(K key)
	abstract K                       higherKey(K key)
	abstract K                       lwoerKey(K key)
	abstract NavigableSet<K>         descendingKeySet()
	abstract NavigableSet<K>         navigableKeySet()
	abstract NavigableMap<K, V>      descendingMap()
	abstract NavigableMap<K, V>      headMap(K toKey, boolean inclusive)
	abstract SortedMap<K, V>         headMap(K toKey)
	abstract SortedMap<K, V>         subMap(K fromKey, K toKey)
	abstract NavigableMap<K, V>      subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive)
	abstract SortedMap<K, V>         tailMap(K fromKey)
	abstract NavigableMap<K, V>      tailMap(K fromKey, boolean inclusive)

	NavigableMap除了继承SortedMap的特性外，它的提供的功能可以分为4类：

1. 第1类，提供操作键-值对的方法。
               lowerEntry、floorEntry、ceilingEntry 和 higherEntry 方法，它们分别返回与小于、小于等于、大于等于、大于给定键的键关联的 Map.Entry 对象。
               firstEntry、pollFirstEntry、lastEntry 和 pollLastEntry 方法，它们返回和/或移除最小和最大的映射关系（如果存在），否则返回 null。
               
2. 第2类，提供操作键的方法。这个和第1类比较类似
               lowerKey、floorKey、ceilingKey 和 higherKey 方法，它们分别返回与小于、小于等于、大于等于、大于给定键的键。

3. 第3类，获取键集。
              navigableKeySet、descendingKeySet分别获取正序/反序的键集。

4. 第4类，获取键-值对的子集。