### bboss 令牌和凭证redis存储机制配置

bboss 提供了四种令牌和凭证存储机制：

- 内存 不能在集群环境使用，只适用于单机部署应用

- 数据库 可在集群环境使用，同时适用于单机部署应用

- mongodb 可在集群环境使用，同时适用于单机部署应用

- redis 可在集群环境使用，同时适用于单机部署应用

  下面分别介绍四种机制的配置和使用方法，我们只需修改/resources/tokenconf.xml配置文件中的tokenStoreService组件实现类即可，示例如下：

  #### **1.内存方式**

  Xml代码

  ```xml
  <property name="tokenStoreService" class="org.frameworkset.web.token.MemTokenStore">  
          <property name="validateApplication" class="org.frameworkset.web.token.NullValidateApplication"/>  
      </property> 
  ```

  

#### **2.数据库方式**

Xml代码

```xml
<property name="tokenStoreService" class="org.frameworkset.web.token.DBTokenStore">  
        <property name="validateApplication" class="org.frameworkset.web.token.NullValidateApplication"/>  
    </property>  
```

存储令牌和凭证相关的表脚本（oracle和mysql）：[token.sql](https://github.com/bbossgroups/security/blob/master/bboss-security/src-token/org/frameworkset/web/token/token.sql)

#### **3.mongodb方式**

Xml代码

```xml
<property name="tokenStoreService" class="org.frameworkset.web.token.MongodbTokenStore">  
        <property name="validateApplication" class="org.frameworkset.web.token.NullValidateApplication"/>  
    </property>  
```

mongodb服务器配置参考文档章节【6.mongodb客户端配置】：
[bboss session共享使用方法介绍](http://yin-bp.iteye.com/blog/2064662)

#### **4.redis方式**

Xml代码

```xml
<property name="tokenStoreService" class="org.frameworkset.web.token.RedisTokenStore">  
        <property name="validateApplication" class="org.frameworkset.web.token.NullValidateApplication"/>  
    </property>  
```

redis客户端采用jedis，redis配置集群/单机配置参考文档章节【一、redis配置 】：
[bboss session redis插件使用指南](http://yin-bp.iteye.com/blog/2287102)