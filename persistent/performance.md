### bbosspersistent 性能初探

针对bbosspersistent，在oracle 10上做了一个简单的性能测试：

环境配置：

连接池最大连接数设置为10

连接池初始连接数位2

测试场景：

300个线程并发往一张3字段的表中插入数据，每个线程执行3条记录插入操作

测试结果：

插入完毕后，统计结果如下

不使用事务 ：357条/秒

使用RW_TRANSACTION事务:820条/秒

测试程序：

Java代码

```java
package com.frameworkset.common.poolman;  
  
import javax.transaction.RollbackException;  
  
import com.frameworkset.orm.transaction.TransactionManager;  
  
/** 
 *  
 * <p>Title: CurrentTest.java</p> 
 * 
 * <p>Description: </p> 
 * 
 * <p>Copyright: Copyright (c) 2007</p> 
 * @Date 2010-8-2 下午12:02:44 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class CurrentTest {  
    public static void main(String[] args) throws InterruptedException {  
  
//      for(int j = 0; j < 300; j ++)  
//      {  
//          CurrentTest i = new CurrentTest();  
//          i.test(false);  
//      }  
          
        for(int j = 0; j < 300; j ++)  
        {  
            CurrentTest i = new CurrentTest();  
            i.test(true);  
        }  
  
    }  
      
    public void test(boolean usetx)  
    {  
        if(!usetx)  
        {  
            Task t = new Task();  
            t.start();  
        }  
        else  
        {  
            TXTask t = new TXTask();  
            t.start();  
        }  
              
    }  
    static class Task extends Thread  
    {  
  
        /* (non-Javadoc) 
         * @see java.lang.Thread#run() 
         */  
        @Override  
        public void run() {  
            DBUtil db = new DBUtil();  
          
            try {  
  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
  
            } catch (Throwable e) {  
  
                e.printStackTrace();  
            }  
              
        }  
          
    }  
      
    static class TXTask extends Thread  
    {  
  
        /* (non-Javadoc) 
         * @see java.lang.Thread#run() 
         */  
        @Override  
        public void run() {  
            DBUtil db = new DBUtil();  
            TransactionManager tm = new TransactionManager();  
            try {  
                tm.begin(tm.RW_TRANSACTION);  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                  
                db.executeInsert("insert into TEST_CURRENT(id,name) values(10,'test')");  
                tm.commit();  
            } catch (Throwable e) {  
                try {  
                    tm.rollback();  
                } catch (RollbackException e1) {  
                    // TODO Auto-generated catch block  
                    e1.printStackTrace();  
                }  
                e.printStackTrace();  
            }  
              
        }  
          
    }  
}  
```

测试结果说明：

为什么启用RW_TRANSACTION事务后性能要好于没有启用事务的情况呢，这是由于数据库连接池的特性决定，连接池中的连接资源是一种共享资源，要想获取一个db connection，首先要获得pool中的共享锁，获取完毕后再释放锁。在没有事务上下文的情况下，每个db操作都是一个独立的事务，都会从db pool中获取连接，因此获取锁的次数比较频繁，这样就会导致性能下降；相反一个RW_TRANSACTION上下文中只会获取一个db connection，只有一次获取锁的情况，性能要好很多。

影响持久层操作性能因素分析：

1.频繁地申请连接池中连接会导致性能降低

2.频繁地释放数据库连接到连接池中也会导致性能降低

3.开启连接池中连接校验特性会导致新能降低

连接池连接在以下情况下会执行校验逻辑：

a.申请连接（默认开启），可以通过以下方式进行控制

<**testOnBorrow**>false<**/testOnBorrow**>

b.释放连接（默认关闭）,可以通过以下方式进行控制(目前bboss没有开放改配置开关，即使配置也无效)

<**testOnReturn**>false<**/testOnReturn**>

c.空闲连接回收扫描操作（默认关闭）,可以通过以下方式进行控制

<**testWhileIdle**>true<**/testWhileIdle**>

同时我们也可以通过指定validationQuery属性来控制执行校验操作时是否执行一条指定的sql语句，来校验连接的可用性，而且在执行的时候会占用共享锁，这个性能开销比较大。

我们可以不配置validationQuery属性来提升系统性能

Xml代码 

```xml
<!--  
    链接有效性检查sql语句 
    -->  
   <validationQuery>select 1 from dual</validationQuery>  
```

4.开启强制回收长时间占用连接机制，会对性能有一定的影响，屏蔽方法：

<**removeAbandoned**>false<**/removeAbandoned**>

removeAbandoned开关主要在开发环境下开启，用来检测是否有泄露的事务链接，并且在连接占用比较多的情况下，回收占用时间较长的链接，生产环境下必须将removeAbandoned设置为false。

上述4个因素也可以通过在编程过程采用数据库事务来优化性能，将密集的数据操作包含在事务中，bboss数据库事务开启方法：

Java代码

```java
TransactionManager tm = new TransactionManager();  
            try {  
                tm.begin(tm.RW_TRANSACTION);  
                              //db操作  
                               tm.commit();  
            } catch (Throwable e) {  
                try {  
                    tm.rollback();  
                } catch (RollbackException e1) {  
                    // TODO Auto-generated catch block  
                    e1.printStackTrace();  
                }  
                e.printStackTrace();  
            }  
```

或者

Java代码

```java
TransactionManager tm = new TransactionManager();  
            try {  
                tm.begin();  
                              //db操作  
                               tm.commit();  
            } catch (Throwable e) {  
                try {  
                    tm.rollback();  
                } catch (RollbackException e1) {  
                    // TODO Auto-generated catch block  
                    e1.printStackTrace();  
                }  
                e.printStackTrace();  
            }  
```

  详细的数据库事务请参考文档[bbossgroups培训.ppt](http://sourceforge.net/projects/bboss/files/bbossgroups-3.4/bbossgroups training.ppt/download) 中的事务管理部分

同时也可以参考另一篇文章《[bbossgroups持久层框架链接池配置优化策略之一 空闲链接回收配置](http://yin-bp.iteye.com/blog/1159337)》  

5.补充说明

如果你的应用部署在weblogic或者websphere下面，尽量使用应用服务器本身的数据源，bboss持久层中配置外部数据源的方法参考文档《[bboss persistent通过jndi引用外部数据源（datasource）方法](http://yin-bp.iteye.com/blog/352924)》

[common-dbcp并发测试.zip](http://dl.iteye.com/topics/download/a26db146-d2b2-30a1-be50-c1db09664100) (3.8 KB)

下载次数: 9