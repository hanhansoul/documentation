Q：将Map转换为List类型

Map提供keySet()、entrySet()与values()方法返回Map的键值对的集合对象。

	List keyList = new ArrayList(map.keySet());
	List valueList = new ArrayList(map.values());
	List entryList = new ArrayList(map.entrySet());

Q：遍历Map

	for(Entry entry: map.entrySet()) {
		K key = entry.getKey();
		V value = entry.getValue();
	}

	Iterator it = map.entrySet().iterator();
	while(it.hasNext()) {
		Entry entry = it.next();
		K key = entry.getKey();
		V value = entry.getValue();
	}

Q：通过Key来对Map排序

1. 通过Collections.sort()对map.entrySet()集合排序
2. 通过将原map添加到一个新的TreeMap对象进行排序

Q：对value对Map进行排序

通过Collections.sort()对map.values()集合排序。

Q：初始化一个静态常量Map对象

在static代码块中初始化map，并通过Collections.unmodifiableMap将该map置为不可变。

Q：线程安全的HashMap

Q：创建一个空的Map

通过Collections.emptyMap()创建一个空Map对象。
