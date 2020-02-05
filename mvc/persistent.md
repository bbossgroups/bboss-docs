### bboss 持久层数据源引用外部属性配置介绍

bboss 持久层数据源外部属性配置引用介绍
bboss持久层的数据源配置文件中可以引用外部配置文件中定义的属性，本文举

```java
jdbc.driver=com.mysql.jdbc.Driver  
jdbc.url=jdbc:mysql://192.168.137.1:3306/apm?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true  
jdbc.username=root  
jdbc.password=123456  
jdbc.testSql=select 1  
```


在数据源配置文件中导入jdbc.properties文件，并引用其中的属性:

```xml
<properties>  
    <config file="jdbc.properties"/>  
    <property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
        <property name="driverClassName" value="${jdbc.driver}"/>  
        <property name="url" value="${jdbc.url}"/>  
  
        <property name="username" value="${jdbc.username}"/>  
        <property name="password" value="${jdbc.password}"/>  
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
        <property name="validationQuery" value="${jdbc.testSql}"/>  
        <property name="testOnBorrow" value="true"/>  
    </property>  
</properties>  
```
通过config元素导入外部属性文件，通过${}语法引用属性文件中定义的属性。

