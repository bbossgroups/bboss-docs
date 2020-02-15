### bboss 持久层配置apache dbcp，proxool,c3p0,Druid等数据源方法

bboss 持久层默认内置了apache dbcp（内置版本commons-pool2-2.3,commons-dbcp2-2.0.1）数据源，除此之外还可以非常简单和轻松地使用其他开源的数据源，这里以下面4种数据源为例进行说明（其他的数据源也可以参考其中的方法自己配置）：

apache dbcp（如果你觉得内置的版本不可靠，那么可以自己配置喜欢的dbcp版本）

proxool

c3p0

Druid

**1.概述**

对于内置的dbcp的配置方法参考文档：[bbossgroups持久层框架数据源配置文件实例](http://yin-bp.iteye.com/blog/1112892)，这里不过多的说明。本文详细介绍上述开源数据源的配置方法。

为了支持这些数据源，poolman.xml文件中在datasource元素新增了一个datasourceFile子元素，用来指定第三方数据源的[bboss ioc配置文件](http://yin-bp.iteye.com/blog/1434626)地址（相对于classpath路径），例如：
**<datasourceFile**>druid.xm**<l/datasourceFile**> 

在这里druid.xml文件位于classes的根路径下，如果放在某个包路径下则需要带上包路径，例如：

com/frameworkset/datasource/conf/druid.xml

**2.引用其他数据源的poolman.xml文件示例** 

Xml代码 

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!-- bboss 持久层框架中配置c3p0,dbcp,proxool,druid等第三方数据源的配置文件示例 -->  
<poolman>  
    <datasource>  
  
        <dbname>c3p0</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>c3p0_datasource_jndiname</jndiName>  
        <datasourceFile>c3p0.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>false</showsql>  
        <keygenerate>composite</keygenerate>  
  
    </datasource>  
  
    <datasource>  
  
        <dbname>dbcp</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>dbcp_datasource_jndiname_1</jndiName>  
        <datasourceFile>dbcp.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>false</showsql>  
        <keygenerate>composite</keygenerate>  
    </datasource>  
    <datasource>  
  
        <dbname>proxool</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>proxool_datasource_jndiname</jndiName>  
        <datasourceFile>proxool.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>false</showsql>  
        <keygenerate>composite</keygenerate>  
    </datasource>  
  
    <datasource>  
  
        <dbname>druid</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>druid_datasource_jndiname</jndiName>  
        <datasourceFile>druid.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>false</showsql>  
        <keygenerate>composite</keygenerate>  
    </datasource>  
  
</poolman>  
```

每个datasource的dbname属性是可以根据需要自己进行命名，对应于[持久层组件](http://yin-bp.iteye.com/blog/1112997)的方法中的dbname属性，以便在相应的数据源上完成db操作。接下来看看每种数据源的定义示例

**3.基于bboss ioc管理的第三方数据源配置文件示例**

c3p0数据源-c3p0.xml

Xml代码

```xml
<property name="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">  
  <!-- 指定连接数据库的JDBC驱动 -->  
  <property name="driverClass" value="oracle.jdbc.driver.OracleDriver"/>    
  <!-- 连接数据库所用的URL -->  
  <property name="jdbcUrl"  
   value="jdbc:oracle:thin:@//10.0.15.134:1521/orcl">  
   <!-- 如果数据库url是加密的，则需要配置解密的编辑器 -->  
    <!--<editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
   </property>    
  <!-- 连接数据库的用户名 -->  
  <property name="user" value="GCMP">  
    <!-- 如果账号是加密的账号，则需要配置解密的编辑器 -->  
<!--     <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
  </property>    
  <!-- 连接数据库的密码 -->  
  <property name="password" value="GCMP">  
  <!-- 如果口令是加密的口令，则需要配置解密的编辑器 -->  
<!--     <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
  </property>  
    
  <!-- 设置数据库连接池的最大连接数 -->  
  <property name="maxPoolSize" value="20"/>    
  <!-- 设置数据库连接池的最小连接数 -->  
  <property name="minPoolSize" value="2"/>  
    
  <!-- 设置数据库连接池的初始化连接数 -->  
  <property name="initialPoolSize" value="2"/>  
  <!-- 设置数据库连接池的连接的最大空闲时间,单位为秒 -->  
  <property name="maxIdleTime" value="20"/>    
  <property name="preferredTestQuery" value="select 1 from dual"/>  
    
</property>  
```

**dbcp数据源-dbcp.xml**

Xml代码

```xml
<property name="datasource" class="org.apache.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="com.mysql.jdbc.Driver" />  
    <property name="url"><![CDATA[jdbc:mysql://localhost:3306/testdb?useUnicode=true&characterEncoding=utf-8&autoReconnect=true]]></property>  
    <property name="username" value="root" />  
    <property name="password" value="123456" />  
    <!--initialSize: 初始化连接 -->  
    <property name="initialSize" value="5" />  
    <!--maxIdle: 最大空闲连接 -->  
    <property name="maxIdle" value="20" />  
    <!--minIdle: 最小空闲连接 -->  
    <property name="minIdle" value="5" />  
    <!--maxActive: 最大连接数量 -->  
    <property name="maxTotal" value="15" />  
    <!--removeAbandoned: 是否自动回收超时连接 -->  
    <property name="removeAbandonedOnBorrow" value="true" />  
    <!--removeAbandonedTimeout: 超时时间(以秒数为单位) -->  
    <property name="removeAbandonedTimeout" value="180" />  
    <!--maxWait: 超时等待时间以毫秒为单位 6000毫秒/1000等于6秒 -->  
    <property name="maxWaitMillis" value="3000" />  
    <property name="validationQuery" value="SELECT 1 " />  
    <property name="testOnBorrow" value="true" />  
</property>  
```

**proxool数据源-proxool.xml**

Xml代码 

```xml
<property name="datasource" class="org.logicalcobwebs.proxool.ProxoolDataSource">  
  <!-- 指定连接数据库的JDBC驱动 -->  
  <property name="driver" value="oracle.jdbc.driver.OracleDriver"/>    
  <!-- 连接数据库所用的URL -->  
  <property name="driverUrl"  
   value="jdbc:oracle:thin:@//10.0.15.134:1521/orcl">  
   <!-- 如果数据库url是加密的，则需要配置解密的编辑器 -->  
    <!--<editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
   </property>    
  <!-- 连接数据库的用户名 -->  
  <property name="user" value="GCMP">  
    <!-- 如果账号是加密的账号，则需要配置解密的编辑器 -->  
<!--     <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
  </property>    
  <!-- 连接数据库的密码 -->  
  <property name="password" value="GCMP">  
  <!-- 如果口令是加密的口令，则需要配置解密的编辑器 -->  
<!--     <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
  </property>  
  
  
    <property name="alias" value="Pool_dbname" />  
    <property name="houseKeepingSleepTime" value="90000" />  
    <property name="prototypeCount" value="0" />  
    <property name="maximumConnectionCount" value="50" />  
    <property name="minimumConnectionCount" value="2" />  
    <property name="simultaneousBuildThrottle" value="50" />  
    <property name="maximumConnectionLifetime" value="14400000" />  
    <property name="houseKeepingTestSql" value="select 1 from dual1" />  
</property>  
```

**国产数据源druid-druid.xml**

Xml代码

```xml
<property id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"  
    init-method="init"><!-- 这里不需要配置destroy-method，因为bboss持久层在jvm退出时会自动调用数据源的close方法 -->  
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver" />  
    <property name="url" value="jdbc:oracle:thin:@//10.0.15.134:1521/orcl">  
        <!-- 如果数据库url是加密的，则需要配置解密的编辑器 -->  
        <!--<editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
    </property>  
    <!-- 连接数据库的用户名 -->  
    <property name="username" value="GCMP">  
        <!-- 如果账号是加密的账号，则需要配置解密的编辑器 -->  
        <!-- <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
    </property>  
    <!-- 连接数据库的密码 -->  
    <property name="password" value="GCMP">  
        <!-- 如果口令是加密的口令，则需要配置解密的编辑器 -->  
        <!-- <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
    </property>  
    <property name="filters" value="stat" />  
    <property name="maxActive" value="20" />  
    <property name="initialSize" value="1" />  
    <property name="maxWait" value="60000" />  
    <property name="minIdle" value="1" />  
    <property name="timeBetweenEvictionRunsMillis" value="3000" />  
    <property name="minEvictableIdleTimeMillis" value="300000" />  
    <property name="validationQuery" value="SELECT 1 from dual" />  
    <property name="testWhileIdle" value="true" />  
    <property name="testOnBorrow" value="false" />  
    <property name="testOnReturn" value="false" />  
    <property name="poolPreparedStatements" value="true" />  
    <property name="maxPoolPreparedStatementPerConnectionSize"  
        value="20" />  
</property>  
```

**bboss整合版-dbcp2(兼容jdk6，可监控性更好，dbcp2官方不兼容jdk6)**

Xml代码

```xml
<property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver"/>    
    <property name="url" value="jdbc:jtds:sqlserver://localhost:1433/sanyleasing"/>    
    <property name="username" value="pms"/>    
    <property name="password" value="pms"/>    
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
    <property name="validationQuery" value="SELECT 1 "/>      
    <property name="testOnBorrow" value="true"/>   
</property>  
```

**4.安全特性**

特别需要注意的是：在c3p0数据源和国产数据源druid的配置文件（其他的数据源配置文件中也可以参考添加）中对于数据url，数据库账号，数据库口令几个元素的定义中都有个注释掉的子元素editor:

Xml代码

```xml
<!-- 如果数据库url是加密的，则需要配置解密的编辑器 -->  
        <!--<editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> --> 
```

editor元素的作用就是，如果相应的值是一个加密的值，则需要通过editor元素配置一个解密的插件（参考这个示例放开注释即可），以遍ioc框架将解密后的值注入到对于的数据源对象中。bboss持久层提供了一个解密插件：

com.frameworkset.common.poolman.security.DecryptEditor

DecryptEditor实现了bboss ioc的com.frameworkset.util.EditorInf接口，代码如下：

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
package com.frameworkset.common.poolman.security;  
  
import com.frameworkset.util.EditorInf;  
  
/** 
 *  
 * <p>Title: DecryptEditor.java</p> 
 * 
 * <p>Description: 对信息进行解密的属性编辑器，主要用户对于连接池账号信息进行加密的相关操作</p> 
 * 
 * <p>Copyright: Copyright (c) 2007</p> 
 * @Date 2012-7-31 上午11:15:40 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class DecryptEditor implements EditorInf {  
  
    public Object getValueFromObject(Object fromValue) {  
        return getValueFromString((String )fromValue) ;  
    }  
  
    public Object getValueFromString(String fromValue) {  
        try {  
            return new DESCipher().decrypt((String)fromValue);  
        } catch (Exception e) {  
            return fromValue;  
        }  
    }  
  
}  
```

采用这个解密插件时，对应的信息加密方法如下：

Java代码

```java
com.frameworkset.common.poolman.security.DESCipher aa = new com.frameworkset.common.poolman.security.DESCipher();  
        String bb = aa.encrypt("123456");  
        System.out.println(bb);  
        System.out.println(aa.decrypt(bb));  
          
        bb = aa.encrypt("root");  
        System.out.println("user:"+bb);  
        System.out.println(aa.decrypt(bb));  
          
        bb = aa.encrypt("jdbc:mysql://localhost:3306/cim");  
        System.out.println("url:"+bb);  
        System.out.println(aa.decrypt(bb));  
```

**常用的数据库驱动配置**

**postgresql**

`<property name="driverClassName" value="org.postgresql.Driver"/>` 

`<property name="url" value="jdbc:postgresql://127.0.0.1:5432/test"/>` 

`<property name="username" value="beigang"/>` 

`<property name="password" value="beigang"/>`

**oracle**

   `<property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/>` 

`<property name="url" value="jdbc:oracle:thin:@//10.0.15.134:1521/orcl"/>` 

`<property name="username" value="GCMP"/>` 

`<property name="password" value="GCMP"/>`    

  **sqlserver**

`<property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver"/>` 

`<property name="url" value="jdbc:jtds:sqlserver://localhost:1433/sanyleasing"/>` 

`<property name="username" value="pms"/>` 

`<property name="password" value="pms"/>` 

**mysql**

`<property name="driverClassName" value="com.mysql.jdbc.Driver"/>`     

​     `<property name="url">`<![CDATA[jdbc:mysql://localhost:3306/bboss?useUnicode=true&characterEncoding=utf-8]]>**</property**>

`<property name="username" value="root"/>`

`<property name="password" value="123456"/>` 

  **derby**

`<property name="driverClassName" value="org.apache.derby.jdbc.EmbeddedDriver"/>` 

`<property name="url" value="jdbc:derby:E:/workspace/bbossgroups/bboss-document/database/cimdb"/>`

`<property name="username" value=""/>` 

`<property name="password" value=""/>`   

**sqlite**

`<property name="driverClassName" value="org.sqlite.JDBC"/>` 

`<property name="url" value="jdbc:sqlite:///home/data"/>`

`<property name="username" value="root"/>` 

`<property name="password" value="root"/>`



