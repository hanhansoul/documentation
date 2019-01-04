# Collections

Collections是一个提供一些处理容器类静态方法的类。

## 不可变集合视图

返回原集合的一个不可变视图集合。

- unmodifiableCollection()
- unmodifiableSet()
- unmodifiableSortedSet()
- unmodifiableNavigableSet()
- unmodifiableList()
- unmodifiableMap()
- unmodifiableSortedMap()
- unmodifiableNavigableMap()

返回的类型

- UnmodifiableCollection
- UnmodifiableSet
- UnmodifiableSortedSet
- UnmodifiableNavigableSet
- UnmodifiableList
- UnmodifiableRandomAccessList
- UnmodifiableMap
- UnmodifiableSortedMap
- UnmodifiableNavigableMap

## 线程同步的集合视图

返回原集合的线程同步的集合视图，为每个集合操作添加synchronized关键字。

- synchronizedCollection()
- synchronizedSet()
- synchronizedSortedSet()
- synchronizedNavigableSet()
- synchronizedList()
- synchronizedMap()
- synchronizedSortedMap()
- synchronizedNavigableMap()

返回的类型

- SynchronizedCollection
- SynchronizedSet
- SynchronizedSortedSet
- SynchronizedNavigableSet
- SynchronizedList
- SynchronizedRandomAccessList
- SynchronizedMap
- SynchronizedSortedMap
- SynchronizedNavigableMap

## 动态类型安全视图

方法用于获取指定集合的一个动态类型安全视图。

返回的检查容器，通过将原容器包装，对传入的对象进行类型检查。

- CheckedCollection
- CheckedQueue
- CheckedSet
- CheckedSortedSet
- CheckedNavigableSet
- CheckedList
- CheckedRandomAccessList
- CheckedMap
- CheckedSortedMap
- CheckedNavigableMap

## 空对象

返回对应的空对象。

emptyIterator()
emptyListIterator()
emptyEnumeration()
emptySet()
emptySortedSet()
emptyNavigableSet()
emptyList()
emptyMap()
emptySortedMap()
emptyNavigableMap()

## 排序

Collections.sort()采用归并排序，是稳定排序。

	<T extends Comparable<? super T>> void sort(List<T> list)
	<T> void sort(List<T> list, Comparator<? super T> c)

## 二分法查找

通过二分法在list中查找元素key。

	<T> int binarySearch(List<? extends Comparable<? super T>> list, T key)
	<T> int binarySearch(List<? extends T> list, T key, Comparator<? super T> c)

##

	// 倒置list中元素的顺序，时间复杂度为线性时间复杂度
	reverse(List<?> list)

	// 随机排列list中的元素顺序
	shuffle(List<?> list);
	shuffle(List<?> list, Random random)

	// 交换list中特定位置的两个元素
	void swap(List<?> list, int i, int j)

	// 将list中所有元素值替换为给定值
	<T> void fill(List<? super T> list, T obj)

	// 将一个src中的所有元素拷贝到dest中
	<T> void copy(List<? super T> dest, List<? extends T> src)

	// 返回最大值/最小值
	<T extends Object & Comparable<? super T>> T min(Collection<? extends T> coll)
	<T> T min(Collection<? extends T> coll, Comparator<? super T> comp)
	<T extends Object & Comparable<? super T>> T max(Collection<? extends T> coll)
	<T> T max(Collection<? extends T> coll, Comparator<? super T> comp)

	// 
	rotate(List<?> list, int distance)

	// 将list中出现的所有给定元素值替换为另一个值。
	<T> boolean replaceAll(List<T> list, T oldVal, T newVal)

	// 返回source中子串target的第一个位置，若不是子串，则返回-1。
	int indexOfSubList(List<?> source, List<?> target)
	int lastIndexOfSubList(List<?> source, List<?> target)

	// 返回一个倒转了的新Comparator
	<T> Comparator<T> reverseOrder()
	// 返回一个倒转cmp的新Comparator
	<T> Comparator<T> reverseOrder(Comparator<T> cmp)

	// 返回一个给定Collection的Enumeration
	<T> Enumeration<T> enumeration(final Collection<T> c)

	// 返回一个ArrayList，包含Enumeration返回的元素
	<T> ArrayList<T> list(Enumeration<T> e)

	// 返回o在c出现的次数
	int frequency(Collection<?> c, Object o)

	// 如果c1与c2没有相同元素，则返回true，否则返回false
	boolean disjoint(Collection<?> c1, Collection<?> c2)

	// 将elements加入到容器c中，该方法比容器自身的addAll()方法性能更好
	<T> boolean addAll(Collection<? super T> c, T... elements)

	<E> Set<E> newSetFromMap(Map<E, Boolean> map)

	<T> Queue<T> asLifoQueue(Deque<T> deque)

## 单例

	// 返回一个只包含一个给定对象的不可变set
	<T> Set<T> singleton(T o)

	// 返回一个只包含一个给定对象的不可变list
	<T> List<T> singletonList(T o)

	// 返回一个只包含一个给定键值对的不可变map
	<K,V> Map<K,V> singletonMap(K key, V value)

	singletonIterator
	singletonSpliterator

# Arrays

## 排序

Arrays.sort()采用双轴快速排序算法，Arrays.sort()函数包含所有基本类型数组的重载。

int类型数组的排序算法如下，其他类型数组类似：

	// 将数组以升序排列
	void sort(int[] a)
	// 将给定区间范围内的元素以升序排列
	void sort(int[] a, int fromIndex, int toIndex)


Arrays.parallelSort()采用平行归并排序算法，同样包含所有基本类型数组的重载。

	// 将数组以升序排列
	void parallelSort(int[] a)
	// 将给定区间范围内的元素以升序排列
	void parallelSort(int[] a, int fromIndex, int toIndex)

## 并行前缀

## 二分查找

# Array

为数组提供静态方法的工具类。