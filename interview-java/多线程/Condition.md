# Condition

Condition的作用是对锁进行更精确的控制。

Condition中await()方法类似于Object中的wait()方法，而Condition中的signal()方法类似于Object中的notify()方法，Condition中的signalAll()方法类似于Object中的notifyAll()方法。

Object中的方法需要与同步锁绑定使用，Condition中的方法需要与互斥锁或共享锁绑定使用。

## Condition的区别

对于同一个锁，可以创建多个Condition，在不同的情况下使用不同的Condition。

针对不同的情况，创建多个不同的Condition，不同的Condition可以保证指定对应的线程阻塞或唤醒对应的线程。