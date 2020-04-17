### bboss persistent框架数据库连接池配置介

- bboss项目下载列表 在sourceforge访问地址为：

  http://sourceforge.net/projects/bboss/files/

  

-  **概述**
      bboss persistent的配置文件为poolman.xml，位于classes目录下即可

  1.bboss persistent框架支持多个数据源配置

  2.bboss persistent框架默认采用apache common dbcp作为数据库连接池

  3.bboss persistent框架可以引用外部数据源，例如tomcat，weblogic和WebSphere平台提供的datasource 

- **基本配置实例，只配置了一个datasource，如果需要配置多个datasource，只需要按照相应格式再加数据源就可以了：**

< ?xml version="1.0" encoding="gb2312"?>

< poolman>

  < datasource >

​        <!--

​        连接池的逻辑名称，

​        应用程序通过该名称获取对应的数据库连接池的链接

​        持久层接口通过指定该名称在相应的数据库上执行增删改查操作

​         import java.sql.Connection;

​         import java.sql.SQLException;

​         import com.frameworkset.common.poolman.DBUtil;

​         .....
​        

​     Connection con = DBUtil.getConection(“bspf”);

​    -->

< dbname >bspf</ dbname >

​      <!--

数据库链接池启动时是否加载数据库表和视图的元数据 ，应用程序可以调用以下接口获取这些数据：

import com.frameworkset.common.poolman.DBUtil;

import com.frameworkset.common.poolman.sql.TableMetaData;

...

   TableMetaData tableMetaData = DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF");               

