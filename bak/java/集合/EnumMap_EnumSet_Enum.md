## enum



## EnumSet

https://www.cnblogs.com/swiftma/p/6044718.html

- EnumSet是一个专为枚举类设计的集合类，EnumSet中所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet时显式或隐式地指定。EnumSet的集合元素也是有序的，EnumSet以枚举值在Enum类的定义顺序来决定集合元素的顺序。

EnumSet在内部以位向量的形式存储，这种存储形式非常紧凑、高效，因此EnumSet对象占用内存很小，而且运行效率很好。尤其是当进行批量操作（如调用containsAll 和 retainAll方法）时，如果其参数也是EnumSet集合，则该批量操作的执行速度也非常快。

EnumSet集合不允许加入null元素。如果试图插入null元素，EnumSet将抛出NullPointerException异常。如果仅仅只是试图测试是否出现null元素、或删除null元素都不会抛出异常，只是删除操作将返回false，因为没有任何null元素被删除。

- allOf()
- complementOf()
- copyOf()
- noneOf()
- of()
- range()

## EnumMap