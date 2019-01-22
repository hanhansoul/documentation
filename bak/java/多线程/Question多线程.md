Q：什么是线程，什么是进程，线程和进程有什么区别？

进程是一个具有一定独立功能的程序在一个数据集上的一次动态执行的过程，是操作系统进行资源分配和调度的一个独立单位，是应用程序运行的载体。进程一般由程序、数据集合和进程控制块三部分组成。

线程时轻量级的进程，是程序执行流的最小单元。一个标准的线程由线程ID，当前指令指针，寄存器集合和堆栈组成。在单个程序中同时运行多个线程完成不同的工作，称为多线程。

同一时间，CPU中只能有一个进程在运行，而一个进程中可以包含一个或多个线程，各个线程之间共享程序的内存空间。

1. 线程是程序执行的最小单位，而进程是操作系统分配资源的最小单位；
2. 一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路线；
3. 进程之间相互独立，但同一进程下的各个线程之间共享程序的内存空间(包括代码段、数据集、堆等)及一些进程级的资源(如打开文件和信号)，某进程内的线程在其它进程不可见；
4. 调度和切换：线程上下文切换比进程上下文切换要快得多。

Q：使用线程的优点和缺点是什么？

Q：线程的生命周期

1. 创建状态
2. 就绪状态
3. 运行状态
4. 阻塞状态
5. 终止状态

Q：多线程的优势

在多线程程序中，多个线程被并发的执行以提高程序的效率，CPU不会因为某个线程需要等待资源而进入空闲状态。多个线程共享堆内存，因此创建多个线程去执行一些任务会比创建多个进程更好。

Q：用户线程和守护线程有什么区别？

当我们在Java程序中创建一个线程，它就被称为用户线程。一个守护线程是在后台执行并且不会阻止JVM终止的线程。当没有用户线程在运行的时候，JVM关闭程序并且退出。一个守护线程创建的子线程依然是守护线程。

Q： 我们如何创建一个线程？

1. 实现Runnable接口，然后传递给Thread构造函数
2. 直接继承Thread类

Q：可以直接调用Thread类的run()方法么？

可以。但只会直接运行run()方法中的程序，不会创建新的线程。必须调用start()方法创建新线程。

Q：如何让正在运行的线程暂停一段时间？

我们可以使用Thread类的Sleep()方法让线程暂停一段时间。需要注意的是，这并不会让线程终止，也不会如wait()方法一样是线程释放同步锁，一旦从休眠中唤醒线程，线程的状态将会被改变为就绪状态，并且根据线程调度进行执行。

Q：你对线程优先级的理解是什么？

每一个线程都是有优先级的，一般来说，高优先级的线程在运行时会具有优先权，但这依赖于线程调度的实现，这个实现是和操作系统相关的(OS dependent)。我们可以定义线程的优先级，但是这并不能保证高优先级的线程会在低优先级的线程前执行。线程优先级是一个int变量(从1-10)，1代表最低优先级，10代表最高优先级。

Q：什么是线程调度器(Thread Scheduler)和时间分片(Time Slicing)？

线程调度器是一个操作系统服务，它负责为Runnable状态的线程分配CPU时间。一旦我们创建一个线程并启动它，它的执行便依赖于线程调度器的实现。时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配CPU时间可以基于线程优先级或者线程等待的时间。线程调度并不受到Java虚拟机控制，所以由应用程序来控制它是更好的选择（也就是说不要让你的程序依赖于线程的优先级）。

Q：什么是线程调度器(Thread Scheduler)和时间分片(Time Slicing)？

线程调度器是一个操作系统服务，它负责为Runnable状态的线程分配CPU时间。一旦我们创建一个线程并启动它，它的执行便依赖于线程调度器的实现。

时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配CPU时间可以基于线程优先级或者线程等待的时间。线程调度并不受到Java虚拟机控制，所以由应用程序来控制它是更好的选择（也就是说不要让你的程序依赖于线程的优先级）。

Q：在多线程中，什么是上下文切换(context-switching)？

上下文切换是存储和恢复CPU状态的过程，它使得线程执行能够从中断点恢复执行。上下文切换是多任务操作系统和多线程环境的基本特征。

Q：你如何确保main()方法所在的线程是Java程序最后结束的线程？

通过join()方法使得main线程等待所有子线程结束后才终止。

Q：为什么线程通信的方法wait(), notify()和notifyAll()被定义在Object类里？

Java的每个对象中都有一个锁(monitor，也可以成为监视器) 并且wait()，notify()等方法用于等待对象的锁或者通知其他线程对象的监视器可用。在Java的线程中并没有可供任何对象使用的锁和同步器。这就是为什么这些方法是Object类的一部分，这样Java的每一个类都有用于线程间通信的基本方法。

Q：为什么Thread类的sleep()和yield()方法是静态的？

Thread类的sleep()和yield()方法将在当前正在执行的线程上运行。所以在其他处于等待状态的线程上调用这些方法是没有意义的。这就是为什么这些方法是静态的。它们可以在当前正在执行的线程中工作，并避免程序员错误的认为可以在其他非运行线程调用这些方法。

Q：如何确保线程安全？

- 原子类
- 并发锁
- volatile关键字
- final类
- 线程安全类

Q：volatile关键字在Java中有什么作用？

volatile关键字作用：
1. 内存可见性
2. 阻止语句重排序

