#Spring-Transaction
事务：指访问并可能更新数据库中各种数据项的一个程序执行单元(unit)。

### 事务的四个特性
1. 原子性（Atomicity）：事务是一个原子操作，由一系列动作组成。事务的原子性确保动作要么全部完成，要么完全不起作用。
2. 一致性（Consistency）：一旦事务完成（不管成功还是失败），系统必须确保它所建模的业务处于一致的状态，而不会是部分完成部分失败。在现实中的数据不应该被破坏。
3. 隔离性（Isolation）：可能有许多事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏。
4. 持久性（Durability）：一旦事务完成，无论发生什么系统错误，它的结果都不应该受到影响，这样就能从任何系统崩溃中恢复过来。通常情况下，事务的结果被写到持久化存储器中。

**事务如果不考虑隔离性，会引发脏读、不可重复读、幻读等问题**
1. 脏读：一个事务读取到另一个事务还没有提交的数据，如果这些数据被回滚，则读取到的数据是无效的。
2. 不可重复读：在同一个事务中，多次读取同一数据，读到了另一个事务已经更新的数据，导致返回的结果有所不同。
3. 虚读，幻读：一个事务读取几行记录后，另一个事务插入一些记录，幻读就发生了，在后来的查询中，第一个事务就会发现原来没有的记录。

**不可重复读重点是修改，而幻读重点是新增或删除。**

### 事务的隔离级别
定义了一个事务可能受其他并发事务影响的程度。
1. ISOLATION_DEFAULT：使用后端数据库默认的隔离级别
2. ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
3. ISOLATION_READ_COMMITTED：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
4. ISOLATION_REPEATABLE_READ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生
5. ISOLATION_SERIALIZABLE：最高的隔离级别，完全服从ACID的隔离级别，确保阻止脏读、不可重复读以及幻读，也是最慢的事务隔离级别，因为它通常是通过完全锁定事务相关的数据库表来实现的
　　上面定义的隔离级别，在 Spring 的 TransactionDefinition.class 中也分别用常量 -1,0,1,2,4,8表示。
    **MySql默认的事务隔离级别为REPEATABLE_READ**

### 事务的传播行为
当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。
1. PROPAGATION_REQUIRED ：required , 必须。默认值，A如果有事务，B将使用该事务；如果A没有事务，B将创建一个新的事务。
2. PROPAGATION_SUPPORTS：supports ，支持。A如果有事务，B将使用该事务；如果A没有事务，B将以非事务执行。
3. PROPAGATION_MANDATORY：mandatory ，强制。A如果有事务，B将使用该事务；如果A没有事务，B将抛异常。
4. PROPAGATION_REQUIRES_NEW ：requires_new，必须新的。如果A有事务，将A的事务挂起，B创建一个新的事务；如果A没有事务，B创建一个新的事务。
5. PROPAGATION_NOT_SUPPORTED ：not_supported ,不支持。如果A有事务，将A的事务挂起，B将以非事务执行；如果A没有事务，B将以非事务执行。
6. PROPAGATION_NEVER ：never，从不。如果A有事务，B将抛异常；如果A没有事务，B将以非事务执行。
7. PROPAGATION_NESTED ：nested ，嵌套。A和B底层采用保存点机制，形成嵌套事务。

平台事务管理器(PlatformTransactionManager)会根据TransactionDefinition中定义的事务信息(包括隔离级别、传播行为)来进行事务的管理,在管理的过程中事务可能产生了保存点或事务是新的事务等情况,那么这些信息都会记录在TransactionStatus的对象中.

### 事务管理高层抽象主要包括三个接口
1. PlatformTransactionManager：事务管理器
2. TransactionDefinition：事务定义信息(事务隔离级别、传播行为，超时，只读)，TransactionDefinition的实现类保存了对事务管理的配置信息，该类的实例被Manager读取用来创建执行事务管理的对象；
3. TransactionStatus：事务具体运行状态

