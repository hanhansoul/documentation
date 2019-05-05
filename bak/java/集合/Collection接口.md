## Collection接口

Collection接口包含了集合的基本操作：添加、删除、清空、遍历、是否为空、获取大小，是否包含某元素。

	abstract void            clear()
	abstract boolean         contains(Object object)
	abstract boolean         containsAll(Collection<?> collection)
	abstract int             hashCode()
	abstract boolean         isEmpty()



// TO-DO

	toArray()
	toArray(T[] array)

	// 并集
	addAll(Collection<?> collection)
	// 差集
	removeAll(Collection<?> collection)
	// 交集
	retainAll(Collection<?> collection)

	abstract Iterator<E>     iterator()

# Collection

## List接口

继承于Collection接口，表示有序队列。List中每一个元素有一个以0起始的索引值，List中允许重复元素。

// TO-DO

	listIterator(Object object)
	listIterator()

	subList(int start, int end)

	toArray(T[] array)
	toArray()

## Set

继承于Collection接口，表示集合类型，Set中没有重复元素。

// TO-DO

	iterator()

	toArray(T[] array)
	toArray()

# AbstractCollection

抽象类，实现了Collection中除了iterator()和size()之外的函数。

用于实现Collection接口中大部分的函数，方便其他类实现Collection，例如ArrayList、LinkedList等。

## AbstractList

继承于AbstractCollection，实现了iterator()、size()、get(int location)等方法。

## AbstractSet

继承于AbstractCollection，实现了iterator()、size()、get(int location)等方法。

# Iterator

是集合的迭代器接口，集合可以通过Iterator遍历集合中的元素。

Iterator提供了遍历的基本操作：是否存在下一个元素、获取下一个元素、删除当前元素。

Iterator提供了fail-fast机制，即当某一个线程通过iterator去遍历某集合的过程中，若该集合内容被其他线程改变，则该线程将抛出ConcurrentModificationException异常。

## ListIterator

ListIterator接口继承于Iterator，是队列迭代器，提供双向遍历List的功能。

相比于Iterator，增加了添加、是否存在上一个元素、获取上一个元素等操作。