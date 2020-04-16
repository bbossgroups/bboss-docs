### 关于bboss persistent主键生成机制的说明

**4.14 主键的生成**

bboss主键生成有两种模式，一种模式是在对象主键属性上加@PrimaryKey（uuid主键）或者@PrimaryKey(auto=true,pkname="xxxx")（pkname对应tableinfo中的TABLE_NAME子字段的值），另外一种模式就是通过DBUtil.getNextPrimaryKey方法再程序中获取（前提是表的主键信息需要配置到tableinfo中，可以支持uuid和oracle的sequence，以及mysql的自定义sequence）。

对于mysql之类可以自动产生主键的数据库，如果获取自动产生的主键请参考文档：

[bboss持久层返回mysql自增主键功能说明](http://yin-bp.iteye.com/blog/1739260)

模式一 注解模式实例

自动生成uuid主键，生成的主键值会自动赋给id属性，这种模式不需要进行额外配置

Java代码  

```java
public class POBean  
{  
    @PrimaryKey  
    private String id ;  
}  
```

默认采用com.frameworkset.common.poolman.sql.StrongUuidGenerator生成uuid，用户可以自己实现接口
com.frameworkset.common.poolman.sql.IdGenerator来自定义uuid主键生成机制，自定义的uuid生成组件可以在poolman.xml文件中配置：

Xml代码

```xml
<datasource>   
....  
<idGenerator>com.frameworkset.common.poolman.sql.StrongUuidGenerator</idGenerator>  
......  
</datasource> 
```

如何在程序中使用IdGenerator来生成主键：

Java代码

```java
com.frameworkset.common.poolman.sql.IdGenerator ienerator = com.frameworkset.common.poolman.DBUtil.getPool("bspf").getIdGenerator();//指定数据源  
或者  
com.frameworkset.common.poolman.sql.IdGenerator ienerator = com.frameworkset.common.poolman.DBUtil.getPool().getIdGenerator();//默认数据源                     
String value = ienerator.getNextId();//获取主键  
```

根据tableinfo中配置的主键信息生成记录主键，生成的主键值会自动赋给id属性

Java代码

```java
public class POBean  
{  
    @PrimaryKey(pkname="ListBean",auto=true)  
    private String id ;  
}  
```

模式二实例 编程模式

主键信息表

Poolman可以自动生成表的主键，前提是将表的主键信息配置到tableinfo表中

TABLE_NAME           VARCHAR2(255)   表名称                

TABLE_ID_NAME        VARCHAR2(255),   表的主键名称

TABLE_ID_INCREMENT   NUMBER(5),      表的主键自增值

TABLE_ID_VALUE       NUMBER(20),  表的主键的当前值

TABLE_ID_GENERATOR   VARCHAR2(255),  表的主键自定义生成机制，

可提供poolman之外的生成机制，

比如数据库sequence名称，要求TABLE_ID_TYPE是sequence

TABLE_ID_TYPE        VARCHAR2(255), 表主键类型,取值范围：string,int,long,sequence

TABLE_ID_PREFIX      VARCHAR2(255) 主键前缀，如果TABLE_ID_TYPE的值为string，则可以添加前缀，否则不可以

4.14.2人工获取主键的接口

com.frameworkset.common.poolman.DBUtil组件定义了一组getNextPrimaryKey静态方法，用户在程序中获取主键配置在tableinfo表中的主键信息。

1. 获取数字类型的主键

获取缺省数据库中表的主键信息

public static long getNextPrimaryKey(String tableName) throws SQLException

获取指定数据库中表的主键信息

public static long getNextPrimaryKey(String dbName,String tableName) throws SQLException    

2. 获取字符串类型的主键

获取缺省数据库中表的主键信息

public static String getNextStringPrimaryKey(String tableName) throws SQLException


获取指定数据库中表的主键信息

public static String getNextStringPrimaryKey(String dbName,String tableName) throws SQLException


特别声明

持久层框架在poolman.xml配置文件中新增了配置属性：  

false

可以通过该开关来控制是否启用为sql语句自动补全主键的功能，这样能够避免持久层框架去分析每一条insert语句，提升系统性能。

控制的开关autoprimarykey的值为false时，表示不自动补全insert语句中的主键值；为true时，表示启用自动补全主键的功能，但是主键信息必须配置在tableinfo表中。  