### bboss多数据库事务管理

本文以一个简单的实例来介绍bbossgroups中持久层框架如何实现多数据库事务：

Java代码

```java
@Test  
    public void testMutiDBTX()  
    {  
        TransactionManager tm =  new TransactionManager();  
        try  
        {  
            tm.begin();  
            SQLExecutor.deleteWithDBName("bspf","delete from table1 where id=1");  
            SQLExecutor.updateWithDBName("query","update table1 set value='test' where id=1");  
            tm.commit();  
            DBUtil.debugStatus();//debug连接池实时状态  
        }  
        catch(Exception e)  
        {  
              
        }  
        finally  
        {  
            tm.release();  
        }  
  
    }  
      
}  
```

这个示例展示的在两个数据源（数据源名称为bspf和query）上分别执行一个delete操作和一个删除操作，通过TransactionManager事务管理组件来控制两个操作的事务，从而保证两个数据源事务的一致性，也就是说在bspf和query两个数据源上的操作都包含在一个事务中，这就是所谓的多数据库事务。

另外还可以支持嵌套的事务处理，例如：

Java代码

```java
@Test  
    public void testNestedMutiDBTX()  
    {  
        TransactionManager tm =  new TransactionManager();  
        try  
        {  
            tm.begin();  
            SQLExecutor.deleteWithDBName("bspf","delete from table1 where id=1");  
            SQLExecutor.updateWithDBName("query","update table1 set value='test' where id=1");  
            testMutiDBButSampleDatabaseTX();  
            tm.commit();  
            DBUtil.debugStatus();//debug连接池实时状态  
        }  
        catch(Exception e)  
        {  
              
        }  
        finally  
        {  
            tm.release();  
        }  
  
    }  
    @Test  
    public void testMutiDBButSampleDatabaseTX()  
    {  
        TransactionManager tm =  new TransactionManager();  
        try  
        {  
            tm.begin();  
            SQLExecutor.deleteWithDBName("bspf","delete from table1 where id=1");  
            SQLExecutor.updateWithDBName("mq","update table1 set value='test' where id=1");  
            tm.commit();  
            DBUtil.debugStatus();//debug连接池实时状态  
        }  
        catch(Exception e)  
        {  
              
        }  
        finally  
        {  
            tm.release();  
        }  
        DBUtil.debugStatus();;//debug连接池实时状态  
  
    }  
```

bspf和query两个数据源的配置（poolman.xml）：

Xml代码

```xml
<?xml version="1.0" encoding="utf-8"?>  
<poolman>  
      
    <datasource>  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
      ......  
    </datasource>  
    <datasource>  
        <dbname>query</dbname>  
        。。。。。  
    </datasource>  
</poolman>  
```

详细的配置请参考：

[bbossgroups持久层框架数据源配置文件实例](http://yin-bp.iteye.com/blog/1112892)