**PlatformTransactionManager：事务管理器**
1. org.springframework.jdbc.datasource.DataSourceTransactionManager ——> 在使用Spring JDBC或iBatis进行持久化数据时使用
2. org.springframework.orm.hibernate3.HibernateTransactionManager ——> 在使用Hibernate3.0版本进行持久化数据时使用
3. org.springframework.orm.jpa.JpaTransactionManager ——> 在使用JPA进行持久化时使用
4. org.springframework.jdo.JdoTransactionManager ——> 当持久化机制是jdo时使用
5. org.springframework.transaction.jta.JtaTransactionManager ——> 使用一个JTA实现来管理事务，在一个事务跨越多个资源时必须使用

![PlatformTransactionManager](https://pic4.zhimg.com/80/v2-f38bb22c0b74d5fb3493fabfeb7e342b_hd.jpg "PlatformTransactionManager")
* TransactionStatus getTransaction(TransactionDefinition definition) ，事务管理器 通过TransactionDefinition，获得“事务状态”，从而管理事务。
* void commit(TransactionStatus status) 根据状态提交
* void rollback(TransactionStatus status) 根据状态回滚

**TransactionStatus　事务状态**
![TransactionStatus　事务状态](https://pic4.zhimg.com/v2-cd50fb8aed2e35530a65f19a35aafebb_r.jpg "TransactionStatus　事务状态")
这个接口描述的是一些处理事务提供简单的控制事务执行和查询事务状态的方法，在回滚或提交的时候需要应用对应的事务状态。

**TransactionDefinition　基本事务属性的定义**（definition:定义，规定,【物】清晰度，解释）
事务管理器接口PlatformTransactionManager通过getTransaction(TransactionDefinition definition)方法来得到事务，这个方法里面的参数是TransactionDefinition类，这个类就定义了一些基本的事务属性。
事务属性包含了5个方面:
![事务属性](https://pic2.zhimg.com/80/v2-cd231c2885ac72a3348e9e2c7fe00749_hd.jpg "事务属性")

### 只读
这是事务的第三个特性，是否为只读事务。如果事务只对后端的数据库进行该操作，数据库可以利用事务的只读特性来进行一些特定的优化。通过将事务设置为只读，你就可以给数据库一个机会，让它应用它认为合适的优化措施。

### 事务超时
为了使应用程序很好地运行，事务不能运行太长的时间。因为事务可能涉及对后端数据库的锁定，所以长时间的事务会不必要的占用数据库资源。事务超时就是事务的一个定时器，在特定时间内事务如果没有执行完毕，那么就会自动回滚，而不是一直等待其结束。

###回滚规则
事务五边形的最后一个方面是一组规则，这些规则定义了哪些异常会导致事务回滚而哪些不会。默认情况下，事务只有遇到运行期异常时才会回滚，而在遇到检查型异常时不会回滚（这一行为与EJB的回滚行为是一致的） 。但是你可以声明事务在遇到特定的检查型异常时像遇到运行期异常那样回滚。同样，你还可以声明事务遇到特定的异常不回滚，即使这些异常是运行期异常。

### Spring 编程式事务和声明式事务的区别
**编程式事务处理**
所谓编程式事务指的是通过编码方式实现事务，允许用户在代码中精确定义事务的边界。即类似于JDBC编程实现事务管理。管理使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。
**声明式事务处理**
管理建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或通过基于@Transactional注解的方式)，便可以将事务规则应用到业务逻辑中。
**简单地说，编程式事务侵入到了业务代码里面，但是提供了更加详细的事务管理；而声明式事务由于基于AOP，所以既能起到事务管理的作用，又可以不影响业务代码的具体实现。**
代码：
https://zhuanlan.zhihu.com/p/37108469

### @Transactional
![@Transactional属性](/Transactional.png "@Transactional属性")
@Transactional 可以作用于接口、接口方法、类以及类方法上。当作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。
虽然 @Transactional 注解可以作用于接口、接口方法、类以及类方法上，但是 Spring 建议不要在接口或者接口方法上使用该注解，因为这只有在使用基于接口的代理时它才会生效。另外， @Transactional 注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。如果你在 protected、private 或者默认可见性的方法上使用 @Transactional 注解，这将被忽略，也不会抛出任何异常。
默认情况下，只有来自外部的方法调用才会被AOP代理捕获，也就是，类内部方法调用本类内部的其他方法并不会引起事务行为，即使被调用方法使用@Transactional注解进行修饰。
