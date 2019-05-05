# java多线程

## 线程等待与唤醒

`wait()`方法的作用是让当前线程进入等待状态，而与调用该方法的对象无关。

java中的对象是通过monitor实现同步锁功能的。

每一个对象内部都包含一个monitor，通过monitorenter与monitorexit指令持有monitor或释放monitor。

进入`sychronized`代码块或函数时，便调用monitorenter来竞争同步锁，而在退出`sychronized`代码块或函数时，则执行monitorexit指令来释放同步锁。同一时间只能有一个线程持有一个对象的同步锁。

线程可以通过三种方式持有对象obj的monitor：

1. 调用对象obj的`sychronized`方法。
2. 调用在对象obj上同步的`sychronized`代码块，即`sychronized(obj) {...}`。
3. 调用类的静态`sychronized`方法。

只有持有当前对象同步锁的线程才能调用`wait()`、`notify()`或`notifyAll()`方法。即只有通过`sychronized`绑定同步了的对象才能调用`wait()`、`notify()`或`notifyAll()`方法，否则会抛出`IllegalMonitorStateException`异常。

### `wait()`

wait()的作用是让“当前线程”等待，而“当前线程”是指正在cpu上运行的线程。

### `notify()`、`wait()`方法定义在`Object`中而非`Thread`的原因

`Object`中的`wait()`, `notify()`等函数，和`synchronized`一样，会对“对象的同步锁”进行操作。

`wait()`会使“当前线程”等待，因为线程进入等待状态，所以线程应该释放它锁持有的“同步锁”，否则其它线程获取不到该“同步锁”而无法运行！

线程调用`wait()`之后，会释放它锁持有的“同步锁”；而且，根据前面的介绍，我们知道：等待线程可以被`notify()`或`notifyAll()`唤醒。现在，请思考一个问题：`notify()`是依据什么唤醒等待线程的？或者说，`wait()`等待线程和`notify()`之间是通过什么关联起来的？答案是：依据“对象的同步锁”。

负责唤醒等待线程的那个线程(我们称为“唤醒线程”)，它只有在获取“该对象的同步锁”(这里的同步锁必须和等待线程的同步锁是同一个)，并且调用`notify()`或`notifyAll()`方法之后，才能唤醒等待线程。虽然，等待线程被唤醒；但是，它不能立刻执行，因为唤醒线程还持有“该对象的同步锁”。必须等到唤醒线程释放了“对象的同步锁”之后，等待线程才能获取到“对象的同步锁”进而继续运行。

总之，`notify()`, `wait()`依赖于“同步锁”，而“同步锁”是对象锁持有，并且每个对象有且仅有一个！这就是为什么`notify()`, `wait()`等函数定义在Object类，而不是Thread类中的原因。

## 线程的让步

`Thread.yield()`方法是`Thread`的类方法，用于使当前线程让步，并由运行状态进入就绪状态。

我们知道，`wait()`的作用是让当前线程由“运行状态”进入“等待(阻塞)状态”的同时，也会释放同步锁。而`yield()`的作用是让步，它也会让当前线程离开“运行状态”。它们的区别是：

`yield()`：`Thread`类中的静态方法，调用后使当前线程进入就绪状态，但当前线程不会释放锁。之后当前线程与其他线程竞争获取CPU的执行权。因为`yield()`方法不会释放锁，因此其他需要获取该同步锁继续执行的线程就不会执行。

1. `wait()`是让线程由“运行状态”进入到“等待(阻塞)状态”，而`yield()`是让线程由“运行状态”进入到“就绪状态”。
2. `wait()`是会线程释放它所持有对象的同步锁，而`yield()`方法不会释放锁，其目的是为了令持有不同同步锁的线程能够竞争CPU的使用权。

`wait()`：`Object`类中的实例方法，当前线程所持有的同步锁对象调用该方法，会使当前线程进入U阻塞状态，并释放同步锁。

`notify()`

`notifyAll()`

`join()`：`Thread`类中的静态方法，让父线程等待子线程结束后才继续执行。父线程中调用`join()`后，父线程会阻塞直到子线程返回。

## 线程的休眠

`sleep()`函数是`Thread`类中的静态方法。

调用`sleep()`后使当前线程进入阻塞状态，但当前线程不会释放锁。之后其他线程会竞争获取CPU的执行权，而当前线程则需要休眠给定的时间后才会进入就绪状态。

- `wait()`的作用是让当前线程由“运行状态”进入“等待(阻塞)状态”的，同时释放同步锁。
- `sleep()`的作用是也让当前线程由“运行状态”进入到“休眠(阻塞)状态”，但sleep()则不会释放锁，阻塞的时长由`sleep()`函数参数确定。

## 线程的等待

`join()`函数是`Thread`类中的静态方法。

`join()`会令当前线程从“运行状态”进入“阻塞状态”，直到“主线程”等待“子线程”运行结束之后才能继续运行。

`join()`内部是通过`wait()`实现的。

## 线程的终止

`interrupt()`：`Thread`类的实例方法，用于中断给定的线程。

### 终止处于阻塞状态的线程

线程通过`sleep()`、`wait()`或`join()`方法进入阻塞状态，此时调用线程的`interrupt()`会将线程的中断标记设为true，由于处于阻塞状态，中断标记会被清除，抛出一个`InterruptedException`异常，并终止线程。

### 终止处于运行状态的线程

**线程中断标记**

`isInterrupted()`方法可以判断当前线程的中断标记是否为true。

	public void run() {
    try {
        // 1. isInterrupted()保证，只要中断标记为true就终止线程。
        while (!isInterrupted()) {
            // 执行任务...
        }
    } catch (InterruptedException ie) {  
        // 2. InterruptedException异常保证，当InterruptedException异常产生时，线程被终止。
    }
}

`interrupt()`方法并不会终止运行状态的线程，而是将中断标记设为true。

**额外标记**

	private volatile boolean flag = true;

	private void stopTask() {
		flag = false;
	}

	public void run() {
		while (flag) {
			// process ...
		}
	}

通过`stopTask()`将`flag`设为false。将`flag`定义为`volatile`，保证内存可见性。

`interrupted()`与`isInterrupted()`方法都用于检查线程的中断标记。不同的是`interrupted()`除了返回中断标记外，还会清除中断标记，将其设为false；而`isInterrupted()`仅返回中断标记。