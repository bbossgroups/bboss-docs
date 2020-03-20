### bbossgroups持久层ConfigSQLExecutor组件的典型用法-预编译操作

本文介绍bbossgroups持久层ConfigSQLExecutor组件的典型用法-预编译操作

本文分三部分：

1.dao层写法

2.sql配置文件配置方法（可以支持多种数据库sql配置）

3.涉及的datasource的配置

第一部分 dao层写法

Java代码

```java
package com.chinacreator.tjbb.dao.impl;  
  
import java.util.ArrayList;  
import java.util.HashMap;  
import java.util.List;  
import java.util.Map;  
  
  
  
import com.chinacreator.tjbb.dao.SjxftjDao;  
import com.chinacreator.tjbb.dto.DssjxfTj;  
import com.chinacreator.tjbb.dto.SjxftjDto;  
import com.frameworkset.common.poolman.ConfigSQLExecutor;  
import com.frameworkset.common.poolman.Record;  
import com.frameworkset.common.poolman.handle.NullRowHandler;  
  
  
public class SjxftjDaoImpl implements SjxftjDao{  
    public static final String DBNAME = "stsmc";  
    private static ConfigSQLExecutor executor = new ConfigSQLExecutor("com/chinacreator/tjbb/dao/impl/stxftj.xml");  
  
    public List<SjxftjDto> findSjxftjList()  throws Exception{          
        return executor.queryListWithDBName(SjxftjDto.class, DBNAME, "findSjxftjList");  
    }  
  
    public List<DssjxfTj> findDstjList(String startDate,String endDate, String tbname) throws Exception{  
        return executor.queryListWithDBName(DssjxfTj.class, DBNAME, "findDstjList", startDate,endDate,tbname);  
    }  
  
    public List<String> findDsMcList(String date) throws Exception{  
        final List<String> list = new ArrayList<String>();  
        executor.queryWithDBNameByNullRowHandler(new NullRowHandler() {  
            @Override  
            public void handleRow(Record record) throws Exception {  
                list.add(record.getString("DSMC"));  
            }  
        }, DBNAME, "findDsMcList", date);  
        return list;  
    }  
  
  
      
      
    public Map<String, String> findDsMcMap(String startDate,String endDate) throws Exception {  
        final Map<String, String>  map = new HashMap<String, String>();  
        executor.queryWithDBNameByNullRowHandler(new NullRowHandler() {  
            @Override  
            public void handleRow(Record record) throws Exception {  
                String mc = record.getString("DSMC");  
                String dm = record.getString("DSDM");  
                map.put(dm, mc);  
            }  
        }, DBNAME, "findDsMcMap", startDate,endDate);  
          
        return map;  
    }  
}  
```

