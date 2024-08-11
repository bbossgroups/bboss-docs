# bboss MongoDB组件介绍

bboss提供一个简单的MongoDB操作组件，基于MongoDB官方驱动进行封装：

```java
org.frameworkset.nosql.mongodb.MongoDBHelper
```

由于MongoDB官方驱动api比较简单易用，因此bboss只是通过MongoDBHelper组件来额外管理MongoDB数据源，MongoDBHelper可以管理单个或多个MongoDB数据源，MongoDBHelper的功能主要有：

1) 启动和关闭MongoDB数据源

```java
 //定义和启动MongoDB数据源testmg1
MongoDBConfig mongoDBConfig = new MongoDBConfig();
mongoDBConfig.setName("testmg1");
mongoDBConfig.setConnectString("mongodb://192.168.137.2:27017,192.168.137.2:27018,192.168.137.2:27019/?replicaSet=rs0");
started = MongoDBHelper.init(mongoDBConfig);//可以通过init方法启动多个数据源
//关闭MongoDB数据源
MongoDBHelper.closeDB("testmg1");
```

2) 获取MongoDB数据库和数据库表

```java
     MongoDatabase mongoDatabase = MongoDBHelper.getDB("testmg1",//指定MongoDB数据源名称：testmg1
                "useusu");//database;
        MongoCollection dbcollectoin  = MongoDBHelper.getDBCollection("testmg1",//指定MongoDB数据源名称：testmg1
                "useusu",//database
                "classes" //table
```

## 1.导入bboss MongoDB组件

gradle

```java
compile 'com.bbossgroups:bboss-data:6.2.3' 
```

maven


```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>6.2.3</version>  
</dependency>  
```

## 2.启动和关闭MongoDB数据源

### 2.1 启动MongoDB数据源

先通过一段简单的代码来看看bboss MongoDB组件的使用方法

```java
 //数据源testmg1定义
		MongoDBConfig mongoDBConfig = new MongoDBConfig();
		mongoDBConfig.setName("testmg1");
//		mongoDBConfig.setCredentials(mongoDBInputConfig.getCredentials());
		mongoDBConfig.setUserName("bboss");
		mongoDBConfig.setPassword("bboss");
		mongoDBConfig.setAuthDb("sessions");
		mongoDBConfig.setMechanism("MONGODB-CR");
//		mongoDBConfig.setServerAddresses(mongoDBInputConfig.getServerAddresses());
		mongoDBConfig.setWriteConcern("JOURNAL_SAFE");//private String writeConcern;
		/**
		 * if (readPreference.equals("PRIMARY"))
		 * 			return ReadPreference.primary();
		 * 		else if (readPreference.equals("SECONDARY"))
		 * 			return ReadPreference.secondary();
		 * 		else if (readPreference.equals("SECONDARY_PREFERRED"))
		 * 			return ReadPreference.secondaryPreferred();
		 * 		else if (readPreference.equals("PRIMARY_PREFERRED"))
		 * 			return ReadPreference.primaryPreferred();
		 * 		else if (readPreference.equals("NEAREST"))
		 * 			return ReadPreference.nearest();
		 */
		mongoDBConfig.setReadPreference("SECONDARY");//private String readPreference;

		mongoDBConfig.setConnectionsPerHost(100);//private int connectionsPerHost = 50;

		mongoDBConfig.setMaxWaitTime(120000);//private int maxWaitTime = 120000;
		mongoDBConfig.setSocketTimeout(120000);//private int socketTimeout = 0;
		mongoDBConfig.setConnectTimeout(120000);//private int connectTimeout = 15000;


		mongoDBConfig.setSocketKeepAlive(true);//private Boolean socketKeepAlive = false;

		mongoDBConfig.setConnectString("mongodb://192.168.137.1:27017,192.168.137.1:27018,192.168.137.1:27019/?replicaSet=rs0");
		boolean started = MongoDBHelper.init(mongoDBConfig);//数据源正常启动，返回true
```

### 2.2 关闭MongoDB数据源

关闭MongoDB数据源，指定要关闭的数据源名称即可

```java
MongoDBHelper.closeDB("testmg1");
```

## 3.使用数据源操作MongoDB

在数据源testmg1上执行MongoDB操作：

```java
MongoCollection<Document> dbcollectoin  = MongoDBHelper.getDBCollection("testmg1",//指定MongoDB数据源名称：testmg1
              "useusu",//database
       "classes" //table
       );
 
String attribute = "name";//MongoDBHelper.converterSpecialChar( "name");//如果名称中包含mongodb关键字特殊字符，则需要进行转换
 
Document obj = dbcollectoin.findOneAndDelete(new BasicDBObject(attribute,"一六班-1"));//查询条件，返回所有字段
       
if(obj == null)
    return null;
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
StringBuilder result = new StringBuilder();
result.append("name=").append((String)obj.get(attribute)).append(",")
.append("creationTime=").append(format.format((Date)obj.get("creationTime"))).append(",")
.append("members=").append(obj.get("members"));
System.out.println(result);
```



## 4.MongoDB多数据源配置和使用

可以配置多个MongoDB集群数据源，例如：

