### bboss持久层多数据源配置及多数据库事务控制使用方法

bboss持久层多数据源配置及使用方法，持久层框架及demo下载请参看文档：http://yin-bp.iteye.com/blog/1080824

**1.配置多个数据源-poolman.xml**

在classes类路径根目录下准备好dbcp.xml和dbcp1.xml两个配置文件（基于bboss ioc语法）dbcp.xml:

Xml代码

```xml
<property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/>    
    <property name="url" value="jdbc:oracle:thin:@//10.0.15.51:1521/orcl"/>    
    <property name="username" value="afms"/>    
    <property name="password" value="afms"/>    
    <!--initialSize: 初始化连接-->    
    <property name="initialSize" value="5"/>    
    <property name="maxTotal" value="20"/>    
    <!--maxIdle: 最大空闲连接-->    
    <property name="maxIdle" value="20"/>    
    <!--minIdle: 最小空闲连接-->    
    <property name="minIdle" value="20"/>    
  
    <!--removeAbandoned: 是否自动回收超时连接-->    
    <property name="removeAbandonedOnBorrow" value="false"/>  
    <property name="logAbandoned" value="true"/>  
    <!--removeAbandonedTimeout: 超时时间(以秒数为单位)-->    
    <property name="removeAbandonedTimeout" value="180"/>    
    <!--maxWait: 超时等待时间以毫秒为单位 6000毫秒/1000等于6秒-->    
    <property name="maxWaitMillis" value="3000"/>    
    <property name="validationQuery" value="SELECT 1 from dual"/>     
    <property name="testOnBorrow" value="true"/>   
</property>  
```

dbcp1.xml:

Xml代码

```xml
<property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/>    
    <property name="url" value="jdbc:oracle:thin:@//10.0.15.51:1521/orcl"/>    
    <property name="username" value="bfms"/>    
    <property name="password" value="bfms"/>    
    <!--initialSize: 初始化连接-->    
    <property name="initialSize" value="5"/>    
    <property name="maxTotal" value="20"/>    
    <!--maxIdle: 最大空闲连接-->    
    <property name="maxIdle" value="20"/>    
    <!--minIdle: 最小空闲连接-->    
    <property name="minIdle" value="20"/>    
  
    <!--removeAbandoned: 是否自动回收超时连接-->    
    <property name="removeAbandonedOnBorrow" value="false"/>  
    <property name="logAbandoned" value="true"/>  
    <!--removeAbandonedTimeout: 超时时间(以秒数为单位)-->    
    <property name="removeAbandonedTimeout" value="180"/>    
    <!--maxWait: 超时等待时间以毫秒为单位 6000毫秒/1000等于6秒-->    
    <property name="maxWaitMillis" value="3000"/>    
    <property name="validationQuery" value="SELECT 1 from dual"/>     
    <property name="testOnBorrow" value="true"/>   
</property>  
```

在poolman.xml中装载这两个数据源：

Xml代码 

```xml
<poolman>  
  
  <datasource>  
  
        <dbname>ds0</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>ds0_datasource_jndiname</jndiName>  
        <datasourceFile>dbcp.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
                <RETURN_GENERATED_KEYS>true</RETURN_GENERATED_KEYS>  
                <queryfetchsize>10000</queryfetchsize>  
    </datasource>  
<datasource>  
  
        <dbname>ds1</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>ds1_datasource_jndiname</jndiName>  
        <datasourceFile>dbcp1.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
                <RETURN_GENERATED_KEYS>false</RETURN_GENERATED_KEYS>  
    </datasource>  
</poolman>  
```

  配置了两个数据源ds0和ds1，下面介绍利用持久层api在不同数据源上执行db操作。

**queryfetchsize:指定数据源对应的jdbc 查询操作的fetchsize，不指定的话采用jdbc默认设置**

**2.多数据源api及多数据源事务控制实例**

目前bboss常用的数据库访问组件ConfigSQLExecutor和SQLExecutor都提供了多数据源操作api，这种api中都显示地指定dbname参数，也就是数据源的名称，例如上面的ds0和ds1；另外一种api是不带dbname参数，这种api默认在poolman.xml文件中配置的第一个数据源上执行db操作。两种api举例说明如下（以SQLExecutor组件为例来说明，ConfigSQLExecutor组件除了sql语句从配置文件中读取外,它的api与SQLExecutor组件api一致，就不额外介绍ConfigSQLExecutor了。）：  

Java代码

```java
TransactionManager tm = new TransactionManager();  
try{  
tm.begin();//开始事务  
SQLExecutor.delete("delete from LISTBEAN where id=?",1);//不带数据源dbname的api，默认在第一个数据源上执行db操作，也就是ds0数据源。  
        SQLExecutor.deleteWithDBName("ds0","delete from LISTBEAN where id=?",1);//显示指定db操作在ds0数据源上操作  
        SQLExecutor.deleteWithDBName("ds1","delete from LISTBEAN where id=?",1);//显示指定db操作在ds1数据源上操作  
tm.commit();//提交事务  
}  
catch(Exception e)  
{  
   throw e;  
}  
finally  
{  
   tm.release();//释放事务资源，如果有异常发生，则会回滚事务  
}  
```

上面是以删除操作来做说明，同时我们将两个数据源上的数据库操作包含到一个事务中，这样很好地保证了多数据库操作事务的一致性。其它操作（插入，修改，查询）类似也不举例一一介绍了，具体可参考以下示例文件：

[SimpleApiTest1.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/sqlexecutor/SimpleApiTest1.java)

[ConfigSQLExecutorTest.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/sqlexecutor/ConfigSQLExecutorTest.java)

[SimpleApiTest.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/sqlexecutor/SimpleApiTest.java)
[TestPrepareDBUtilNewInterface.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/common/TestPrepareDBUtilNewInterface.java)