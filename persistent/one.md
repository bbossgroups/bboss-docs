### bboss persistent事务管理介绍 （一）

bboss项目下载列表 在sourceforge访问地址为：

https://sourceforge.net/project/showfiles.php?group_id=238653

bboss persistent的事务管理框架实现数据库的增、删、改、查事务管理，整个事务管理框架在下面的各节中详细介绍。

应用程序中的事务TX都会和本地线程关联，本地线程ThreadLocal是一个对上下文Thread关联的线程独占资源进行有效管理的容器。

##### 4.9.1.多数据库事务的支持（分布式事务）

一个事务中如果存在不同数据库的操作，那么事务的处理比较复杂，只有所有的数据库的操作都成功以后事务提交，否则事务回滚。下面的代码演示了如果管理两个不同数据库（分布式）事务：

Poolman.xml中配置了两个数据源bspf和query：

  < datasource >

​      ...

< dbname >bspf</ dbname >

...

</ datasource > 

< datasource >

​      ...

< dbname >query</ dbname >

...

</ datasource >

代码段如下：   

​           TransactionManager tm =  **new** TransactionManager();

​       **try**

​       {

​           tm.begin();

​           DBUtil db = **new** DBUtil();

​             //在bspf库上执行删除操作

​           db.executeDelete("bspf","delete from table1 where id=1");

​             //在query库上执行更新操作

​           db.executeUpdate("query","update table1 set value='test'

where id=1");

​           tm.commit();

​       }

​       **catch**(Exception e)

​       {

​           **try** {

​              tm.rollback();

​           } **catch** (RollbackException e1) {

​             

​              e1.printStackTrace();

​           }

   }

上述代码中对bspf库上执行删除操作，在query库上执行更新操作，两个操作被包含在一个事务中，只有两个操作全部成功后事务才会成功，任何一个失败，都会导致事务被回滚。

##### 4.9.2.四种类型的事务

a.    必须创建新的事务（NEW_TRANSACTION）

b.    有事务就加入当前事务，没有就不创建事务（MAYBE_TRANSACTION）

c.    有事务就加入当前事务，没有就创建事务（REQUIRED_TRANSACTION）（默认情况）

d.    没有事务（NO_TRANSACTION） 

##### 4.9.3.事务嵌套

对于同一个的事务中包含的事务定义为事务嵌套，事务所包含的子事务通过子事务计数器来管理，增加一个子事务时计数器加1，提交一个子事务时计数器减一，当计数器为零时提交所有事务，只要事务执行过程中有rollback的情况则回滚全部子事务。事务的嵌套具有一下16种组合，每种组合对全局事务的影响见表：

|                          | **内部事务类型**                                         |                                                            |                                                        |                                                              |
| ------------------------ | -------------------------------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| **外部事****务类型**     | **NEW_TRANSACTION**                                      | **REQUIRED_TRANSACTION**                                   | **MAYBE_TRANSACTION**                                  | **NO_TRANSACTION**                                           |
| **NEW_TRANSACTION**      | 屏蔽声明的事务，程序中开启一个新事务                     | 使用声明的事务                                             | 使用声明的事务                                         | 屏蔽声明的事务,程序在没有事务的环境下运行                    |
| **REQUIRED_TRANSACTION** | 屏蔽声明的事务，程序中开启一个新事务                     | 使用声明的事务                                             | 使用声明的事务                                         | 屏蔽声明的事务,程序在没有事务的环境下运行                    |
| **MAYBE_TRANSACTION**    | 如果声明的事务存在，屏蔽声明的事务，程序中开启一个新事务 | 如果声明的事务存在，则使用声明的事务，否则开启一个新的事务 | 如果声明的事务存在，则使用声明的事务，否则不需声明事务 | 如果声明的事务存在，屏蔽声明的事务,程序在没有事务的环境下运行 |
| **NO_TRANSACTION**       | 程序中开启一个新事务                                     | 程序中开启一个新事务                                       | 程序在没有事务的环境下运行                             | 程序在没有事务的环境下运行                                   |

##### 4.9.4.支持事务的挂起和恢复

事务的挂起包括挂起当前正在执行的事务和挂起所有的事务.

挂起当前正在执行的事务是指暂停当前线程中运行的事务。如果在执行当前事务之前有事务挂起则恢复之前挂起的事务，挂起所有的事务是指将当前事务挂起同时不恢复之前事务，即在执行恢复之前的当前环境没有事务关联。 

事务恢复：将之前挂起的事务进行恢复，事务继续执行

##### 4.9.5 事务管理的相关接口

事务管理器：com.frameworkset.orm.transaction.TransactionManager ，每个接口表：

| **名称**                                                     | **功能描述**                                                 | **说明**                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **public void begin() throws TransactionException**          | 开启一个事务，如果当前上下文有事务则加入，如果没有则创建新的事务 | Begin方法只能在TransactionManager的同一个对象实例上调用一次，超过一次将抛出事务异常 |
| **public** **void** **begin(int tx_type) throws TransactionException** | 开启一个事务，参数tx_type值的取值范围为：NEW_TRANSACTION-<span style="color: navy; fo |                                                              |




  


   