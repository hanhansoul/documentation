1. Excption与Error包结构。OOM你遇到过哪些情况，SOF你遇到过哪些情况。

2. 静态内部类和动态内部的区别

3. java多态的实现原理

4. 锁的等级

5. 写出生产者消费者模式

6. ThreadLocal设计理念与作用

7. Concurrent包中的类

8. foreach与正常for循环效率对比。

9. 反射的作用与原理

10. 泛型常用特点，List<String>能否转为List<Object>。

11. Java1.7与1.8新特性。

12. 设计模式：单例、工厂、适配器、责任链、观察者等等。

13. JNI的使用。

14. 内存模型/分区

15. GC收集器有哪些？CMS收集器与G1收集器的特点。

16. Minor GC与Full GC分别在什么时候发生？

17. 双亲委派模型

18. 分派：静态分派与动态分派。

19. Arrays.sort实现原理

20. foreach和while的区别

21. 线程池的种类，区别和使用场景

22. 分析线程池的实现原理和线程的调度过程
23. 线程池如何调优
24. 线程池的最大线程数目根据什么确定
25. LinkedHashMap
26. 反射的原理，反射创建类实例的三种方式是什么？
27. 反射中，Class.forName和ClassLoader区别
28. 简述NIO的最佳实践，比如netty，mina


jvm

1. 类的实例化顺序，比如父类静态数据，构造函数，字段，子类静态数据，构造函数，字段，他们的执行顺序
2. JVM内存分代
3. Java 8的内存分代改进
4. JVM垃圾回收机制，何时触发MinorGC等操作jvm中一次完整的GC流程（从ygc到fgc）是怎样的，重点讲讲对象如何晋升到老年代，几种主要的jvm参数等
5. 你知道哪几种垃圾收集器，各自的优缺点，重点讲下cms，g1
6. 新生代和老生代的内存回收策略
7. Eden和Survivor的比例分配等
9. 深入分析了Classloader，双亲委派机制
10. JVM的编译优化
11. 对Java内存模型的理解，以及其在并发中的
12. 应用指令重排序，内存栅栏等
13. OOM错误，stackoverflow错误，permgen space错误
14. JVM常用参数
15. tomcat结构，类加载器流程
16. volatile的语义，它修饰的变量一定线程安全吗
17. g1和cms区别,吞吐量优先和响应优先的垃圾收集器选择
18. 说一说你对环境变量classpath的理解？如果一个类不在classpath下，为什么会抛出ClassNotFoundException异常，如果在不改变这个类路径的前期下，怎样才能正确加载这个类？
19. 说一下强引用、软引用、弱引用、虚引用以及他们之间和gc的关系