# 泛型

## 不要使用原生态类型

声明中具有一个或多个类型参数的类或接口，就是泛型类或泛型接口。每种泛型定义一组参数化的类型，如List<String>。

每个泛型都定义一个原生态类型，即不带任何实际类型参数的泛型名称。如List<E>相对应的原生态类型是List。

泛型子类型化的规则：List<String>是List类型的一个子类型，而不是参数类型List<Object>的子类型，因此可以将List<String>赋值给List类型的参数，而不能将其赋值给List<Object>类型参数。

需要使用泛型，但不确定或不关心实际的类型参数，可以使用无限制的通配符类型`<?>`。

	static int numElementsInCommon(Set<?> s1, Set<?> s2) {
		int result = 0;
		for (Object o1: s1) {
			if (s2.contains(o1))
				result++;
		}
		return result;
	}

通配符类型是类型安全的，而原生类型是不安全的。

由于反省信息在运行时被擦除，因此有两个需要使用原生类型的例外情况：
1. 类文字中必须使用原生态类型，即List.class、String[].class都是合法的，而List<String>.class和List<?>.class都是不合法的。
2. 在参数化类型而非无限制通配符类型上使用instatnceof是不合法的。

## 列表优先于数组

数组与列表的不同：
1. 数组是协变的，即如果Sub类型是Super类型的子类型，则数组类型Sub[]就是Super[]的子类型。而泛型时不可变的，对于任意两个不同类型Type1和Type2，List<Type1>既不是List<Type2>的子类型，也不是List<Type2>的超类型。
2. 数组是具体化的，只有在运行时才知道并检查它们元素类型约束，而泛型是通过擦除来实现的，因此泛型只在编译时强化它们的类型信息，并在运行时抛弃。擦除就是使泛型可以与没有泛型的代码随意进行互用。

创建泛型、参数化类型或者类型参数的数组是非法的。以下创建表达式都是非法的：

	new List<E>[]
	new List<String>[]
	new E[]

创建泛型数组非法的原因是因为它不是类型安全的。

	List<String>[] stringLists = new List<String>[1];	// error
	List<Integer> intList = Arrays.asList(42);
	Object[] objects = stringLists;
	String s = stringLists[0].get(0);

不可具体化的类型是指其运行时表示法包含的信息比它的编译时表示法包含的信息更少的类型。唯一可具体化的参数化类型是无限制通配符类型，如List<?>或Map<?, ?>类型。