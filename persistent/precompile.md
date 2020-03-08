### bboss预编译批处理api使用介绍

bboss预编译批处理功能能够非常方便地完成对数据的批量插入、批量删除、批量更新操作。所谓批量操作就是一次向数据库中执行多条记录操作，bboss批量处理操作全部采用预编译方式执行，执行效率非常不错。本文详细介绍bboss持久层框架中批量处理操作的api及使用实例。切入正题。

1.相关组件

com.frameworkset.sqlexecutor.SQLExecutor-所有的api都是静态方法，直接操作原始sql语句，所有的方法都可以指定操作的数据源，也可以不指定（这样会在poolman.xml文件中的第一个数据源上执行对应的sql）

com.frameworkset.sqlexecutor.ConfigSQLExecutor--所有的api都是实例方法，一个ConfigSQLExecutor对象实例必须以一个sql配置文件路径作为参数的构造函数来实例化；实例的所有方法不能直接使用sql语句，只能指定一个配置sql的名称，这个名称对应sql配置文件中的一条sql语句；ConfigSQLExecutor中所有的方法都可以指定操作的数据源，也可以不指定（这样会在poolman.xml文件中的第一个数据源上执行对应的sql）。

2.批量插入操作

api

Java代码  

```java
SQLExecutor.insertBeans(sql,beans);  
SQLExecutor.insertBeans(dbname,sql,beans);  
  
ConfigSQLExecutor.insertBeans(sqlname,beans);  
ConfigSQLExecutor.insertBeans(dbname,sqlname,beans);  
```

使用实例-以SQLExecutor为例

Java代码 

```java
public void batchadd(List<TestBean> newdatas)  
    {                 
        //sql中的变量对应TestBean中的属性名称，必须要有相应的get/set方法,框架会自动将其转换为预编译占位符的sql语句  
        String sql = "insert into LISTBEAN(ID,FIELDNAME,FIELDLABLE,FIELDTYPE,SORTORDER," +  
                "ISPRIMARYKEY,REQUIRED,FIELDLENGTH,ISVALIDATED) " +  
                "values(#[id],#[fieldName],#[fieldLable],#[fieldType],#[sortorder]," +  
                "#[isprimaryKey],#[required],#[fieldLength],#[isvalidated])";  
        //SQLExecutor.insertBeans(sql,newdatas);//不带数据源的方法  
        SQLExecutor.insertBeans("testds",//数据源  
                                sql,//数据库sql语句  
                                newdatas//批量插入的对象记录集  
                                );  
    }  
```

3.批量更新操作

api

Java代码

```java
SQLExecutor.updateBeans(sql,beans);  
SQLExecutor.updateBeans(dbname,sql,beans);  
  
ConfigSQLExecutor.updateBeans(sqlname,beans);  
ConfigSQLExecutor.updateBeans(dbname,sqlname,beans);  
```

使用实例-以SQLExecutor为例

Java代码

```java
public void batchadd(List<TestBean> updatedatas)  
{  
       sql ="update LISTBEAN set FIELDNAME=#[fieldName] where ID=#[id]";   
                  //SQLExecutor.updateBeans(sql,updatedatas);  
  
        SQLExecutor.updateBeans("testds",sql,updatedatas);  
}  
```

4.批量删除操作

api

Java代码

```java
SQLExecutor.deleteBeans(sql,beans);  
SQLExecutor.deleteBeans(dbname,sql,beans);  
ConfigSQLExecutor.deleteBeans(sqlname,beans);  
ConfigSQLExecutor.deleteBeans(dsname,sqlname,beans);  
```

使用实例-以SQLExecutor为例

Java代码

```java
public void batchadd(List<TestBean> updatedatas)  
{  
       sql ="delete  from LISTBEAN where ID=#[id]";     
       //SQLExecutor.deleteBeans(sql,beans);  
       SQLExecutor.deleteBeans("mysql",sql,beans);  
}  
```

到此bboss中三种典型的批量处理操作的api及使用实例就介绍完了，至于poolman.xml中配置数据源请参考文章：
http://yin-bp.iteye.com/blog/352599

ConfigSQLExecutor组件的更详细的使用实例请参考以下文章：
http://yin-bp.iteye.com/blog/1112997

SQLExecutor组件的更详细的使用实例请参考以下文章：
http://yin-bp.iteye.com/blog/1035991

这里说明的都是单条sql语句的预编译批处理操作，还可以参考以下多条sql语句同时进行预编译批处理的实例：
https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/common/TestPreparedBatch.java