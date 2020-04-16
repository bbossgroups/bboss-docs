### bboss persistent事务管理介绍 （二）

##### 4.9.5  事务管理的相关接口

事务管理器：com.frameworkset.orm.transaction.TransactionManager ，每个接口表：

| **名称**                                                     | **功能描述**                                                 | **说明**                                                     |
| :----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **public void begin() throws TransactionException**          | 开启一个事务，如果当前上下文有事务则加入，如果没有则创建新的事务 | Begin方法只能在TransactionManager的同一个对象实例上调用一次，超过一次将抛出事务异常 |
| **public**     **void**   **begin(int tx_type) throws TransactionException** | 开启一个事务，参数tx_type值的取值范围为：NEW_TRANSACTION-开启新事务，隔离上下文事务；REQUIRED_TRANSACTION-有则加入事务，没有就开启新事物；MAYBE_TRANSACTION-有则加入事务，没有就不开启事务；NO_TRANSACTION-不开启事务也不加入上下文事务 |                                                              |
| **public void commit() throws RollbackException**            | 提交事务，如果是嵌套子事务，则事务计数器递减直至为0时提交全部嵌套事务，只有所有子事务都提交成功后才算提交成功 |                                                              |
| **public void rollback() throws RollbackException**          | 回滚事务，如果事务执行过程当中出现异常，则回滚事务           |                                                              |
| **public**   **JDBCTransaction suspend()**  **throws SystemException** | 挂起事务，挂起当前正在执行的事务是指暂停当前线程中运行的事务。如果在执行当前事务之前有事务挂起则恢复之前挂起的事务 |                                                              |
| **public JDBCTransaction suspendAll() throws SystemException** | 挂起所有的事务，挂起所有的事是指将当前事务挂起同时不恢复之前事务，即在执行恢复之前的当前环境没有事务关联。 |                                                              |
| **public**  **void**  **resume(JDBCTransaction tx)**  **throws**  **InvalidTransactionException,**    **IllegalStateException,**  **SystemException** | 恢复之前挂起的事务                                           |                                                              |

##### 4.9.7应用程序使用事务框架进行编程的两种方式

应用程序使用事务框架进行编程的具有以下两种方式

- ​       **可编程的事务管理模式**

可编程的事务管理模式就是说可以直接在业务程序中直接调用事务管理的接口来进行事务处理，在这种模式下同样可以采用两种方式进行编程，一种是程序员直接在代码中创建事务对象，开启事务，提交或者回滚事务；一种是通过系统提供的事务管理模板接口来编写事务控制程序。前者容易造成事务泄漏（没有被正常的提交或者回滚），但是使用起来比较直观，后者将事务管理的功能全部封装在执行模板方法的功能类中，开发人员只需在模板方法中编写包含数据库操作的业务功能即可，不会出现事务泄漏的情况。

- ​       **声明式的事务管理模式**

声明式的事务管理模式就是指将业务组件的事务控制策略全部通过配置文件的来声明实现，业务组件本身无需对事物进行管理，具体进行配置的规则简单描述如下：

第一步 将业务组件配置到配置文件中

第二步 需要控制事务的方法名添加到配置文件中

第三步 定义事务控制策略，即在什么情况下事务执行成功，什么情况下事务执行失败，也就说需要定义失败的策略，失败策略一般通过定义一些特定的异常来声明，也可以不定义异常，默认所有的异常都会导致事务回滚。

声明性事务的实现是基于系统中提供的轻量级aop框架来实现的，通过该框架可以对业务组件中所有需要进行事务控制的方法进行实时动态拦截嵌入事务的控制功能，具体的细节请参照《aop框架的使用文档》。

##### 4.9.8可编程事务使用实例

###### 1.简单的事务处理-【开始】>【提交】>【回滚】

TransactionManager tm = new TransactionManager();   

​           try {

​              tm.begin();

​              log("delete befer" ,tm.getStatus());

​             

​              DBUtil dbUtil = new DBUtil();

​              dbUtil.executeDelete("delete from test");

​              dbUtil.executeDelete("delete from test1");

​              log("delete after" ,tm.getStatus());

​              tm.commit();

​              log("delete commit after" ,tm.getStatus());

​             

​           } catch (TransactionException e) {

​              try {

​                  tm.rollback();

​              } catch (RollbackException e1) {

​                  // TODO Auto-generated catch block

​                  e1.printStackTrace();

​              }

​              e.printStackTrace();

​           } catch (Exception e) {

​              // TODO Auto-generated catch block

​              e.printStackTrace();

​              try {

​                  tm.rollback();

​              } catch (RollbackException e1) {

​                  // TODO Auto-generated catch block

​                  e1.printStackTrace();

​              }

​           }

###### 2.事务挂起处理-【开始】>【挂起】>【恢复】>【提交】>【回滚】

​              TransactionManager tm = new TransactionManager();

​      

​       try {

​           //开始事务,在begin和commit之间的各种数据库操作都包含在事务中,除非中间有事务的挂起和中断,但是中断恢复后事务将继续,

​           //或者另外在事务执行过程如果开启了新的事务,则当前事务将被中断

​           tm.begin();

​           log("delete before",tm.getStatus());

​          

​          

​           DBUtil dbUtil = new DBUtil();

​           //执行两个删除操作,如果一个失败整个事务就回滚

​           dbUtil.executeDelete("delete from test");

​           dbUtil.executeDelete("delete from test1");

​           //挂起事务,挂起后的数据库操作将不受事务控制直到事务被恢复

​           JDBCTransaction tx = tm.suspend();

​           try

​           {

​              dbUtil.executeInsert("insert into test1(name) values('biaoping.yin')");

​           }

​           catch(Exception e)

​           {

​             

​           }

​           //恢复事务,之后的事务将加入之前被中断的事务当中

​           tm.resume(tx);

​           log("delete after",tm.getStatus());