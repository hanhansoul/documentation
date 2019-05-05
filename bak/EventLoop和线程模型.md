# EventLoop和线程模型

## 线程模型概述

## EventLoop接口

运行任务来处理在连接的生命周期内发生的事件是任何网络框架的基本功能，通常被称为事件循环。netty使用EventLoop实现实现循环功能。

Netty的EventLoop是协同设计的一部分，它采用了两个基本的API：并发和网络编程。

一个EventLoop由一个不变的线程执行，同时任务（Runnable或者Callable）可以直接提交给EventLoop具体实现类，以立即执行或者调度执行。

Netty的EventLoop在继承了ScheduledExecutorService的同时，只定义了一个parent()方法，用于返回当前EventLoop实例所属的EventLoopGroup引用。

## 使用EventLoop调度任务

ScheduledExecutorService的实现具有局限性，例如，事实上作为线程池管理的一部分，将会有额外的线程创建。

### 线程管理

Netty线程模型的卓越性能取决于对于当前执行的Thread的身份的确定，也就是说，确定它是否是分配给当前Channel以及它的EventLoop的那一个线程（EventLoop将负责处理一个Channel的整个生命周期内的所有事件）。