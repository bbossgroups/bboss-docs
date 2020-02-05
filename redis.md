### bboss redis组件使用实例

**在工程中导入bboss redis组件**

gradle

Java代码

```java
compile 'com.bbossgroups:bboss-data:5.2.8' 
```

maven
Xml代码

```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>5.1.2</version>  
</dependency>  
```

bboss redis操作组件使用代码：
Java代码

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
        RedisHelper redisHelper = null;  
        try  
        {  
            redisHelper = RedisFactory.getRedisHelper();  
            redisHelper.set("test", "value1");  
            String value = redisHelper.get("test");  
            System.out.println("test="+value);  
            redisHelper.setex("foo", 1,"fasdfasf");//指定缓存有效期1秒  
              
            System.out.println("foo ttl="+redisHelper.ttl("foo"));//获取有效期  
            value = redisHelper.get("foo");//获取数据  
            System.out.println("foo="+value);  
            //删除数据  
            redisHelper.del("foo");  
            value = redisHelper.getSet("fowwero","test");  
              
            System.out.println("fowwero="+value);  
            value = redisHelper.getSet("fowwero","eeee");//获取后修改数据  
              
            System.out.println("fowwero="+value);  
              
            value = redisHelper.get("fowwero");  
              
            System.out.println("fowwero="+value);  
        }  
        finally  
        {  
            if(redisHelper != null)  
                redisHelper.release();  
        }  
    }  
       
  
}  
```

**配置redis服务器**

**redis集群配置**

修改resources/redis.xml文件，设置redis的服务器地址和端口

Xml代码

```xml
<properties>  
      
    <property name="default" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            127.0.0.1:6379  
                        127.0.0.1:6380  
        </property>  
        <!-- single|cluster|shared -->  
        <property name="mode" value="cluster" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
       
</properties>  
```

**redis单节点配置**

修改resources/redis.xml文件，设置redis的服务器地址和端口

Xml代码

```xml
<properties>  
      
    <property name="default" class="org.frameworkset.nosql.redis.RedisDB">  
        <property name="servers">  
            127.0.0.1:6379  
        </property>  
        <!-- single|cluster|shared -->  
        <property name="mode" value="single" />  
              
        <property name="auth" value="123456" />  
        <property name="poolMaxTotal" value="10"/>      
        <property name="poolMaxWaitMillis" value="2000"/>   
           
    </property>  
       
</properties>x
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