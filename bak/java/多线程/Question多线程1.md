https://blog.csdn.net/ll666634/article/details/78615505

Q：现在有T1、T2、T3三个线程，你怎样保证T2在T1执行完后执行，T3在T2执行完后执行？

1. 使用join()
2. 使用CountDownLatch
3. 使用CyclicBarrier

Q：join()实现原理

// TODO

Q：在Java中Lock接口比synchronized块的优势是什么？你需要实现一个高效的缓存，它允许多个用户读，但只允许一个用户写，以此来保持它的完整性，你会怎样去实现它？

Lock接口采用CAS与乐观锁等机制，synchronized采用悲观锁，Lock接口效率更高，功能也更加强大（新版本中synchronized实现被优化了）。

https://blog.csdn.net/ll666634/article/details/78615505

Q：在java中wait和sleep方法的不同？

最大的不同是在等待时wait会释放锁，而sleep一直持有锁。Wait通常被用于线程间交互，sleep通常被用于暂停执行。

- Java的多线程锁是挂在对象上的，并不是在方法上的。即每个对象都有一个锁，当遇到类似synchronized的同步需要时，就会监视(monitor)每个想使用本对象的线程按照一定的规则来访问，规则也就是在同一时间内只能有一个线程能访问此对象。 
- Java中获取锁的单位是线程。当线程A获取了对象B的锁，也就是对象B的持有标记上写的是线程A的唯一标识，在需要同步的情况下的话，只有线程A能访问对象B。 
- Thread常用方法有：start/stop/yield/sleep/interrupt/join等，他们是线程级别的方法，所以并不会太关心锁的具体逻辑。 
- Object的线程有关方法是：wait/wait(事件参数)/notify/notifyAll，他们是对象的方法，所以使用的时候就有点憋屈了，必须当前线程获取了本对象的锁才能使用，否则会报异常。但他们能更细粒度的控制锁，可以释放锁。

Q：实现阻塞队列

BlockingQueue原理
- ArrayBlockingQueue
- LinkedBlockingQueue
- PriorityBlockingQueue
- DelayQueue
- SynchronousQueue

ReentrantLock与Condition

Q：实现生产者——消费者问题

- 用wait() / notify()方法 
- 用Lock的多Condition方法 
- BlockingQueue阻塞队列方法

Q：死锁的原理。用Java写一个会导致死锁的程序，你将怎么解决？

Q：什么是原子操作，Java中的原子操作是什么？

原子操作是不可分割的操作，一个原子操作中间是不会被其他线程打断的，所以不需要同步一个原子操作。 

Q：Java中的volatile关键是什么作用？怎样使用它？在Java中它跟synchronized方法有什么不同？

1. 内存可见性
2. 阻止语句重排序
3. volatile不能保证原子性

Q：volatile如何作为一个轻量级锁使用？

// TODO
https://www.cnblogs.com/getting-better/p/3245993.html

Q：volatile与sychronized区别

1. volatile轻量级，只能修饰变量；synchronized重量级，还可修饰方法。 
2. volatile不会造成线程的阻塞，而synchronized可能会造成线程的阻塞。

Q：单例模式两次检查如何实现？为什么要使用volatile变量？

	private static volatile Singleton _instance;

	public static Singleton getInstanceDC() {
	    if (_instance == null) {                // Single Checked
	        synchronized (Singleton.class) {
	            if (_instance == null) {        // Double checked
	                _instance = new Singleton();
	            }
	        }
	    }
	    return _instance;
	}

Q：饱汉模式与饿汉模式


Q：你将如何使用thread dump？你将如何分析Thread dump？

Q：为什么我们调用start()方法时会执行run()方法，为什么我们不能直接调用run()方法？

当你调用start()方法时你将创建新的线程，并且执行在run()方法里的代码。但是如果你直接调用run()方法，它不会创建新的线程也不会执行调用线程的代码。

Q：Java中你怎样唤醒一个阻塞的线程？

1. notify() / notifyAll()

阻塞方法分类：

- 会抛出InterruptedException的方法：wait、sleep、join、Lock.lockInterruptibly等，针对这类方法，我们在线程内部处理好异常(要不完全内部处理，要不把这个异常抛出去)，然后就可以实现唤醒。 
- 不会抛InterruptedException的方法：Socket的I/O,同步I/O，Lock.lock等。对于I/O类型，我们可以关闭他们底层的通道，比如Socket的I/O，关闭底层套接字，然后抛出异常处理就好了;比如同步I/O，关闭底层Channel然后处理异常。对于Lock.lock方法，我们可以改造成Lock.lockInterruptibly方法去实现。

Q：什么是不可变对象，它对写并发应用有什么帮助？写一个不可变对象。为什么String是不可变对象？

// TODO

不可变对象是那些一旦被创建，它们的状态就不能被改变的对象，每次对他们的改变都是产生了新的immutable的对象，而mutable Objects(可变对象)就是那些创建后，状态可以被改变的Objects.

如何在Java中写出Immutable的类？ 
1. immutable对象的状态在创建之后就不能发生改变，任何对它的改变都应该产生一个新的对象。 
2. immutable类的所有的属性都应该是final的。 
3. 对象必须被正确的创建，比如：对象引用在对象创建过程中不能泄露(leak)。 
4. 对象应该是final的，以此来限制子类继承父类，以避免子类改变了父类的immutable特性。 
5. 如果类中包含mutable类对象，那么返回给客户端的时候，返回该对象的一个拷贝，而不是该对象本身（该条可以归为第一条中的一个特例）

使用Immutable类的好处： 
1. Immutable对象是线程安全的，可以不用被synchronize就在并发环境中共享 
2. Immutable对象简化了程序开发，因为它无需使用额外的锁机制就可以在线程间共享 
3. Immutable对象提高了程序的性能，因为它减少了synchroinzed的使用 
4. Immutable对象是可以被重复使用的，你可以将它们缓存起来重复使用，就像字符串字面量和整型数字一样。你可以使用静态工厂方法来提供类似于valueOf()这样的方法，它可以从缓存中返回一个已经存在的Immutable对象，而不是重新创建一个新对象。

Q：你在多线程环境中遇到的常见的问题是什么？你是怎么解决它的？

// TODO

多线程和并发程序中常遇到的有Memory-interface、竞争条件、死锁、活锁和饥饿。问题是没有止境的，如果你弄错了，将很难发现和调试。这是大多数基于面试的，而不是基于实际应用的Java线程问题。

Q：Java中绿色线程和本地线程的区别？

Q：线程和进程的区别？

多线程的上下文切换是什么？
死锁和活锁的区别？死锁和饥饿的区别？
Java 中使用什么线程调度算法？
Java 中线程调度是什么？
线程中如何处理某个未处理异常？
什么是线程组？为什么 Java 中不建议使用线程组？
为什么使用 Executor 框架比直接创建线程要好？
Java 中 Executor 和 Executors 的区别？答案
在 windows 和 linux 系统上分别如何找到占用 CPU 最多的线程？
