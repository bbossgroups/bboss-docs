### bboss高性能db批处理功能使用方法介绍

bboss持久层在v5.0.3.5中新增简单的高效的db批处理功能，本文介绍使用方法。
**首先在项目中导入bboss 持久层包：**
**maven坐标**

<dependency>
    <groupId>com.bbossgroups</groupId>
    <artifactId>bboss-persistent</artifactId>
    <version>6.1.6</version>
</dependency>

**gradle坐标**
compile 'com.bbossgroups:bboss-persistent:6.1.6'

**轻量级批处理方法**

直接操作sql语句的组件SQLExecutor
com.frameworkset.common.poolman.SQLExecutor

Java代码

```java
public static <T> void executeBatch(String sql,List<T> datas,int batchsize, BatchHandler<T> batchHandler) throws SQLException   
//指定数据源dbname  
public static <T> void executeBatch(String dbname,String sql,List<T> datas,int batchsize, BatchHandler<T> batchHandler) throws SQLException  
```

**加载sql配置文件的组件ConfigSQLExecutor**

com.frameworkset.common.poolman.ConfigSQLExecutor

Java代码

```java
public <T> void executeBatch(String sqlname,List<T> datas,int batchsize, BatchHandler<T> batchHandler) throws SQLException  
//指定数据源dbname  
public <T> void executeBatch(String dbname,String sqlname,List<T> datas,int batchsize, BatchHandler<T> batchHandler) throws SQLException  

```

批处理语句参数设置器：
Java代码

```java
package com.frameworkset.common.poolman;  
/** 
 * 轻量级jdbc批处理操作记录设置器 
 */  
  
import java.sql.PreparedStatement;  
import java.sql.SQLException;  
  
public interface BatchHandler<T> {  
    /** 
     * 
     * @param stmt jdbc PreparedStatement   parameterIndex the first parameter is 1, the second is 2, ... 
     *                                      x the parameter value 
     * @param record 当前操作的变量 
     * @param i 行索引 
     * @throws SQLException 
     */  
    public void handler(PreparedStatement stmt,T record,int i) throws SQLException;  
  
}  

```

**使用实例**

以SQLExecutor组件来做为例子介绍如下：

Java代码

```java
@Test  
    public void testBatch() throws SQLException {  
        List<Map<String,String>> datas = new ArrayList<Map<String,String>>();//构造数据  
        for(int i = 0; i < 100000; i ++){  
            Map<String,String> data = new HashMap<String, String>();  
            if(i % 3 == 0)  
                data.put("name","jack_"+i);  
            else if(i % 3 == 1)  
                data.put("name","brown_"+i);  
            else if(i % 3 == 2)  
                data.put("name","john_"+i);  
            datas.add(data);  
        }  
        SQLExecutor.delete("delete from batchtest");//清空表数据  
//批处理执行  
        SQLExecutor.executeBatch("insert into batchtest (name) values(?)", datas, 10,new BatchHandler<Map<String,String>>() {  
            @Override  
            public void handler(PreparedStatement stmt, Map<String,String> record, int i) throws SQLException {  
                stmt.setString(1,record.get("name"));  
            }  
        });  
    }  
```

