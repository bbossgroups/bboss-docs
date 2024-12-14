# Clickhouse客户端负载均衡和容灾功能使用介绍

 本文讲解如何基于bboss和Clickhouse native jdbc（第三方，基于tcp端口）、Clickhouse jdbc（官方，基于http端口）实现Clickhouse客户端负载均衡和容灾功能，主要内容如下：

1. Clickhouse native jdbc介绍
2. 基于Clickhouse native jdbc实现负载均衡和灾备功能
3. 应用案例
4. Clickhouse jdbc使用介绍



## 1.Clickhouse native java介绍

### 1.1 官方描述

  Clickhouse native jdbc一个基于原生(TCP)协议实现的 JDBC 驱动，用于访问 [ClickHouse](https://clickhouse.yandex/) ，同时也支持与 [Apache Spark](https://github.com/apache/spark/) 的集成。

### 1.2 驱动类

com.github.housepower.jdbc.ClickHouseDriver

在项目中导入Clickhouse native jdbc驱动包
- Gradle

```groovy

// (recommended) shaded version, available since 2.7.1
api "com.github.housepower:clickhouse-native-jdbc-shaded:${clickhouse_native_jdbc_version}"

// normal version
api "com.github.housepower:clickhouse-native-jdbc:${clickhouse_native_jdbc_version}"

```
- Maven

```xml
<!-- (recommended) shaded version, available since clickhouse-native-jdbc.version 2.7.1 -->
<dependency>
    <groupId>com.github.housepower</groupId>
    <artifactId>clickhouse-native-jdbc-shaded</artifactId>
    <version>${clickhouse-native-jdbc.version}</version>
</dependency>

<!-- normal version clickhouse-native-jdbc.version 2.7.1-->
<dependency>
    <groupId>com.github.housepower</groupId>
    <artifactId>clickhouse-native-jdbc</artifactId>
    <version>${clickhouse-native-jdbc.version}</version>
</dependency>
```

### 1.3  负载均衡使用案例

基于BalancedClickhouseDataSource类来实现**负载均衡功能**

```java

DataSource singleDataSource = new BalancedClickhouseDataSource("jdbc:clickhouse://127.0.0.1:9000/db1");

DataSource dualDataSource = new BalancedClickhouseDataSource("jdbc:clickhouse://192.168.137.1:9000,192.168.137.2:9000/db1");

Connection conn1 = dualDataSource.getConnection();
conn1.createStatement().execute("CREATE DATABASE IF NOT EXISTS test");

Connection conn2 = dualDataSource.getConnection();
conn2.createStatement().execute("DROP TABLE IF EXISTS test.insert_test");
conn2.createStatement().execute("CREATE TABLE IF NOT EXISTS test.insert_test (i Int32, s String) ENGINE = TinyLog");
```

### 1.4 BalancedClickhouseDataSource特性分析

数据源类BalancedClickhouseDataSource为客户端提供负载均衡的能力，**只支持Random负载均衡机制，同时在容错处理及容错恢复方面的能力缺失。**

### 1.5 基于Apache DBCP数据源使用案例（灾备特性）

Add dependency in Maven pom.xml

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-dbcp2</artifactId>
    <version>2.11.0</version>
</dependency>

```

Use BasicDataSource.
```java

Properties prop = new Properties();
prop.put("url", "jdbc:clickhouse://192.168.137.1:9000,192.168.137.2:9000/db1");
prop.put("driverClassName", "com.github.housepower.jdbc.ClickHouseDriver");
try (BasicDataSource ds = BasicDataSourceFactory.createDataSource(prop)) {
    runSql(ds);
}

```

### 1.6 基于Apache DBCP数据源特性分析

基于Apache DBCP数据源直接使用驱动ClickHouseDriver，**只能基于集群多个节点的实现客户端灾备功能，不具备负载均衡功能**，例如，以下url中指定的多个地址：


```
jdbc:clickhouse://192.168.137.1:9000,192.168.137.2:9000,192.168.137.3:9000/db1
```

正常情况下只会将请求发送给地址清单中的第一个地址，后面的地址作为第一个地址的备用地址，只有在第一个地址不可用时，才会起作用，这样就会导致第一个节点负载过高其他节点资源闲置的问题。

上述两个案例都存在明显的缺陷（至少从本文撰写时的版本是这样的），为此我们基于bboss持久层和Clickhouse native jdbc，结合bboss的数据源负载均衡功能+ClickHouseDriver灾备功能，实现了完整的Clickhouse客户端负载均衡和容灾功能。

## 2.基于Clickhouse native jdbc实现负载均衡和灾备功能

 结合bboss的数据源负载均衡功能+ClickHouseDriver灾备功能，可以实现完整的Clickhouse客户端负载均衡和容灾功能，下面进行详细介绍。

参考章节1导入Clickhouse native jdbc驱动包，再导入bboss持久层包：

gradle

```groovy
api "com.bbossgroups:bboss-persistent:${BBOSS_VERSION}"
```

maven

```xml
<dependency>   
    <groupId>com.bbossgroups</groupId>   
    <artifactId>bboss-persistent</artifactId>   
    <version>${BBOSS_VERSION}</version>   
</dependency> 
```

**BBOSS_VERSION**对应bboss的版本号，最新版本为**6.2.7**，可以到maven中央库获取最新的版本

https://central.sonatype.com/artifact/com.bbossgroups/bboss-persistent

bboss持久层操作Clickhouse实现负载均衡和灾备的两种使用方法：

### 2.1 方法1 在jdbc url地址后面增加b.balance和b.enableBalance参数

```
jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/visualops?b.balance=roundbin&b.enableBalance=true
```

b.enableBalance为true时启用负载均衡机制，并具备原有容灾功能，否则只具备容灾功能

b.balance 指定负载均衡算法，目前支持random（随机算法，不公平机制）和roundbin(轮询算法，公平机制)两种算法，默认random算法

启动数据源和进行Clickhouse操作的实例代码：*在**url中指定启用负载均**衡机制**以及负载均衡算法，默认具备ClickHouseDriver的灾备功能*

```java
   DBConf tempConf = new DBConf();
        tempConf.setPoolname("test");
        tempConf.setDriver("com.github.housepower.jdbc.ClickHouseDriver");
        //在url中指定启用负载均衡机制以及负载均衡算法，默认具备ClickHouseDriver的灾备功能
        tempConf.setJdbcurl( "jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/visualops?b.balance=roundbin&b.enableBalance=true");
        tempConf.setUsername("default");
        tempConf.setPassword(null);
        tempConf.setValidationQuery("select 1 ");
        //tempConf.setTxIsolationLevel("READ_COMMITTED");
        tempConf.setJndiName("jndi-test");
        PropertiesContainer propertiesContainer = PropertiesUtil.getPropertiesContainer();
        int initialConnections = propertiesContainer.getIntProperty("initialConnections",5);
        tempConf.setInitialConnections(initialConnections);
        int minimumSize = propertiesContainer.getIntProperty("minimumSize",5);
        tempConf.setMinimumSize(minimumSize);
        int maximumSize = propertiesContainer.getIntProperty("maximumSize",10);
        tempConf.setMaximumSize(maximumSize);
        tempConf.setUsepool(true);
        tempConf.setExternal(false);
        tempConf.setEncryptdbinfo(false);
        boolean showsql = propertiesContainer.getBooleanProperty("showsql",true);
        tempConf.setShowsql(showsql);
        tempConf.setQueryfetchsize(null);    
        SQLManager.startPool(tempConf);
		//操作Clickhouse
        List<Map> datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));
