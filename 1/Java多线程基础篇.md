## 线程的状态

线程包括以下五种状态：

- 新建状态：线程对象被创建后，进入新建状态。
- 就绪状态：在线程处于新建状态后，**其他线程**通过`thread.start()`方法启动该线程后，该线程就处于就绪状态了，随时都可以被CPU调度运行。
- 运行状态：线程获得CPU控制权，进入执行状态。线程只能从就绪状态进入运行状态。
- 阻塞状态：线程因为某些原因放弃CPU控制权，暂停运行。知道线程再次进入就绪状态，才可能再次进入运行状态。阻塞状态分为三种情况：
	- 等待阻塞：通过`wait()`方法，让线程等待其他线程完成工作。
	- 同步阻塞：获取`synchronized`同步锁失败后进入阻塞状态。
	- 其他阻塞：调用线程的`sleep()`或`join()`函数后，线程进入阻塞状态。
- 终止状态：线程执行完毕或因异常退出`run()`方法后，线程结束生命周期，处于终止状态。

`Object`类：定义了`wait()`、`notify()`、`nitifyAll()`等方法。
`Thread`类：定义了`sleep()`、`interrupt()`等方法。
`synchronized`关键字：分为同步函数与同步代码块，负责让线程获取同步锁。

## Runnable与Thread

- Runnable是接口
- Thread是类

`start()`与`run()`的区别：
- `start()`的作用是启动一个新线程，新线程执行相应的`run()`方法，`start()`不能被重复调用。
- `run()`与普通成员方法一样，可以重复调用。单独调用`run()`方法，会在当前线程中执行`run()`方法，并不会启动新的线程。

	// Demo.java 的源码
	class MyThread extends Thread{  
	    public MyThread(String name) {
	        super(name);
	    }

	    public void run(){
	        System.out.println(Thread.currentThread().getName()+" is running");
	    } 
	}; 

	public class Demo {  
	    public static void main(String[] args) {  
	        Thread mythread=new MyThread("mythread");

	        System.out.println(Thread.currentThread().getName()+" call mythread.run()");
	        mythread.run();

	        System.out.println(Thread.currentThread().getName()+" call mythread.start()");
	        mythread.start();
	    }  
	}

## `synchronized`关键字

java中的每一个对象都有且仅有一个同步锁，即同步锁是依赖于对象存在的。

带有`synchronized`关键字的方法和代码块被称为对象的“同步方法”与“同步代码块”，调用执行某对象的“同步方法”或“同步代码块”即表示获取了该对象的同步锁，而每一个对象又只包含一个同步锁。

不同线程的同步锁访问是互斥的，即对于某一个对象的同步锁，同一时间只能有一个线程获得。

`synchronized`关键字规则：

1. 当一个线程调用某对象的“同步方法”或“同步代码块”时，其他线程对该对象的**当前**“同步方法”或“同步代码块”的调用将被阻塞。
2. 当一个线程调用某对象的“同步方法”或“同步代码块”时，其他线程对该对象的“非同步方法”或“非同步代码块”的调用不会被阻塞。
3. 当一个线程调用某对象的“同步方法”或“同步代码块”时，其他线程对该对象的**其他**“同步方法”或“同步代码块”的调用将被阻塞。

根据同步锁的持有对象的不同，又可以分为实例锁与全局锁

- 实例锁：每一个实例都有一个独立的同步锁。
- 全局锁：每一个类只有一个全局锁。


	public class A
		// synchronized的锁为A的实例的锁
		public synchronized void func() {
			
		}

		// synchronized的锁为A的类的锁
		public static synchronized void static_func() {

		}
	}

- 对于普通方法同步，锁是当前实例对象
- 对于静态方法同步，锁是当前类的 Class 对象
- 对于方法块同步，锁是 Synchronized 括号里的对象

---

`synchronized`的机制是通过monitorenter与monitorexit指令实现的。

Java对象头与Monitor对象

## 线程的等待与唤醒

`Object`类定义了`wait()`、`notify()`与`notifyAll()`用于控制线程状态。

- `wait()`：当前线程释放所持有的的锁。
- `notify()`：唤醒在竞争当前对象锁的一个线程。
- `notifyAll()`：唤醒在竞争当前对象锁的所有线程。

`Object.notify()`

IllegalMonitorStateException

表明一个不是某个对象的监视器持有者的线程试图在该对象的监视器上`wait()`或试图唤醒在该监视器上等待的其他线程。

一个线程成为一个对象监视器的持有者的三种方式：
1. 通过执行对象的synchronized方法。
2. 通过执行对象的synchronized代码块。
3. 对于类，通过执行类的静态synchronized方法。