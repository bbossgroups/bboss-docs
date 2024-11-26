# bboss redis组件使用实例

bboss提供一个简单的redis操作组件，基于jedis进行封装

```java
org.frameworkset.nosql.redis.RedisTool
```

RedisTool提供两个单例静态方法,来获取RedisTool实例，支持多redis数据源

```java
RedisTool.getInstance();//获取默认的redis数据源
RedisTool.getInstance(String redisDatasourceName);//获取指定名称的redis数据源
```



## 1.导入bboss redis组件

gradle

```java
compile 'com.bbossgroups:bboss-data:6.2.7' 
```

maven


```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>6.2.7</version>  
</dependency>  
```

## 2.bboss redis组件使用

先通过一段简单的代码来看看bboss redis组件的使用方法

```java
package org.frameworkset.nosql;  
  
import org.frameworkset.nosql.redis.RedisFactory;  
import org.frameworkset.nosql.redis.RedisHelper;  
import org.junit.Test;  
  
public class RedisTest {  
  
    public RedisTest() {  
        // TODO Auto-generated constructor stub  
    }  
    @Test  
    public void get()  
    {  
       	RedisTool.getInstance().set("aaa","ddd");
		RedisTool.getInstance().hset("ddd","aaa","xxxx");
		RedisTool.getInstance().get("aaa");
		RedisTool.getInstance().set("vops_biz_count_history_max","{sss}");
		System.out.println(RedisTool.getInstance().get("vops_biz_count_history_max"));
    }  
       
  
}  
```

## **3.配置Redis组件**



### **3.1 Redis集群配置**

修改resources/redis.xml文件，设置redis的服务器地址和端口



```xml
<properties>  
      
    <property name="default" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            127.0.0.1:6379,127.0.0.1:6380  
        </property>  
        <!-- single|cluster -->  
        <property name="mode" value="cluster" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
       
</properties>  
```

### **3.2 Redis单节点配置**

修改resources/redis.xml文件，设置redis的服务器地址和端口



```xml
<properties>  
      
    <property name="default" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            127.0.0.1:6379  
        </property>  
        <!-- single|cluster -->  
        <property name="mode" value="single" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
       
</properties>
```

**redis配置说明**
**nodes**列表中配置服务器列表，通过host属性指定ip或者域名，通过port属性指定redis节点的端口
**mode**属性指定redis的三种部署模式：

- single 单redis服务器模式，nodes列表只需要配置一个redis服务器的地址和端口即可

- cluster redis集群或者分片集群模式，nodes列表需要配置所有redis服务器的地址和端口（包括主节点和从节点）

- shared  保留，暂不使用

  **auth**：redis服务器认证口令

  **poolMaxTotal**：客户端连接池最大连接数
  **poolMaxWaitMillis**：等待空闲连接超时时间，单位：毫秒

## 4.多数据源配置

可以配置多个redis集群，例如：

```xml
<properties>  
      
    <property name="default" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            127.0.0.1:6379,127.0.0.1:6380  
        </property>  
        <!-- single|cluster -->  
        <property name="mode" value="cluster" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
    
     <property name="buzRedis" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            192.168.1.2:6379,192.168.1.3:6380  
        </property>  
        <!-- single|cluster -->  
        <property name="mode" value="cluster" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
       
</properties> 
```

配置好多数据源后，通过以下方法获取指定的数据源api实例

```java
RedisTool.getInstance("buzRedis");//获取redis2对应的redis数据源
```



## 5.通过代码配置Redis数据源

```java
public class RedisConfigTest {
   @Before
   public void init(){
      //构建名称为test的redis数据源，可以通过RedisFactory.builRedisDB构建其他的数据源
      //不同的数据源设置不同的name，如果对应的name已经被其他redis集群使用，则忽略创建
      RedisConfig redisConfig = new RedisConfig();
      redisConfig.setName("test")
            .setAuth("")
            //集群节点可以通过逗号分隔，也可以通过\n符分隔
//          .setServers("101.13.4.15:6359\n101.13.4.15:6369\n101.13.4.15:6379\n101.13.4.15:6389")
            .setServers("127.0.0.1:6359,101.13.4.15:6369,101.13.4.15:6379,101.13.4.15:6389")
            .setMaxRedirections(5)
            .setMode(RedisDB.mode_cluster)
            .setConnectionTimeout(10000)
            .setSocketTimeout(10000)
            .setPoolMaxWaitMillis(2000)
            .setPoolMaxTotal(50)
            .setPoolTimeoutRetry(3)
            .setPoolTimeoutRetryInterval(500l)
            .setMaxIdle(-1)
            .setMinIdle(-1)
            .setTestOnBorrow(true)
            .setTestOnReturn(false)
            .setTestWhileIdle(false)
            .setProperties(new LinkedHashMap<>());
      RedisFactory.builRedisDB(redisConfig);

   }
   @Test
   public void test(){
      //使用test数据源操作对应的redis集群
      RedisTool.getInstance("test").set("aaa","ddd");
      RedisTool.getInstance("test").hset("ddd","aaa","xxxx");
      Assert.assertEquals("ddd",RedisTool.getInstance("test").get("aaa"));
   }
}
```

## 6.redis批处理操作

```java
            ClusterPipeline clusterPipeline = null;
//          ClusterPipeline clusterPipeline1 = null;//可以写多个redis集群
            //批量处理
            try {
               clusterPipeline = RedisTool.getInstance().getClusterPipelined();
//             clusterPipeline1 = RedisTool.getInstance("redis1").getClusterPipelined();//可以写多个redis集群

               for (CommonRecord record : datas) {
                  Map<String, Object> data = record.getDatas();
                  String LOG_ID =String.valueOf(data.get("LOG_ID"));
//             logger.info(SimpleStringUtil.object2json(data));
                  String valuedata = SimpleStringUtil.object2json(data);
                  logger.debug("LOG_ID:{}",LOG_ID);
                  clusterPipeline.hset("xingchenma1", LOG_ID, valuedata);
//                clusterPipeline1.hset("xingchenma2", cert_no, valuedata);
               }
               clusterPipeline.sync();

//             clusterPipeline1.sync();//可以写多个redis集群
            }
            finally {
               if(clusterPipeline != null){
                  clusterPipeline.close();
               }

//             if(clusterPipeline1 != null){//可以写多个redis集群
//                clusterPipeline1.close();
//             }
            }
```