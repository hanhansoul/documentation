# java多态的实现原理

## 多态的概念

JAVA类被jvm加载运行时根据调用该方法的对像实例的类型来决定选择调用哪个方法则被称为运行时多态。也叫动态绑定：是指在执行期间判断所引用对象实例的实际类型，根据其实际的类型调用其相应的方法。

## JVM多态的实现

Java中的多态在语义上与上面分析C++的原理是相同的，Java在JVM中的多态机制并没有跳出这个圈也采用了 vftable 来实现动态绑定。

JVM的vftable机制与C++的vftable机制之间的不同点在于， C++的vftable在编译期间便由编译器完成分析和模型构建，而JVM的vftable则在JVM运行期类被加载时进行动态构建。