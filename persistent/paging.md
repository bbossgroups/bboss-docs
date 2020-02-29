### 基于bbossgroups持久层框架实现数据库分页查询

bbossgroups中提供的分页查询方法，非常简单，也非常的高效,本文介绍一个基于行处理器的PreparedDBUtil的案例。

Java代码

```java
PreparedDBUtil dbUtil = new PreparedDBUtil();  
        try {  
            dbUtil.preparedSelect("select * from testnewface where object_id < ?",0,10);//分页查询方法  
            dbUtil.setInt(1, 100);  
//下面使用到了行处理器，如果TestNewface对象中的属性名称和查询表字段的名称一致  
//（忽略大小写），则不需要行处理器，直接执行查询即可：  
//List list = dbUtil.executePreparedForList(TestNewface.class);  
            List list = dbUtil.executePreparedForList(TestNewface.class,new RowHandler<TestNewface>()  
            {  
  
                public void handleRow(TestNewface t, Record record) {  
                      
                    try {  
                        t.setCREATED(record.getDate("created"));  
                        t.setDATA_OBJECT_ID(record.getInt("DATA_OBJECT_ID"));  
                        //........设置其他的属性  
                    } catch (SQLException e) {  
                        // TODO Auto-generated catch block  
                        e.printStackTrace();  
                    }  
                    System.out.println("row handler:"+t);  
                      
                }  
                  
            });  
            int totalsize = dbUtil.getTotalSize();//获得总记录数  
            for(int i = 0; i < list.size()/*list.size()当页记录数*/; i ++)//遍历当页记录  
            {  
                TestNewface testNewface = (TestNewface)list.get(i);  
                ... ...  
            }  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
```

java值对象：

Java代码

```java
package com.frameworkset.common;  
  
import java.util.Date;  
  
/** 
 *  
 * <p>Title: TestNewface.java</p> 
 * 
 * <p>Description: </p> 
 * 
 * <p>Copyright: Copyright (c) 2007</p> 
 * @Date Nov 4, 2008 2:51:17 PM 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestNewface {  
    String OWNER          ;  
    String  OBJECT_NAME    ;  
    String   SUBOBJECT_NAME   ;  
    int   OBJECT_ID     ;  
    int  DATA_OBJECT_ID  ;  
    String   OBJECT_TYPE     ;  
    Date  CREATED          ;  
    Date LAST_DDL_TIME    ;  
    String  TIMESTAMP        ;  
    String  STATUS         ;  
    String  TEMPORARY      ;  
    String   GENERATED     ;  
    String   SECONDARY    ;  
    public String getOWNER() {  
        return OWNER;  
    }  
    public void setOWNER(String owner) {  
        OWNER = owner;  
    }  
    public String getOBJECT_NAME() {  
        return OBJECT_NAME;  
    }  
    public void setOBJECT_NAME(String object_name) {  
        OBJECT_NAME = object_name;  
    }  
    public String getSUBOBJECT_NAME() {  
        return SUBOBJECT_NAME;  
    }  
    public void setSUBOBJECT_NAME(String subobject_name) {  
        SUBOBJECT_NAME = subobject_name;  
    }  
    public int getOBJECT_ID() {  
        return OBJECT_ID;  
    }  
    public void setOBJECT_ID(int object_id) {  
        OBJECT_ID = object_id;  
    }  
    public int getDATA_OBJECT_ID() {  
        return DATA_OBJECT_ID;  
    }  
    public void setDATA_OBJECT_ID(int data_object_id) {  
        DATA_OBJECT_ID = data_object_id;  
    }  
    public String getOBJECT_TYPE() {  
        return OBJECT_TYPE;  
    }  
    public void setOBJECT_TYPE(String object_type) {  
        OBJECT_TYPE = object_type;  
    }  
    public Date getCREATED() {  
        return CREATED;  
    }  
    public void setCREATED(Date created) {  
        CREATED = created;  
    }  
    public Date getLAST_DDL_TIME() {  
        return LAST_DDL_TIME;  
    }  
    public void setLAST_DDL_TIME(Date last_ddl_time) {  
        LAST_DDL_TIME = last_ddl_time;  
    }  
    public String getTIMESTAMP() {  
        return TIMESTAMP;  
    }  
    public void setTIMESTAMP(String timestamp) {  
        TIMESTAMP = timestamp;  
    }  
    public String getSTATUS() {  
        return STATUS;  
    }  
    public void setSTATUS(String status) {  
        STATUS = status;  
    }  
    public String getTEMPORARY() {  
        return TEMPORARY;  
    }  
    public void setTEMPORARY(String temporary) {  
        TEMPORARY = temporary;  
    }  
    public String getGENERATED() {  
        return GENERATED;  
    }  
    public void setGENERATED(String generated) {  
        GENERATED = generated;  
    }  
    public String getSECONDARY() {  
        return SECONDARY;  
    }  
    public void setSECONDARY(String secondary) {  
        SECONDARY = secondary;  
    }  
}  
```

在bbossgroups持久层框架中，主要是通过数据库适配器来实现不同数据库产品的分页查询算法，框架缺省为每种数据库提供分页算法实现（基本上使用每种数据库的最优算法），用户可以扩展这些适配器来实现自己的分页算法,以oracle为列，编写自己的适配器覆盖默认的public String getDBPagineSql(String sql, long offset, int maxsize)方法：

