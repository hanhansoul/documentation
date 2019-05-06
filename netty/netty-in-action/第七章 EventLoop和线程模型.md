# EventLoop和线程模型

## 线程模型概述

## EventLoop接口

运行任务来处理在连接的生命周期内发生的事件是任何网络框架的基本功能。与之相应的编程上的构造通常被称为事件循环—一个Netty使用了interface io.netty.channel.EventLoop来适配的术语。

Netty的EventLoop是协同设计的一部分，它采用了两个基本的API：并发和网络编程。

在这个模型中，一个EventLoop将由一个永远都不会改变的Thread驱动，同时任务（Runnable或者Callable）可以直接提交给EventLoop实现，以立即执行或者调度执行。

## 任务调度