```

### 2.2 方法2 在DBConf上设置balance和enableBalance参数


```java

BConf tempConf = new DBConf();
tempConf.setPoolname("test");
tempConf.setDriver("com.github.housepower.jdbc.ClickHouseDriver");
tempConf.setJdbcurl( "jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/visualops");
tempConf.setUsername("default");
tempConf.setPassword(null);
tempConf.setValidationQuery("select 1 ");
tempConf.setJndiName("jndi-test");
PropertiesContainer propertiesContainer = PropertiesUtil.getPropertiesContainer();
int initialConnections = propertiesContainer.getIntProperty("initialConnections",5);
tempConf.setInitialConnections(initialConnections);
int minimumSize = propertiesContainer.getIntProperty("minimumSize",5);
tempConf.setMinimumSize(minimumSize);
int maximumSize = propertiesContainer.getIntProperty("maximumSize",10);
tempConf.setMaximumSize(maximumSize);
tempConf.setUsepool(true);
tempConf.setExternal(false);
tempConf.setEncryptdbinfo(false);
boolean showsql = propertiesContainer.getBooleanProperty("showsql",true);
tempConf.setShowsql(showsql);
tempConf.setQueryfetchsize(null);
tempConf.setEnableBalance(true); //启用负载均衡机制
tempConf.setBalance(DBConf.BALANCE_RANDOM);//指定负载均衡算法
return SQLManager.startPool(tempConf);
```

案例对应的源码：

https://gitee.com/bboss/bestpractice/blob/master/persistent/src/com/frameworkset/sqlexecutor/TestClickHouseDB.java

## 3.应用案例
上面介绍了如何基于bboss和Clickhouse native jdbc实现Clickhouse客户端负载均衡和容灾功能，本节介绍一个比较高阶的应用，在bboss etl&流批一体化处理框架中使用这个功能。

### 3.1 在数据库输入插件中使用


```java

DBInputConfig dbInputConfig = new DBInputConfig();
    dbInputConfig.setDbName("source")
        .setDbDriver("com.github.housepower.jdbc.ClickHouseDriver") //数据库驱动程序，必须导入相关数据库的驱动jar包
                .setDbUrl("jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/bboss") // jdbc_port = 29000
                .setDbUser("default")
                .setDbPassword("")
                .setBalance(DBConf.BALANCE_RANDOM)//指定负载均衡算法
                .setEnableBalance(true)//启用负载均衡机制
                .setValidateSQL("select 1")
        .setUsePool(true)//是否使用连接池
        .setSqlFilepath("sql.xml")
        .setSqlName("demoexport");