​    System.out.println(DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF"));

-->

​    < loadmetadata >false</ loadmetadata >

​        <!--

​    datasource的jndi查找名称，如果指定了jndiName参数

​    bboss persistent框架将链接池绑定为datasource，应用程序可以通过jndi查找获取数据库链接，例如：

​     Context ctx = new InitialContext();

​     DataSource ds =  (DataSource)ctx.lookup("bspf_datasource_jndiname");

​     -->

< jndiName >bspf_datasource_jndiname</ jndiName >

​      <!--

autoprimarykey

​    true表示启用insert自动补全功能，如果在insert sql语句中没有指定主键字段，那么bboss persistent将自动补全该条语句

​    首先分析sql语句得到table name，然后根据该表名到tableinfo表中查找表的主键信息，找到后根据主键配置生成一个主键，补全到sql语句中

​    当执行插入操作的方法时，该主键将作为方法的返回值返回给调用程序。

false表示不自动生成，默认值，这样避免分析sql语句，提高系统性能。

​     -->

< autoprimarykey >false</ autoprimarykey >

​         <!--    

cachequerymetadata 是否缓冲查询语句中查询列表中的字段信息        

true表示缓冲，提高系统新能，一旦系统内存不够时，将自动释放这些缓冲        

false，表示不缓冲        

 -->    

< cachequerymetadata >false</ cachequerymetadata >     

​      <!--    

driver 指定数据库驱动程序         

-->    

< driver >oracle.jdbc.driver.OracleDriver</ driver >     

​           <!--    

url 指定数据库连接串         

-->        

< url >jdbc : oracle : thin : @//localhost : 1521/orcl</ url >         

​         <!--    

username 指定数据库用户        

 -->    

< username >cmsnew</ username >     

​       <!--    

password 指定数据库用户口令         

-->    

< password >cmsnew</ password >    

​          <!--    

txIsolationLevel 指定数据库事务隔离级别         

-->        

< txIsolationLevel >READ_COMMITTED</ txIsolationLevel >         

​            <!--    

initialConnections 指定数据库连接池初始连接数         

-->    

< initialConnections >2</ initialConnections >     

​             <!--    

minimumSize 指定数据库连接池最小连接数        

 -->    

< minimumSize >2</ minimumSize >     

​              <!--    

maximumSize 指定数据库连接池最大连接数         

-->    

< maximumSize >8</ maximumSize >        

​            <!--

控制connection达到maximumSize是否允许再创建新的connection                

true：允许，缺省值                

false：不允许

-->    

< maximumSoft >false</ maximumSoft >

​          <!--

是否检测超时链接（事务超时链接），当数据库连接资源不够时，才会检测，否则不检测

true-检测，如果检测到有事务超时的链接，系统将强制回收（释放）该链接

false-不检测，默认值

 -->

< removeAbandoned >true</ removeAbandoned >

​            <!--

​            链接使用超时时间（事务超时时间）

​            单位：秒

​    -->

< userTimeout >120</ userTimeout >

​             <!--

​        系统强制回收链接时，是否输出后台日志

​        true-输出，默认值

​        false-不输出

 -->

< logAbandoned >true</ logAbandoned >

​           <!--

​        数据库会话是否是readonly，缺省为false

 -->

< readOnly >true</ readOnly >

​               <!--

​            对应属性：timeBetweenEvictionRunsMillis

​            the amount of time (in seconds) to sleep between examining idle objects for eviction

​            空闲链接超时检测处理启动间隔时间

​            单位：秒

​    -->

​    < skimmerFrequency >864000</ skimmerFrequency >

​           <!--

对应于minEvictableIdleTimeMillis 属性：

​    minEvictableIdleTimeMillis the minimum number of milliseconds
​    an object can sit idle in the pool before it is eligable for evcition

​    单位：秒

​    空闲链接回收时间，空闲时间超过指定的值时，将被回收

​    -->

​        < connectionTimeout >12000000</ connectionTimeout >

​                <!--

​        numTestsPerEvictionRun

​        the number of idle objects to

​        examine per run within the idle object eviction thread (if any)

​        每次检测时，检测的空闲链接个数 ，为零时检测所有的空闲链接

​        -->

​    < shrinkBy >4</ shrinkBy >

​                 <!--

​    /**

​     \* 检测空闲链接处理时，是否对空闲链接进行有效性检查控制开关

​     \* true-检查，都检查到有无效链接时，直接销毁无效链接

​     \* false-不检查，缺省值

​     */

​     -->

​    < testWhileidle >false</ testWhileidle >


​          <!--

​       （1） 在tableinfo表中配置了sequence时，以下机制不起作用，

​        定义数据库主键生成机制

​        缺省的采用系统自带的主键生成机制，

​        外步程序可以覆盖系统主键生成机制

​        由值来决定

​        auto:自动，一般在生产环境下采用该种模式，解决了单个应用并发访问数据库添加记录产生冲突的问题，效率高，如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式

​        composite：结合自动和实时从数据库中获取最大的主键值两种方式来处理，开发环境下建议采用该种模式，
​                   解决了多个应用同时访问数据库添加记录时产生冲突的问题，效率相对较低， 如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式

​      （2）通过以下方法获取主键

   DBUtil中的下述方法获取主键

​       DBUtil.getNextPrimaryKey(Connection, tablename)

​       DBUtil.getNextPrimaryKey(Connection, dbname, tablename)

​       DBUtil.getNextPrimaryKey(tablename)

​       DBUtil.getNextPrimaryKey(dbname, tablename)

​       DBUtil.getNextStringPrimaryKey(Connection, tablename)

​       DBUtil.getNextStringPrimaryKey(Connection, dbname, tablename)

​       DBUtil.getNextStringPrimaryKey(tablename)

​       DBUtil.getNextStringPrimaryKey(dbname, tablename)

​       前提是tablename对应的表的主键信息必须配置在tableinfo表中,tableinfo表的结构为：

 名称                                      是否为空? 类型                              描述

 ----------------------------------------- -------- ----------------------------

 TABLE_NAME                                NOT NULL VARCHAR2(255)          表名称

 TABLE_ID_NAME                                      VARCHAR2(255)          表的主键名称

 TABLE_ID_INCREMENT                                 NUMBER(5)              表主键的自增值

 TABLE_ID_VALUE                                     NUMBER(20)             表主键当前值，已经不使用

 TABLE_ID_GENERATOR                                 VARCHAR2(255)          表主键生成机制，只有TABLE_ID_TYPE为sequence时才需填为相应sequence的名称

 TABLE_ID_TYPE                                      VARCHAR2(255)          表主键的数值类型，int,long,string，sequence，如果为sequence时必须指定TABLE_ID_GENERATOR为对应的sequence的名称

 TABLE_ID_PREFIX                                    VARCHAR2(255)          为表主键添加统一的前缀，默认为null，只有表主键类型为string时才能指定前缀。

​    -->

​    < keygenerate>composite</ keygenerate >

​            <!-- 

请求链接时等待时间，单位：秒

​    客服端程序请求链接等待时间，超过指定值时后台包报待超时异常

​     -->

​    < maxWait >10</ maxWait >

​             <!--

​            链接有效性检查sql语句

​     -->

​    < validationQuery >select 1 from dual</ validationQuery >

​           <!-- 

如果在tableinfo表中配置了表的主键信息，而且主键通过sequence生成，则synsequence元素
        用来控制是否同步sequence的当前值为最大的未使用的表的主键值+1。

​         -->

​    < synsequence >false</ synsequence >

​            <!-- 

每个connection预编译statement池化标记

​    true：池化

​    false：不池化，默认值

​     -->

​    < poolingPreparedStatements >false</ poolingPreparedStatements >

​              <!--

​            每个connection最大打开的预编译statements数-1

​     -->

​    < maxOpenPreparedStatements >-1</ maxOpenPreparedStatements >

​            <!--

​                设置为true，则相关的增删改查，批处理，预编译处理，存储函数调用sql语句将全部被输出的系统后台
​                否则不输出
​     -->

​    < showsql >true</ showsql >

  </ datasource >

</ poolman >

- **外部数据源实例**

  < ?xml version="1.0" encoding="gb2312"? >

< poolman >

< datasource external="true" >

​         <!--

​        连接池的逻辑名称，

​        应用程序通过该名称获取对应的数据库连接池的链接

​        持久层接口通过指定该名称在相应的数据库上执行增删改查操作

​         import java.sql.Connection;

​         import java.sql.SQLException;

​         import com.frameworkset.common.poolman.DBUtil;

​         .....

​         Connection con = DBUtil.getConection(“bspf”);

​        -->

​    < dbname>bspf</ dbname >

​            <!--

​    数据库链接池启动时是否加载数据库表和视图的元数据 ，应用程序可以调用以下接口获取这些数据：

​    import com.frameworkset.common.poolman.DBUtil;

​    import com.frameworkset.common.poolman.sql.TableMetaData;

​    ...

​    TableMetaData tableMetaData = DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF");

​        System.out.println(DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF"));

​    -->

​        < loadmetadata>false</ loadmetadata >

​                 <!--

​        datasource的jndi查找名称，如果指定了jndiName参数

​        bboss persistent框架将链接池绑定为datasource，应用程序可以通过jndi查找获取数据库链接，例如：

​         Context ctx = new InitialContext();

​         DataSource ds =  (DataSource)ctx.lookup("bspf_datasource_jndiname");

​         -->

​    < jndiName >bspf_datasource_jndiname</ jndiName >

​                <!--

​            当dataSource的external属性指定为true时，必须指定外部数据源jndi绑定名称

​            如果系统需要通过内部的jndi查找该数据源，还必须指定jndiName名称，比如系统管理中hibernate

​            必需通过内部jndi访问数据源来获取数据库的链接，以及应用hibernate产生自定义数据库主键机制

​    -->

​    < externaljndiName>external_bspf_datasource_jndiname</ externaljndiName >

​             <!--

​    autoprimarykey

​        true表示启用insert自动补全功能，如果在insert sql语句中没有指定主键字段，那么bboss persistent将自动补全该条语句

​        首先分析sql语句得到table name，然后根据该表名到tableinfo表中查找表的主键信息，找到后根据主键配置生成一个主键，补全到sql语句中

​        当执行插入操作的方法时，该主键将作为方法的返回值返回给调用程序。

​    false表示不自动生成，默认值，这样避免分析sql语句，提高系统性能。

​         -->

​    < autoprimarykey >false</ autoprimarykey >

​                <!--

​    cachequerymetadata 是否缓冲查询语句中查询列表中的字段信息

​        true表示缓冲，提高系统新能，一旦系统内存不够时，将自动释放这些缓冲

​        false，表示不缓冲

​         -->

​    < cachequerymetadata >false</ cachequerymetadata >

​                <!--

​    driver 指定数据库驱动程序，外部数据源需要指定数据库的驱动程序是为了识别不同类型的数据库

​         -->

​    < driver >oracle.jdbc.driver.OracleDriver</ driver >

​                <!--
​       （1） 在tableinfo表中配置了sequence时，以下机制不起作用，

​        定义数据库主键生成机制

​        缺省的采用系统自带的主键生成机制，

​        外步程序可以覆盖系统主键生成机制

​        由值来决定

​        auto:自动，一般在生产环境下采用该种模式，解决了单个应用并发访问数据库添加记录产生冲突的问题，效率高，如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式

​        composite：结合自动和实时从数据库中获取最大的主键值两种方式来处理，开发环境下建议采用该种模式，
​                   解决了多个应用同时访问数据库添加记录时产生冲突的问题，效率相对较低， 如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式

​      （2）通过以下方法获取主键

   DBUtil中的下述方法获取主键

​       DBUtil.getNextPrimaryKey(Connection, tablename)

​       DBUtil.getNextPrimaryKey(Connection, dbname, tablename)

​       DBUtil.getNextPrimaryKey(tablename)

​       DBUtil.getNextPrimaryKey(dbname, tablename)

​       DBUtil.getNextStringPrimaryKey(Connection, tablename)

​       DBUtil.getNextStringPrimaryKey(Connection, dbname, tablename)

​       DBUtil.getNextStringPrimaryKey(tablename)

​       DBUtil.getNextStringPrimaryKey(dbname, tablename)

​       前提是tablename对应的表的主键信息必须配置在tableinfo表中,tableinfo表的结构为：

​       前提是tablename对应的表的主键信息必须配置在tableinfo表中,tableinfo表的结构为：

 名称                                      是否为空? 类型                              描述

 ----------------------------------------- -------- ----------------------------

 TABLE_NAME                                NOT NULL VARCHAR2(255)          表名称

 TABLE_ID_NAME                                      VARCHAR2(255)          表的主键名称

 TABLE_ID_INCREMENT                                 NUMBER(5)              表主键的自增值

 TABLE_ID_VALUE                                     NUMBER(20)             表主键当前值，已经不使用

 TABLE_ID_GENERATOR                                 VARCHAR2(255)          表主键生成机制，只有TABLE_ID_TYPE为sequence时才需填为相应sequence的名称

 TABLE_ID_TYPE                                      VARCHAR2(255)          表主键的数值类型，int,long,string，sequence，如果为sequence时必须指定TABLE_ID_GENERATOR为对应的sequence的名称

 TABLE_ID_PREFIX                                    VARCHAR2(255)          为表主键添加统一的前缀，默认为null，只有表主键类型为string时才能指定前缀。

​    -->

​    < keygenerate >composite</ keygenerate >            

​                   <!-- 

如果在tableinfo表中配置了表的主键信息，而且主键通过sequence生成，则synsequence元素用来控制是否同步sequence的当前值为最大的未使用的表的主键值+1。

​         -->

​    < synsequence >false</ synsequence >   

​             <!--

​             设置为true，则相关的增删改查，批处理，预编译处理，存储函数调用sql语句将全部被输出的系统后台
​                否则不输出

​     -->

​    < showsql >true</ showsql >

  </ datasource >

</ poolman >