```java
//数据源testmg1定义
		MongoDBConfig mongoDBConfig = new MongoDBConfig();
		mongoDBConfig.setName("testmg1");
//		mongoDBConfig.setCredentials(mongoDBInputConfig.getCredentials());
		mongoDBConfig.setUserName("bboss");
		mongoDBConfig.setPassword("bboss");
		mongoDBConfig.setAuthDb("sessions");
		mongoDBConfig.setMechanism("MONGODB-CR");
//		mongoDBConfig.setServerAddresses(mongoDBInputConfig.getServerAddresses());
		mongoDBConfig.setWriteConcern("JOURNAL_SAFE");//private String writeConcern;
		/**
		 * if (readPreference.equals("PRIMARY"))
		 * 			return ReadPreference.primary();
		 * 		else if (readPreference.equals("SECONDARY"))
		 * 			return ReadPreference.secondary();
		 * 		else if (readPreference.equals("SECONDARY_PREFERRED"))
		 * 			return ReadPreference.secondaryPreferred();
		 * 		else if (readPreference.equals("PRIMARY_PREFERRED"))
		 * 			return ReadPreference.primaryPreferred();
		 * 		else if (readPreference.equals("NEAREST"))
		 * 			return ReadPreference.nearest();
		 */
		mongoDBConfig.setReadPreference("SECONDARY");//private String readPreference;

		mongoDBConfig.setConnectionsPerHost(100);//private int connectionsPerHost = 50;

		mongoDBConfig.setMaxWaitTime(120000);//private int maxWaitTime = 120000;
		mongoDBConfig.setSocketTimeout(120000);//private int socketTimeout = 0;
		mongoDBConfig.setConnectTimeout(120000);//private int connectTimeout = 15000;


		mongoDBConfig.setSocketKeepAlive(true);//private Boolean socketKeepAlive = false;

		mongoDBConfig.setConnectString("mongodb://192.168.137.1:27017,192.168.137.1:27018,192.168.137.1:27019/?replicaSet=rs0");
		boolean started = MongoDBHelper.init(mongoDBConfig);

        //数据源testmg2定义
        mongoDBConfig = new MongoDBConfig();
        mongoDBConfig.setName("testmg2");
//		mongoDBConfig.setCredentials(mongoDBInputConfig.getCredentials());
        mongoDBConfig.setUserName("bboss");
        mongoDBConfig.setPassword("bboss");
        mongoDBConfig.setAuthDb("sessions");
        mongoDBConfig.setMechanism("MONGODB-CR");
//		mongoDBConfig.setServerAddresses(mongoDBInputConfig.getServerAddresses());
        mongoDBConfig.setWriteConcern("JOURNAL_SAFE");//private String writeConcern;
        /**
         * if (readPreference.equals("PRIMARY"))
         * 			return ReadPreference.primary();
         * 		else if (readPreference.equals("SECONDARY"))
         * 			return ReadPreference.secondary();
         * 		else if (readPreference.equals("SECONDARY_PREFERRED"))
         * 			return ReadPreference.secondaryPreferred();
         * 		else if (readPreference.equals("PRIMARY_PREFERRED"))
         * 			return ReadPreference.primaryPreferred();
         * 		else if (readPreference.equals("NEAREST"))
         * 			return ReadPreference.nearest();
         */
        mongoDBConfig.setReadPreference("SECONDARY");//private String readPreference;

        mongoDBConfig.setConnectionsPerHost(100);//private int connectionsPerHost = 50;

        mongoDBConfig.setMaxWaitTime(120000);//private int maxWaitTime = 120000;
        mongoDBConfig.setSocketTimeout(120000);//private int socketTimeout = 0;
        mongoDBConfig.setConnectTimeout(120000);//private int connectTimeout = 15000;


        mongoDBConfig.setSocketKeepAlive(true);//private Boolean socketKeepAlive = false;

        mongoDBConfig.setConnectString("mongodb://192.168.137.2:27017,192.168.137.2:27018,192.168.137.2:27019/?replicaSet=rs0");
        started = MongoDBHelper.init(mongoDBConfig);
```

配置好多数据源后，通过以下方法获取指定的数据源的MongoDB数据库表实例

```java
    //在默认数据源上操作，第一个启动的MongoDB数据源为默认数据源，startDBs方法中第一个启动的数据源名称为：startDBs，所以默认为：testmg1
			MongoCollection dbcollectoin  = MongoDBHelper.getDBCollection("useusu",//database
																	"classes" //table
					);

 MongoCollection dbcollectoin  = MongoDBHelper.getDBCollection("testmg2",//指定MongoDB数据源名称：testmg2
                    "useusu",//database
                    "classes" //table
            );

MongoCollection<Document> dbcollectoin  = MongoDBHelper.getDBCollection("testmg1",//指定MongoDB数据源名称：testmg1
                "useusu",//database
				"classes" //table
				);
```

## 5.高阶应用

MongoDB组件高阶应用：

1. bboss etl及流批一体化计算引擎[MongoDB输入插件](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_17-mongodb%e9%87%87%e9%9b%86%e6%8f%92%e4%bb%b6)
2. bboss etl及流批一体化计算引擎[MongoDB输出插件](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_28-mongodb%e8%be%93%e5%87%ba%e6%8f%92%e4%bb%b6)
3. bboss etl及流批一体化计算引擎[MongoDB CDC插件](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_18-mongodb-cdc%e6%8f%92%e4%bb%b6)

本文涉及案例源码下载地址

https://gitee.com/bboss/bestpractice/tree/master/testmongo