可以作为一个简单轻量级的同步锁。

Q：同步方法和同步块，哪个是更好的选择？

同步块是更好的选择，因为它不会锁住整个对象（当然你也可以让它锁住整个对象）。同步方法会锁住整个对象，哪怕这个类中有多个不相关联的同步块，这通常会导致他们停止执行并需要等待获得这个对象上的锁。

Q：如何创建守护线程？

使用Thread类的setDaemon(true)方法可以将线程设置为守护线程，需要注意的是，需要在调用start()方法前调用这个方法，否则会抛出IllegalThreadStateException异常。

---

Q：什么是ThreadLocal?

ThreadLocal类的作用是为每个线程都创建一个变量副本，每个线程都可以修改自己所拥有的变量副本,，而不会影响其他线程的副本。

ThreadLocal用于创建线程的本地变量，一个对象的所有线程会共享它的全局变量，所以这些变量不是线程安全的，我们可以使用同步，也可以选择ThreadLocal变量。

每个线程都会拥有他们自己的Thread变量，它们可以使用get()set()方法去获取他们的默认值或者在线程内部改变他们的值。ThreadLocal实例通常是希望它们同线程状态关联起来是private static属性。

Q：实现一个类似的ThreadLocal类

	public class MyThreadLocal<T> {
 
	    private Map<Thread, T> container = 
	    	Collections.synchronizedMap(new HashMap<Thread, T>());
	 
	    public void set(T value) {
	        container.put(Thread.currentThread(), value);
	    }
	 
	    public T get() {
	        Thread thread = Thread.currentThread();
	        T value = container.get(thread);
	        if (value == null && !container.containsKey(thread)) {
	            value = initialValue();
	            container.put(thread, value);
	        }
	        return value;
	    }
	 
	    public void remove() {
	        container.remove(Thread.currentThread());
	    }
	 
	    protected T initialValue() {
	        return null;
	    }
	}

Q：ThreadLocal使用实例

1. 多线程情况下，通过ThreadLocal存放多个JDBC连接，实现连接之间的隔离。
2. session管理

Q：ThreadLocal实现原理

ThreadLocal类包含的方法：

	public T get() { }
	public void set(T value) { }
	public void remove() { }
	protected T initialValue() { }

首先，在每个线程Thread内部有一个ThreadLocal.ThreadLocalMap类型的成员变量threadLocals，这个threadLocals就是用来存储实际的变量副本的，键值为当前ThreadLocal变量，value为变量副本（即T类型的变量）。

初始时，在Thread里面，threadLocals为空，当通过ThreadLocal变量调用get()方法或者set()方法，就会对Thread类中的threadLocals进行初始化，并且以当前ThreadLocal变量为键值，以ThreadLocal要保存的副本变量为value，存到threadLocals。

然后在当前线程里面，如果要使用副本变量，就可以通过get方法在threadLocals里面查找。

---

Q：什么是Thread Group？为什么建议使用它？

Q：什么是Java线程转储(Thread Dump)，如何得到它？

线程转储是一个JVM活动线程的列表，它对于分析系统瓶颈和死锁非常有用。有很多方法可以获取线程转储——使用Profiler，Kill -3命令，jstack工具等等。我更喜欢jstack工具，因为它容易使用并且是JDK自带的。由于它是一个基于终端的工具，所以我们可以编写一些脚本去定时的产生线程转储以待分析。

Q：什么是死锁(Deadlock)？如何分析和避免死锁？

死锁是指两个以上的线程永远阻塞的情况，这种情况产生至少需要两个以上的线程和两个以上的资源。

分析死锁，我们需要查看Java应用程序的线程转储。我们需要找出那些状态为BLOCKED的线程和他们等待的资源。每个资源都有一个唯一的id，用这个id我们可以找出哪些线程已经拥有了它的对象锁。

避免嵌套锁，只在需要的地方使用锁和避免无限期等待是避免死锁的通常办法，去学习如何分析死锁。

Q：Condition与同步锁使用的wait()和notify()方法有什么区别？

Condition可以提供更加精细的控制，使程序可以唤醒指定的线程，而wait()和notify()会不加区分的唤醒所有线程。

Q：ReentrantLock与Condition的应用场景

1. 生产者/消费者模型
2. 阻塞队列的读写控制

Q：临界区

访问共用资源，而这些共用资源又无法同时被多个线程访问的代码段。

Q：Java中锁实现的方式

1. volatile
2. synchronized
3. concurrency包中的锁

Q：CountDownLatch和CyclicBarrier的区别

1. CountDownLatch的作用是允许1或N个线程等待其他线程完成执行；而CyclicBarrier则是允许N个线程相互等待。
2. CountDownLatch的计数器无法被重置；CyclicBarrier的计数器可以被重置后使用，因此它被称为是循环的barrier。

Q：CountDownLatch实现原理

通过AQS的共享锁实现。

Q：CyclicBarrier

- CyclicBarrier是一个同步辅助类，它允许一组线程相互等待直到所有线程都到达一个公共的屏障点。
- 在程序中有固定数量的线程，这些线程有时候必须等待彼此，这种情况下，使用CyclicBarrier很有帮助。
- 这个屏障之所以用循环修饰，是因为在所有的线程释放彼此之后，这个屏障是可以重新使用的。

CyclicBarrier常用于多线程分组计算。