### bboss-persistent结合bboss-aop实现注解事务

bboss-persistent结合bboss-aop也可以实现注解事务哦.

先看一个业务组件：

Java代码

```java
package org.frameworkset.spi.transaction.annotation;  
  
import java.sql.SQLException;  
  
import com.frameworkset.common.poolman.DBUtil;  
import com.frameworkset.orm.annotation.RollbackExceptions;  
import com.frameworkset.orm.annotation.Transaction;  
import com.frameworkset.orm.annotation.TransactionType;  
import com.frameworkset.orm.transaction.TransactionManager;  
  
/** 
 * <p>Title: TXA.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2010-1-19 下午05:17:43 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TXA   
{  
      
    @Transaction(TransactionType.REQUIRED_TRANSACTION)  
    @RollbackExceptions({"java.sql.SQLException","org.frameworkset.spi.transaction.Exception1"})  
    public void executeTXDBFailed() throws SQLException  
    {  
        DBUtil db = new DBUtil();  
        try  
        {  
            System.out.println("executeTXDBFailed:"+TransactionManager.getTransaction());  
            db.executeInsert("insert into char_table(id) values(2) ");  
            db.executeDelete("delete ar_table wher id=4");  
        }  
        catch (SQLException e)  
        {  
            throw e;  
        }  
    }  
      
    @Transaction      
    public void executeDefualtTXDB() throws SQLException  
    {  
        DBUtil db = new DBUtil();  
        try  
        {  
            db.executeInsert("insert into char_table(id) values(2) ");  
            db.executeDelete("delete from char_table where id=4");  
            System.out.println("executeTXDBFailed:"+TransactionManager.getTransaction());  
        }  
        catch (SQLException e)  
        {  
            throw e;  
        }  
    }  
      
    @Transaction(TransactionType.REQUIRED_TRANSACTION)  
    @RollbackExceptions({"java.sql.SQLException","org.frameworkset.spi.transaction.Exception1"})  
    public void executeTxDB() throws SQLException  
    {  
        DBUtil db = new DBUtil();  
        try  
        {  
            db.executeSelect("select * from char_table where id=2");  
            System.out.println("db.size()="+ db.size());  
            db.executeInsert("insert into char_table(id) values(3) ");  
            db.executeInsert("insert into char_table(id) values(4) ");  
            db.executeDelete("delete from char_table where id=3");  
            System.out.println("executeTxDB:"+TransactionManager.getTransaction());  
        }  
        catch (SQLException e)  
        {  
            throw e;  
        }  
    }  
}  
```

再看一下aop配置文件org/frameworkset/spi/transaction/annotation /annotationtx.xml的内容:

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<properties>  
    <property name="annotation.test" singlable="true" class="org.frameworkset.spi.transaction.annotation.TXA"/>  
</properties>  
```

再看看测试用例：

Java代码

```java
package org.frameworkset.spi.transaction.annotation;  
  
import java.sql.SQLException;  
  
import org.frameworkset.spi.ApplicationContext;  
import org.junit.Test;  
  
/** 
 * <p>Title: TestAnnotationTx.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2010-1-19 下午05:12:03 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestAnnotationTx  
{  
    ApplicationContext context = ApplicationContext.getApplicationContext("org/frameworkset/spi/transaction/annotation/annotationtx.xml");  
    @Test  
    public void testTxfailed()  
    {  
        TXA ai = (TXA)context.getBeanObject("annotation.test");  
        try  
        {  
            ai.executeTXDBFailed();  
        }  
        catch (SQLException e)  
        {     
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void testTx()  
    {  
          
        TXA ai = (TXA)context.getBeanObject("annotation.test");  
        try  
        {  
            ai.executeTxDB();  
        }  
        catch (SQLException e)  
        {  
            e.printStackTrace();  
        }  
    }  
      
    @Test  
    public void testDefualtTx()  
    {  
        TXA ai = (TXA)context.getBeanObject("annotation.test");  
        try  
        {  
            ai.executeDefualtTXDB();  
        }  
        catch (SQLException e)  
        {  
            e.printStackTrace();  
        }  
    }      
}  
```

可到sourceforge下载测试用例，测试用例包含在最新版本bbossgroups-3.4，下载地址：

http://sourceforge.net/projects/bboss/files/