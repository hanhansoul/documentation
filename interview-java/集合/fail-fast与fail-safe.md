# fail-fast机制

fail-fast 机制是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。

当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

## fail-fast的解决方式

fail-fast机制，是一种错误检测机制。它只能被用来检测错误，因为JDK并不保证fail-fast机制一定会发生。若在多线程环境下使用fail-fast机制的集合，建议使用“java.util.concurrent包下的类”去取代“java.util包下的类”。

例如，使用CopyOnWriteArrayList替代ArrayList。

## fail-fast的原理

在调用next()与remove()方法时，执行checkForComodification()方法，检查modCount是否等于expectedModCount，若不相等，则抛出ConcurrentModificationException异常，产生fail-fast事件。

凡是涉及到修改集合中的元素个数时，就会改变modCount的值，每次修改modCount都会递增1。

# fail-safe

// TO-DO