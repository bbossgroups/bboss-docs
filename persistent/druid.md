### bboss内置数据源apache dbcp与druid数据源切换方法

bboss内置数据源内置数据源为apache dbcp,也可以配置到阿里巴巴开源的druid数据源，本文介绍他们之间如何切换：

**内置数据源dbcp配置**

bboss集成了apache dbcp2连接池，并做了jdk1.6兼容性改造，dbcp2官方要求jdk 7+。

**dbcp在bboss中老的配置方式（不建议）**：

首先在poolman.xml中配置了内置dbcp数据源：

Xml代码

```xml
<datasource>  
  
   <dbname>bspf</dbname>  
<loadmetadata>false</loadmetadata>  
   <jndiName>ds0_datasource_jndiname</jndiName>  
    <autoprimarykey>false</autoprimarykey>  
    <cachequerymetadata>false</cachequerymetadata>  
   <driver>oracle.jdbc.driver.OracleDriver</driver>  
  
    <url>jdbc:oracle:thin:@//10.0.15.51:1521/orcl</url>   
   <username>testpdp1</username>  
   <password>testpdp1</password>  
  
   <txIsolationLevel>READ_COMMITTED</txIsolationLevel>  
  
   <nativeResults>true</nativeResults>  
  
   <poolPreparedStatements>false</poolPreparedStatements>  
  
   <initialConnections>2</initialConnections>  
     
   <minimumSize>50</minimumSize>  
   <maximumSize>50</maximumSize>  
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
   <logAbandoned>false</logAbandoned>  
     
   <!--  
    数据库会话是否是readonly，缺省为false 
    -->  
   <readOnly>true</readOnly>  
  
<!--  
    对应属性：timeBetweenEvictionRunsMillis  
    the amount of time (in milliseconds) to sleep between examining idle objects for eviction   
-->  
<skimmerFrequency>120000</skimmerFrequency>  
<!--对应于minEvictableIdleTimeMillis 属性：  
minEvictableIdleTimeMillis the minimum number of milliseconds   
an object can sit idle in the pool before it is eligable for evcition  
单位：秒  
  
空闲链接回收时间，空闲时间超过指定的值时，将被回收  
-->  
<connectionTimeout>240000</connectionTimeout>  
<!--  
numTestsPerEvictionRun   
the number of idle objects to   
examine per run within the idle object eviction thread (if any)  
  
每次回收的链接个数   
-->  
   <shrinkBy>5</shrinkBy>  
   <testWhileidle>true</testWhileidle>  
   <keygenerate>composite</keygenerate>  
   <debugging>true</debugging>-->  
   <!-- 请求链接时等待时间，单位：秒  
   客服端程序请求链接等待时间超过指定值时，后台包等待超时异常  
    -->  
   <maxWait>60</maxWait>  
   <validationQuery>select 1 from dual</validationQuery>  
<RETURN_GENERATED_KEYS>false</RETURN_GENERATED_KEYS>  
 </datasource>  
```

**bboss配置dbcp建议方法：**

在classes类路径根目录下准备好dbcp.xml配置文件（基于bboss ioc语法）

Xml代码

```xml
property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/>    
    <property name="url" value="jdbc:oracle:thin:@//10.0.15.51:1521/orcl"/>    
    <property name="username" value="sanyfms"/>    
    <property name="password" value="sanyfms"/>    
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

然后将poolman.xml的内容修改为：

Xml代码

```xml
<datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>ds0_datasource_jndiname</jndiName>  
        <datasourceFile>dbcp.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
                <RETURN_GENERATED_KEYS>false</RETURN_GENERATED_KEYS>  
    </datasource>  
```

**druid数据源配置方法**

如果需要切换为druid数据源，则需要编写druid.xml文件,内容为：

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
    <property name="username" value="SANYGCMP">  
        <!-- 如果账号是加密的账号，则需要配置解密的编辑器 -->  
        <!-- <editor clazz="com.frameworkset.common.poolman.security.DecryptEditor"/> -->  
    </property>  
    <!-- 连接数据库的密码 -->  
    <property name="password" value="SANYGCMP">  
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
    <property name="testOnBorrow" value="true" />  
    <property name="testOnReturn" value="false" />  
    <property name="poolPreparedStatements" value="true" />  
    <property name="maxPoolPreparedStatementPerConnectionSize"  
        value="20" />  
</property>  
```

然后再修改poolman.xml:

Xml代码

```xml
<datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>ds0_datasource_jndiname</jndiName>  
        <datasourceFile>druid.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
                <RETURN_GENERATED_KEYS>true</RETURN_GENERATED_KEYS>  
    </datasource>  
```

同样可以反过来将druid切换为apache dbcp数据源。druid的特点就是监控功能做的比较好，如果只需要一个连接池的话，建议采用apache dbcp数据源（比较成熟一点）![img](https://www.iteye.com/images/smiles/icon_lol.gif)

