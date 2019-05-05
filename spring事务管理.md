# JDBC事务和JTA事务

java事务类型：

1. JDBC事务
2. JTA事务
3. 容器事务，如Spring事务

容器事务主要是J2EE应用服务器提供的，容器事务大多是基于JTA完成，这是一个基于JNDI的，相当复杂的API实现。

## JDBC事务

JDBC的一切行为包括事务是基于一个Connection的，在JDBC中是通过Connection对象进行事务管理。在JDBC中，常用的和事务相关的方法是：
1. setAutoCommit
2. commit
3. rollback

优点：JDBC为使用Java进行数据库的事务操作提供了最基本的支持。通过JDBC事务，我们可以将多个SQL语句放到同一个事务中，保证其ACID特性。JDBC事务的主要优点就是API比较简单，可以实现最基本的事务操作，性能也相对较好。

缺点：一个JDBC事务不能跨越多个数据库。

## JTA事务

JTA事务比JDBC事务更强大。一个JTA事务可以有多个参与者，而一个JDBC事务则被限定在一个单一的数据库连接。

下列任一个Java平台的组件都可以参与到一个JTA事务中：
- JDBC连接
- JDO PersistenceManager对象
- JMS队列
- JMS主题
- 企业JavaBeans（EJB）
- 一个用J2EE Connector Architecture规范编译的资源分配器。

java.transaction.UserTransaction：

- begin：开启一个事务
- commit：提交当前事务
- rollback：回滚当前事务
- setRollbackOnly：把当前事务标记为回滚
- setTransactionTimeout：设置事务的事件，超过这个事件，就抛出异常，回滚事务

只有符合XA规范，并且实现了XA规范的相关接口的类才能参与到JTA事务中来，目前主流的数据库都支持XA规范。

## 分布式事务

一个分布式事务包括一个事务管理器和一个或多个资源管理器。

资源管理器是任意类型的持久化数据存储。

事务管理器承担着所有事务参与单元者的相互通讯的责任。

# spring事务管理

Spring事务包含对分布式事务和单机事务的支持，我们用的比较多的是单机事务，也就是只操作一个数据库的事务。

单机事务，按照用法分，又可以分为编程式事务模型（TransactionTemplate）和声明式事务模型（@Transactional注解），后者可以理解为aop + 编程式事务模型。

编程式事务模型里面涉及到很多知识点，比如统一事务模型、事务传播级别、事务隔离级别等。

## PlatformTransactionManager事务管理器

Spring并不直接管理事务，通过这个接口，Spring为各个平台如JDBC、Hibernate等都提供了对应的事务管理器，也就是将事务管理的职责委托给Hibernate或者JTA等持久化机制所提供的相关平台框架的事务来实现。

- TransactionStatus getTransaction(TransactionDefinition definition) ：事务管理器通过TransactionDefinition，获得“事务状态”，从而管理事务；
- void commit(TransactionStatus status)：根据状态提交；
- void rollback(TransactionStatus status)：根据状态回滚。

Spring事务管理为不同的事务API提供一致的编程模型，具体的事务管理机制由对应各个平台去实现。

## TransactionStatus事务状态

## TransactionDefinition基本事务属性的定义

事务管理器接口PlatformTransactionManager通过getTransaction(TransactionDefinition definition)方法来得到事务，这个方法里面的参数是TransactionDefinition类，这个类就定义了一些基本的事务属性。

事务属性可以理解成事务的一些基本配置，描述了事务策略如何应用到方法上。事务属性包含了5个方面：
1. 传播行为
2. 隔离规则
3. 回滚规则
4. 事务超时
5. 是否只读

### 传播行为

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

1. PROPAGATION_REQUIRED：默认值，A如果有事务，B将使用该事务；如果A没有事务，B将创建一个新的事务。
2. PROPAGATION_SUPPORTS：A如果有事务，B将使用该事务；如果A没有事务，B将以非事务执行。
3. PROPAGATION_MANDATORY：A如果有事务，B将使用该事务；如果A没有事务，B将抛异常。
4. PROPAGATION_REQUIRES_NEW：如果A有事务，将A的事务挂起，B创建一个新的事务；如果A没有事务，B创建一个新的事务。
5. PROPAGATION_NOT_SUPPORTED：如果A有事务，将A的事务挂起，B将以非事务执行；如果A没有事务，B将以非事务执行。
6. PROPAGATION_NEVER：如果A有事务，B将抛异常；如果A没有事务，B将以非事务执行。
7. PROPAGATION_NESTED：A和B底层采用保存点机制，形成嵌套事务。

### 隔离级别

1. 脏读（Dirty reads）——脏读发生在一个事务读取了另一个事务改写但尚未提交的数据时。如果改写在稍后被回滚了，那么第一个事务获取的数据就是无效的。

2. 不可重复读（Nonrepeatable read）——不可重复读发生在一个事务执行相同的查询两次或两次以上，但是每次都得到不同的数据时。这通常是因为另一个并发事务在两次查询期间进行了更新。

3. 幻读（Phantom read）——幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录。

注意：不可重复读重点是修改，而幻读重点是新增或删除。

在 Spring 事务管理中，为我们定义了如下的隔离级别：

1. ISOLATION_DEFAULT：使用后端数据库默认的隔离级别
2. ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
3. ISOLATION_READ_COMMITTED：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
4. ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生
5. ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别，确保阻止脏读、不可重复读以及幻读，也是最慢的事务隔离级别，因为它通常是通过完全锁定事务相关的数据库表来实现的。

## Spring编程式事务和声明式事务的区别

1. 编程式事务处理：所谓编程式事务指的是通过编码方式实现事务，允许用户在代码中精确定义事务的边界。即类似于JDBC编程实现事务管理。管理使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。

2. 声明式事务处理：管理建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或通过基于@Transactional注解的方式)，便可以将事务规则应用到业务逻辑中。

简单地说，编程式事务侵入到了业务代码里面，但是提供了更加详细的事务管理；而声明式事务由于基于AOP，所以既能起到事务管理的作用，又可以不影响业务代码的具体实现。