```

### 3.2 在数据库输出插件中使用

```java

DBOutputConfig dbOutputConfig = new DBOutputConfig();
    dbOutputConfig.setDbName("target")
        .setDbDriver("com.github.housepower.jdbc.ClickHouseDriver") //数据库驱动程序，必须导入相关数据库的驱动jar包
        .setDbUrl("jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/bboss") // jdbc_port = 29000
        .setDbUser("default")
        .setDbPassword("")
        .setValidateSQL("select 1")
        .setUsePool(true)//是否使用连接池
        .setMaxWait(300000)
        .setConnectionTimeout(200000)
        .setBalance(DBConf.BALANCE_RANDOM)//指定负载均衡算法
        .setEnableBalance(true)//启用负载均衡机制
        .setSqlFilepath("sql.xml")
        .setInsertSqlName("insertClickhouseSql");
    importBuilder.setOutputConfig(dbOutputConfig);
```

案例对应的源码：

https://gitee.com/bboss/bboss-datatran-demo/blob/main/src/main/java/org/frameworkset/elasticsearch/imp/clickhouse/Clickhouse2Clickhousedemo.java

也可以在jdbc url地址后面增加b.balance和b.enableBalance参数来设置和启用负载均衡和灾备机制：

```shell
jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/bboss?b.balance=roundbin&b.enableBalance=true
```

### 3.3 在bboss持久层中使用

```java
	//操作Clickhouse
        List<Map> datas = SQLExecutor.queryList(Map.class,"select * from testtable");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));
```

# 4.Clickhouse jdbc使用

官方驱动Clickhouse jdbc基于http/https协议端口连接Clickhouse，支持负载均衡和容灾功能，jdbc url实例如下：

```json
jdbc:ch://(http://101.13.6.4:28123),(http://101.13.6.7:28123),(http://101.13.6.6:28123)/bboss?failover=1&load_balancing_policy=random
```



## 4.1 导入驱动

gradle

```groovy
 api 'com.clickhouse:clickhouse-jdbc:0.5.0:http'
    api (
            [group: 'org.apache.httpcomponents.client5', name: 'httpclient5', version: "5.4-alpha2", transitive: true]
    )
    {
        exclude group: 'org.apache.httpcomponents.core5', module: 'httpcore5'
        exclude group: 'org.slf4j', module: 'slf4j-api'
    }
//            <dependency>
//            <groupId>org.apache.httpcomponents.client5</groupId>
//            <artifactId>httpclient5</artifactId>
//    <exclusions>
//            <exclusion>
//            <groupId>org.apache.httpcomponents.core5</groupId>
//                    <artifactId>httpcore5</artifactId>
//            </exclusion>
//                <exclusion>
//                    <groupId>org.slf4j</groupId>
//    <artifactId>slf4j-api</artifactId>
//                </exclusion>
//            </exclusions>
//            <optional>true</optional>
//    </dependency>
    api 'org.apache.httpcomponents.core5:httpcore5:5.3-alpha2'
```

## 4.1 启动数据源及访问Clickhouse实例

```java
   DBConf tempConf = new DBConf();
        tempConf.setPoolname("test");
        tempConf.setDriver("com.clickhouse.jdbc.ClickHouseDriver");
        //在url中指定启用负载均衡机制以及负载均衡算法，默认具备ClickHouseDriver的灾备功能
        tempConf.setJdbcurl( "jdbc:ch://(http://101.13.6.4:28123),(http://101.13.6.7:28123),(http://101.13.6.6:28123)/bboss?failover=1&load_balancing_policy=random");
        tempConf.setUsername("default");
        tempConf.setPassword(null);
        tempConf.setValidationQuery("select 1 ");
        //tempConf.setTxIsolationLevel("READ_COMMITTED");
        tempConf.setJndiName("jndi-test");
        PropertiesContainer propertiesContainer = PropertiesUtil.getPropertiesContainer();
        int initialConnections = propertiesContainer.getIntProperty("initialConnections",5);
        tempConf.setInitialConnections(initialConnections);
        int minimumSize = propertiesContainer.getIntProperty("minimumSize",5);
        tempConf.setMinimumSize(minimumSize);
        int maximumSize = propertiesContainer.getIntProperty("maximumSize",10);
        tempConf.setMaximumSize(maximumSize);
        tempConf.setUsepool(true);
        tempConf.setExternal(false);
        tempConf.setEncryptdbinfo(false);
        boolean showsql = propertiesContainer.getBooleanProperty("showsql",true);
        tempConf.setShowsql(showsql);
        tempConf.setQueryfetchsize(null);    
        SQLManager.startPool(tempConf);
		//操作Clickhouse
        List<Map> datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));

        datas = SQLExecutor.queryList(Map.class,"show tables");
        System.out.println(SimpleStringUtil.object2json(datas));
```