Java代码

```java
package com.frameworkset.orm.adaptors;   
  
import com.frameworkset.orm.adapter.DBOracle;   
  
public class MyOracle extends DBOracle {   
  
public String getDBPagineSql(String sql, long offset, int maxsize) {   
StringBuffer ret = new StringBuffer("select ss1.* from (select tt1.*,rownum rowno_ from (").append(sql).append(   
        ") tt1 where rownum <= ").append((offset + maxsize)).append(") ss1 where ss1.rowno_ >= ").append(   
        (offset + 1));   
return ret.toString();   
}   
}   
```

写好后将com.frameworkset.orm.adaptors.MyOracle配置到poolman.xml文件中既可：

Xml代码

```xml
<?xml version="1.0" encoding="utf-8"?>  
<poolman>  
<!--注意：  
dbtype名称可以是一个别名，或者直接是对应数据库的驱动包路径，例如：oracle.jdbc.driver.OracleDriver  
默认的别名：  
  
[*]as400  
[*]db2app  
[*]db2net  
[*]cloudscape  
[*]hypersonic  
[*]interbase  
[*]instantdb  
[*]mssql  
[*]mysql  
[*]mariadb  
[*]oracle  
[*]postgresql  
[*]sapdb  
[*]sybase  
[*]weblogic  
[*]axion  
[*]informix  
[*]odbc  
[*]msaccess  
[*]derby  
[*]sqlite  
  
  
如果dbtype使用的别名与默认别名重复，则将覆盖默认的数据库适配器，如果不想，则需要指定其他的别名，比如下面的dbtype="myoracle"，这样我们要引用这个适配器的话，需要在datasource中明确指定这个别名：  
<datasource>  
   <dbtype>myoracle</dbtype>  
</datasource>  
-->  
    <adaptor dbtype="myoracle">com.frameworkset.orm.adaptors.MyOracle</adaptor> <datasource>  
    <dbname>bspf</dbname>  
    <loadmetadata>false</loadmetadata>  
    <jndiName>oracle_datasource_jndiname</jndiName>  
     <autoprimarykey>false</autoprimarykey>  
     <cachequerymetadata>true</cachequerymetadata>  
    <driver>oracle.jdbc.driver.OracleDriver</driver>  
  
     <url>jdbc:oracle:thin:@//10.0.15.51:1521/orcl</url>   
    <username>testpdp1</username>  
    <password>testpdp1</password>  
  
    <txIsolationLevel>READ_COMMITTED</txIsolationLevel>   
    <poolPreparedStatements>false</poolPreparedStatements>  
  
    <initialConnections>2</initialConnections>  
      
    <minimumSize>2</minimumSize>  
    <maximumSize>10</maximumSize>  
    <queryfetchsize>10000</queryfetchsize>  
      
    <!--   
    是否检测超时链接（事务超时链接）  
    true-检测，如果检测到有事务超时的链接，系统将强制回收（释放）该链接  
    false-不检测，默认值  
     -->  
    <removeAbandoned>false</removeAbandoned>  
    <!--  
        链接使用超时时间（事务超时时间）  
        Set max idle Times in seconds ,if exhaust this times the used connection object will be Abandoned removed if removeAbandoned is true.  
        default value is 300 seconds.  
          
        see removeAbandonedTimeout parameter in commons dbcp.   
        单位：秒  
    -->  
    <userTimeout>300</userTimeout>  
    <!--   
        系统强制回收链接时，是否输出后台日志  
        true-输出，默认值  
        false-不输出  
     -->  
    <logAbandoned>true</logAbandoned>  
      
    
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
    <!--  
    /**  
     * 检测空闲链接处理时，是否对空闲链接进行有效性检查控制开关  
     * true-检查，都检查到有无效链接时，直接销毁无效链接  
     * false-不检查，缺省值  
     */  
     -->  
    <testWhileidle>true</testWhileidle>  
  
  
    <!--  
        定义数据库主键生成机制  
        缺省的采用系统自带的主键生成机制，  
        外步程序可以覆盖系统主键生成机制  
        由值来决定  
        auto:自动，一般在生产环境下采用该种模式，  
               解决了单个应用并发访问数据库添加记录产生冲突的问题，效率高，如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式  
        composite：结合自动和实时从数据库中获取最大的主键值两种方式来处理，开发环境下建议采用该种模式，  
                   解决了多个应用同时访问数据库添加记录时产生冲突的问题，效率相对较低， 如果生产环境下有多个应用并发访问同一数据库时，必须采用composite模式  
    -->  
    <keygenerate>composite</keygenerate>  
  
    <!--poolman的日志信息输出改用log4j来输出到日志文件，相关的配置见log4j.properties文件-->  
    <!--<logFile>dbaccess.log</logFile>  
    <debugging>true</debugging>-->  
    <!-- 请求链接时等待时间，单位：秒  
    客服端程序请求链接等待时间超过指定值时，后台包等待超时异常  
     -->  
    <maxWait>60</maxWait>  
      
    <!--  
        链接有效性检查sql语句 
     -->  
    <validationQuery>select 1 from dual</validationQuery>  
    <showsql>true</showsql>  
  
  </datasource>   
      
</poolman>  
```

