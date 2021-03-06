# ThreadGroup

在java中为了方便线程管理出现了线程组ThreadGroup的概念，每个ThreadGroup可以同时包含多个子线程和多个子线程组，在一个进程中线程组是以树形的方式存在，通常情况下根线程组是system。system线程组下是main线程组，默认情况下第一级应用自己的线程组是通过main线程组创建出来的。

ThreadGroup之间的关系是树的关系，而Thread与ThreadGroup的关系就像元素和集合的关系。

## 线程组与线程池

线程组
- 线程组存在的意义，首要原因是安全。
- java默认创建的线程都是属于系统线程组，而同一个线程组的线程是可以相互修改对方的数据的。
- 但如果在不同的线程组中，那么就不能“跨线程组”修改数据，可以从一定程度上保证数据安全.

线程池：
- 线程池存在的意义，首要作用是效率。
- 线程的创建和结束都需要耗费一定的系统时间（特别是创建），不停创建和删除线程会浪费大量的时间。所以，在创建出一条线程并使其在执行完任务后不结束，而是使其进入休眠状态，在需要用时再唤醒，那么 就可以节省一定的时间。
- 如果这样的线程比较多，那么就可以使用线程池来进行管理。保证效率。

线程组和线程池共有的特点：
- 都是管理一定数量的线程
- 都可以对线程进行控制---包括休眠，唤醒，结束，创建，中断（暂停）--但并不一定包含全部这些操作。

