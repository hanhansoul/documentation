# ArrayList

ArrayList是一个动态数组队列，容量能够动态增长。

继承于AbstractList，实现了List, RandomAccess, Cloneable, java.io.Serializable这些接口。

- AbstractList类/List接口：提供了List
- RandmoAccess接口：提供了随机访问功能，提供通过元素的索引序号方式获取元素对象。
- Cloneable接口：覆盖clone()。
- Serializable接口：提供序列化。

## 线程安全

ArrayList是线程不安全的。

例如，添加元素时，size++与elementData[size++]不是原子操作，可能出现线程A添加一个元素后，size还未增加，线程B又添加一个元素，则线程A添加的元素会被覆盖。

	// 添加元素e
	public boolean add(E e) {
		// 确定ArrayList的容量大小
		ensureCapacity(size + 1);  // Increments modCount!!
		// 添加e到ArrayList中
		elementData[size++] = e;
		return true;
	}

// TO-DO
Collections.synchronizedList

## 容量动态增长

ArrayList的默认初始容量为10。

若当前容量不足以容纳当前元素时，新的容量 = (原始容量 x 3) / 2，若原始容量为0，则先将其赋值为默认值10。

## toArray

toArray()用于将List类型转换为数组类型。ArrayList提供了2个toArray()函数：

	Object[] toArray()
	<T> T[] toArray(T[] contents)

调用toArray()时会抛出ClassCastException异常，这是因为toArray()返回的是Object[]数组，然后将Object[]转换为其他类型，如Integer[]等。而Java不支持向下转型，因此会抛出异常。解决方法是调用toArray(T[] contents)方法。

	ArrayList<Integer> v = new ArrayList<>();
	// ...
	Integer[] arr = (Integer[])v.toArray(new Integer[0]);