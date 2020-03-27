### 基于bbossgroups持久层框架实现数据库分页查询

bbossgroups中提供的分页查询方法，非常简单，也非常的高效。

Java代码

```java
PreparedDBUtil dbUtil = new PreparedDBUtil();  
        try {  
            dbUtil.preparedSelect("select * from testnewface where object_id < ?",0,10);//分页查询方法  
            dbUtil.setInt(1, 100);  
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

在bbossgroups持久层框架中，主要是通过数据库适配器来实现不同数据库产品的分页查询算法，用户可以扩展这些适配器来实现自己的分页算法,以oracle为列：

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
<?xml version="1.0" encoding="gb2312"?>  
<poolman>  
    <adaptor dbtype="oracle">com.frameworkset.orm.adaptors.MyOracle</adaptor>   <datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <jndiName>bspf_datasource_jndiname</jndiName>  
  
        <autoprimarykey>false</autoprimarykey>  
        <cachequerymetadata>false</cachequerymetadata>  
          
        <driver>oracle.jdbc.driver.OracleDriver</driver>  
        <url>jdbc:oracle:thin:@//172.16.17.219:1521/orcl</url>  
        <username>baseline</username>  
        <password>baseline</password>  
  
        <txIsolationLevel>READ_COMMITTED</txIsolationLevel>  
  
        <nativeResults>true</nativeResults>  
  
        <poolPreparedStatements>false</poolPreparedStatements>  
  
        <initialConnections>2</initialConnections>  
  
        <minimumSize>0</minimumSize>  
        <maximumSize>10</maximumSize>  
        <!-- 
            控制connection达到maximumSize是否允许再创建新的connection true：允许，缺省值 false：不允许 
        -->  
        <maximumSoft>false</maximumSoft>  
  
        <!-- 
            是否检测超时链接（事务超时链接） true-检测，如果检测到有事务超时的链接，系统将强制回收（释放）该链接 false-不检测，默认值 
        -->  
        <removeAbandoned>true</removeAbandoned>  
        <!--  
        链接使用超时时间（事务超时时间）  
        单位：秒  
    -->  
        <userTimeout>50</userTimeout>  
        <!-- 
            系统强制回收链接时，是否输出后台日志 true-输出，默认值 false-不输出 
        -->  
        <logAbandoned>true</logAbandoned>  
  
        <!--  
        数据库会话是否是readonly，缺省为false 
     -->  
        <readOnly>true</readOnly>  
  
        <!--  
            对应属性：timeBetweenEvictionRunsMillis the amount of time (in  
            milliseconds) to sleep between examining idle objects for eviction  
        -->  
        <skimmerFrequency>10</skimmerFrequency>  
        <!--  
            对应于minEvictableIdleTimeMillis 属性： minEvictableIdleTimeMillis the  
            minimum number of milliseconds an object can sit idle in the pool  
            before it is eligable for evcition 单位：秒 空闲链接回收时间，空闲时间超过指定的值时，将被回收  
        -->  
        <connectionTimeout>10</connectionTimeout>  
        <!--  
            numTestsPerEvictionRun the number of idle objects to examine per run  
            within the idle object eviction thread (if any) 每次回收的链接个数  
        -->  
        <shrinkBy>0</shrinkBy>  
        <!--  
            /** * 检测空闲链接处理时，是否对空闲链接进行有效性检查控制开关 * true-检查，都检查到有无效链接时，直接销毁无效链接 *  
            false-不检查，缺省值 */  
        -->  
        <testWhileidle>true</testWhileidle>  
  
  
     -->  
        <maxWait>60</maxWait>  
  
        <!--  
        链接有效性检查sql语句 
     -->  
        <validationQuery>select 1 from dual</validationQuery>  
  
  
    </datasource>  
      
</poolman>  
```

最新版本bbossgroups-2.0-RC1下载地址：

http://sourceforge.net/projects/bboss/files/