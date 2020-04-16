### bboss persistent事务管理介绍 （三）

##### 2.事务挂起处理-【开始】>【挂起】>【恢复】>【提交】>【回滚】

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

​          

​           dbUtil.executeInsert("insert into test1(name) values('biaoping.yin1')");

​          

​           //提交事务

​           tm.commit();

​           log("delete commit",tm.getStatus());

​       } catch (TransactionException e) {

​           log("delete rollback before",tm.getStatus());

​          

​           try {

​              //回滚事务

​              tm.rollback();

​           } catch (RollbackException e1) {

​              // TODO Auto-generated catch block

​              e1.printStackTrace();

​           }

​          

​           log("delete rollback after",tm.getStatus());

​           e.printStackTrace();

​       } catch (Exception e) {

​           // TODO Auto-generated catch block

​           e.printStackTrace();

​           log("delete rollback1 before",tm.getStatus());

​          

​           try {

​              tm.rollback();

​           } catch (RollbackException e1) {

​              // TODO Auto-generated catch block

​              e1.printStackTrace();

​           }

​          

​           log("delete rollback1 after",tm.getStatus());

​       }

##### 3.指定开启的事务类别

TransactionManager tm = new TransactionManager();

​      

​           //开始事务,在begin和commit之间的各种数据库操作都包含在事务中,除非中间有事务的挂起和中断,但是中断恢复后事务将继续,

​           //或者另外在事务执行过程如果开启了新的事务,则当前事务将被中断

tm.begin(TransactionManager.REQUIRED_TRANSACTION); 

tm.begin(TransactionManager.NEW_TRANSACTION); 

tm.begin(TransactionManager. MAYBE_TRANSACTION); 

tm.begin(TransactionManager. NO_TRANSACTION);

##### 4.使用事务处理模板类对事务处理进行封装

相关的接口如下： 

​     com.frameworkset.common.poolman.JDBCTemplate  不带返回值模板接口

​     com.frameworkset.common.poolman.JDBCValueTemplate  带返回值的模板接

​     com.frameworkset.common.poolman.TemplateDBUtil  提供执行上述两种模板接口的两个静态工具方法，对底层的事务进行了有效的封装。

- ​       **不带返回值的模板方法使用实例**

 

TemplateDBUtil.*executeTemplate*(

​              **new** JDBCTemplate(){

​                  /**

​                   \* 整个execute()方法的执行都会被包含在一个数据库事务中

​                   \* 当有异常抛出时TemplateDBUtil.executeTemplate()方法就

\* 会自动回滚整个数据库事务，

​                   \* 当整个方法正常结束后，事务就会自动被提交。

​                   \* 通过模板工具提供的便利，开发人员不需要编写自己的事务代码就

\* 可以顺利地实现数据库的事务性操作

​                   */

​                  **public** **void** execute() **throws** Exception {

​                     DBUtil dbUtil = **new** DBUtil();                                         PreparedDBUtil db = **null**;

​                     **try** {

​                         **for** (**int** i = 0; i < 10; i++) {

​                            db = **new** PreparedDBUtil();

​                                db.preparedInsert(

​                                              "insert into td_reg_bank_acc_bak (create_acc_time,starttime,endtime) values(?,?,?)");

​                                Date today = **new** Date(**new** java.util.Date().getTime());

​                                db.setDate(1, **new** java.util.Date());

​                                db.setDate(2, **new** java.util.Date());

​                                db.setDate(3, **new** java.util.Date());

​                                db.executePrepared();

​                         }

​                     }

​                     **catch**(Exception e)