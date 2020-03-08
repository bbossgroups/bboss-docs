### bbossgroups持久层sql配置文件多数据库sql语句配置机制

  bbossgroups持久层sql配置文件支持同一个配置名称对应不同数据库sql语句的配置机制，具体的原理如下：

1.多数据库sql语句配置机制

可以通过名称属性name配置默认sql，特定数据库的sql通过在名称后面加数据库类型后缀来区分，例如：

sqltest

sqltest-oracle

sqltest-derby

sqltest-mysql  

bboss持久层支持的数据库类型有：

- as400
- db2app
- db2net
- cloudscape
- hypersonic
- interbase
- instantdb
- mssql
- mysql
- mariadb
- oracle
- postgresql
- sapdb
- sybase
- weblogic
- axion
- informix
- odbc
- msaccess
- derby

2.ConfigSQLExecutor执行数据库操作时，根据指定的数据源类型获取特定数据库sql语句机制

  我们以例子来说明这个机制，首先看一个sql配置文件示例：

Xml代码

```xml
<?xml version="1.0" encoding='gb2312'?>  
<properties>  
         <!--默认sql语句-->  
    <property name="sqltest"><![CDATA[select name from LISTBEAN where id=1]]>  
    </property>  
         <!--mysql sql语句-->  
    <property name="sqltest-mysql"><![CDATA[select ifnull(name,'匿名用户') from LISTBEAN where id=1]]>           
    </property>  
         <!--oracle sql语句-->  
         <property name="sqltest-oracle"><![CDATA[select nvl(name,'匿名用户') from LISTBEAN where id=1]]>           
    </property>   
</properties>  
```

我们配置了名称为sqltest的三条sql语句,一条默认的sql语句，一条对应于mysql数据库，使用了mysql的ifnull函数，一条对应于oracle数据库，使用了oracle的nvl函数，然后根据配置文件内容实例化一个ConfigSQLExecutor对象，并根据sqltest做相应的查询操作：

Java代码

```java
ConfigSQLExecutor executor = new ConfigSQLExecutor("com/frameworkset/sqlexecutor/sqlfile.xml");  
Map dbBeans  =  executor.queryObjectWithDBName(HashMap.class,"ds", "sqltest");   
```

我们在poolman.xml文件中配置了一个名称为ds的mysql数据源：

Xml代码

```xml
<datasource>  
  
    <dbname>ds</dbname>  
    <loadmetadata>false</loadmetadata>  
    <jndiName>jdbc/mysql-ds</jndiName>  
    <driver>com.mysql.jdbc.Driver</driver>  
  
     <url>jdbc:mysql://localhost:3306/cim</url>   
  
    <username>root</username>  
    <password>123456</password>  
  
   。。。。。。  
  </datasource>  
```

这样持久层框架在执行executor.queryObjectWithDBName(HashMap.class,"ds", "sqltest"); 查询操作时，会自动识别出数据源ds是一个mysql数据源，然后就会执行sqltest-mysql对应的sql语句，如果是oracle数据源则会执行sqltest-oracle对应的sql语句，其他类型数据库就会执行默认的sqltest对应的sql语句。

到此bbossgroups持久层sql配置文件多数据库sql语句配置和具体实现原理就介绍完毕了，如有疑问请留言讨论。  