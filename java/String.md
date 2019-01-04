# String / StringBuffer / StringBuilder

- 运行速度：StringBuilder > StringBuffer > String

## String

String类字符串可以通过两种方式实例化
1. `String str = "abc";`
2. `String str = new String("abc")`

String是不可变的，可以用于多线程或多函数间共享。

通过第一种方式初始化时，首先会在JVM字符串池中查找具有相同值的字符串。如果有相同值的字符串，则对象引用指向该字符串常量，否则创建一个新的字符串。

通过new创建的字符串，都会在堆内存中创建一个新的对象。

	String a = "abc";
	String b = "abc";
	System.out.println(a == b);		// true

	String c = new String("abc");
    String d = new String("abc");
    System.out.println(c == d);		// false

String重载了+运算符，用于连接两个或多个String对象，其底层是通过StringBuffer实现的。

String重载了equals()和hashCode()函数，只有当String对象中内容完全相同时，equals()才会返回true。

String以UTF-16格式存储字符串。

String是一个final类，不能够被继承。

## StringBuffer

- append()
- insert()
- delete()
- replace()
- substring()
- indexOf()
- lastIndexOf()
- reverse()

StringBuffer是可变对象，提供了append()、insert()、delete()和substring()等方法。

StringBuffer的所有方法都是同步的。

## StringBuilder

StringBuilder类与StringBuffer类类似，不同的是，StringBuilder是线程不安全的，但在单线程情况下效率更高。

## 区别

1. String：适用于少量的字符串操作的情况
2. StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况
3. StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况

