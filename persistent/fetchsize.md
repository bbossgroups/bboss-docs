### bboss持久层设置数据库查询fetchsize参数方法

jdbc驱动程序api提供了指定了查询语句fetchsize的方法，有些数据库（比如oracle）本身提供了fetchsize的默认值，这样进行大量数据查询时，不会因为返回的结果集太大导致jvm爆掉，有些数据库可能没有默认设置fetchsize，因此需要手动指定。bboss持久层设置数据库查询fetchsize参数方法很简单，只要在poolman.xml文件的datasource中指定一个queryfetchsize参数即可，如果不指定就采用数据库驱动提供的默认值。

设置queryfetchsize的示例：

Xml代码

```xml
<datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>druid_datasource_jndiname</jndiName>  
        <datasourceFile>dbcp.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
        <queryfetchsize>10000</queryfetchsize>    
</datasource>  

```

queryfetchsize为0或小于0，或者不设置就采用数据库驱动提供的fetchsize默认值。

mysql数据库比较特殊，需要在jdbc连接串中添加参数useCursorFetch=true才会起作用，例如：jdbc:mysql://localhost:3306/bboss?useCursorFetch=true

Xml代码

```xml
<property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>  
        <property name="url" value="jdbc:mysql://localhost:3306/bboss?useCursorFetch=true"/>  
        <property name="username" value="root"/>  
        <property name="password" value="123456"/>  
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
        <property name="validationQuery" value="select 1"/>  
        <property name="testOnBorrow" value="true"/>  
</property>  
```

