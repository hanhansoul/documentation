# JUC锁

java中的锁可以分为同步锁与JUC包中的锁。

## 同步锁

通过`synchronized`关键字进行同步，实现对竞争资源的互斥访问的锁。

## JUC锁

- Lock接口
- ReadWriteLock
- LockSupport阻塞原语
- Condition条件
- AbstractOwnableSynchronizer
- AbstractQueuedSynchronizer
- AbstractQueuedLongSynchronizer
- ReentrantLock独占锁
- ReentrantReadWriteLock读写锁
- CountDownLatch
- CyclicBarrier
- Semaphore

// TO-DO race condition 

## 互斥锁`ReentrantLock`

`ReentrantLock`是一个可重入的互斥锁，又被成为独占锁。

`ReentrantLock`在同一时间只能被一个线程持有。`ReentrantLock`是可重入的，可以被单个线程多次获取。

`ReentrantLock`是通过一个FIFO等待队列来管理获取该锁所有线程，根据获取锁的机制是否公平可以分为公平锁与非公平锁。

- 公平锁：等待队列中的线程依次排队获取锁。
- 非公平锁：在锁时可获取状态时，不管线程是否在队列的开头都可以获取锁。

### ReentrantLock的API

	// 获取锁
	lock()
	// 释放锁
	unlock()

## JUC锁

### AQS

`AbstractQueuedSynchronizer`类是java中用于管理锁的抽象类，提供了许多类管理的公共方法。

`AbstractQueuedSynchronizer`类是独占锁(例如`ReentrantLock`)和共享锁(例如`Semaphore`)的公共父类。

`AbstractQueuedSynchronizer`类别：

#### 独占锁

独占锁在同一时间只能被一个线程占有。

根据锁的获取机制，可以分为：
- 公平锁：线程通过CLH等待队列，按照先来后到的顺序公平的获取锁。
- 非公平锁：线程无视CLH等待队列规则，在锁处于可获取状态时，所有线程都可以获取锁。

独占锁包括：
- ReentrantLock
- ReentrantReadWriteLock.WriteLock

#### 共享锁

共享锁能够同时被多个线程同时持有。

共享锁包括：
- ReentrantReadWriteLock.ReadLock
- CyclicBarrier
- CountDownLatch
- Semaphore

### CLH队列

CLH队列是AQS中的一个FIFO队列，用于管理等待获取锁的线程。

CLH是一个非阻塞的FIFO队列，在并发条件下不会阻塞，而是通过自旋锁和CAS保证节点插入和移除的原子性。

### CAS函数

CAS（Compare And Swap）函数是比较并交换函数，属于原子操作函数。

通过CAS操作的数据都是以原子方式进行的，包括：
- compareAndSetHead()
- compareAndSetTail()
- compareAndSetNext()