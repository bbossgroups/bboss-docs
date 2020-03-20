### bbossgroups持久层框架数据源配置文件实例

bbossgroups持久层框架数据源配置文件实例，本配置包含了物理数据源stsmc的配置实例：

Xml代码

```xml
<?xml version="1.0" encoding="utf-8"?>  
  
<poolman>  
  
<datasource>  
  
    <dbname>stsmc</dbname>  
    <loadmetadata>false</loadmetadata>  
    <jndiName>jdbc/mysql-ds</jndiName>  
    <driver>com.mysql.jdbc.Driver</driver>  
  
     <url>jdbc:mysql://172.16.33.46:3306/etl</url>   
  
    <username>root</username>  
    <password>123456</password>  
  
    <txIsolationLevel>READ_COMMITTED</txIsolationLevel>  
  
    <nativeResults>true</nativeResults>  
  
    <poolPreparedStatements>false</poolPreparedStatements>  
  
    <initialConnections>2</initialConnections>  
      
    <minimumSize>2</minimumSize>  
    <maximumSize>10</maximumSize>  
    <!--控制connection达到maximumSize是否允许再创建新的connection  
        true：允许，缺省值  
        false：不允许-->  
    <maximumSoft>false</maximumSoft>  
      
    <!--   
    是否检测超时链接（事务超时链接）  
    true-检测，如果检测到有事务超时的链接，系统将强制回收（释放）该链接  
    false-不检测，默认值  
     -->  
    <removeAbandoned>false</removeAbandoned>  
    <!--  
        链接使用超时时间（事务超时时间）  
        单位：秒  
    -->  
    <userTimeout>50</userTimeout>  
    <!--   
        系统强制回收链接时，是否输出后台日志  
        true-输出，默认值  
        false-不输出  
     -->  
    <logAbandoned>true</logAbandoned>  
      
    <!--  
        数据库会话是否是readonly，缺省为false 
     -->  
    <readOnly>false</readOnly>  
      
    <!--  
        对应属性：timeBetweenEvictionRunsMillis  
        the amount of time (in milliseconds) to sleep between examining idle objects for eviction   
    -->  
    <skimmerFrequency>1200000</skimmerFrequency>  
    <!--对应于minEvictableIdleTimeMillis 属性：  
    minEvictableIdleTimeMillis the minimum number of milliseconds   
    an object can sit idle in the pool before it is eligable for evcition  
    单位：秒  
      
    空闲链接回收时间，空闲时间超过指定的值时，将被回收  
    -->  
    <connectionTimeout>2400000</connectionTimeout>  
    <!--  
    numTestsPerEvictionRun   
    the number of idle objects to   
    examine per run within the idle object eviction thread (if any)  
      
    每次回收的链接个数   
    -->  
    <shrinkBy>5</shrinkBy>  
    <!--  
    /**  
     * 检测空闲链接处理时，是否对空闲链接进行有效性检查控制开关  
     * true-检查，都检查到有无效链接时，直接销毁无效链接  
     * false-不检查，缺省值  
     */  
     -->  
    <testWhileidle>true</testWhileidle>  
  
  
 、  
    <!-- 请求链接时等待时间，单位：秒  
    客服端程序请求链接等待时间超过指定值时，后台包等待超时异常  
     -->  
    <maxWait>60</maxWait>  
      
    <!--  
        链接有效性检查sql语句 
     -->  
    <validationQuery>select 1</validationQuery>  
      
    <autoprimarykey>false</autoprimarykey>  
    <showsql>false</showsql>  
      
  
  </datasource>  
  
</poolman>  
```

bboss持久层框架外部数据源配置方法请参考文章《[关于bboss-persistent持久层框架通过jndi引用外部数据源（datasource）](http://yin-bp.iteye.com/blog/352924)


  