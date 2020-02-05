### bboss模糊查询、动态sql、批处理资料荟萃

[bboss持久层快速上手](http://yin-bp.iteye.com/blog/2318974)

**1.批量增删改**-真正采用jdbc的预编译批处理来实现，性能杠杠的，sql语句只需要配置一条，无需foreach这里以更新为实例，新增和删除类似：

**Xml代码**

```xml
<property name="updateuser">  
        <![CDATA[ update user set name=#[name],age=#[age] where id=#[id] 
]]>  
    </property>  
```

执行批处理操作

**Java代码**

```java
List<User> users =...;  
executor.updateBeans("updateuser",users);//在bboss默认数据源上执行 ，多个用户的更新操作以预编译批处理的模式执行    
executor.updateBeans(dbname,"updateuser",users);//在指定的bboss数据源上执行   
```

参考文档：
[bboss预编译批处理api使用介绍](http://yin-bp.iteye.com/blog/1477995)
[bboss高性能db批处理功能使用方法介绍](http://yin-bp.iteye.com/blog/2374285)

**2.模糊查询**

以下是mysql数据库依赖模式：

**Xml代码** 

```xml
<property name="selectGroupInfoByNames">  
        <![CDATA[ select *   
from user    
where name like CONCAT('%',#[name],'%' )     
]]>  
    </property>  
```

**Java代码**

```java
Map<String, String> condition = new HashMap<String, String>();//定义包含变量的map对象，key为变量名称，value为变量值，模糊匹配符直接在sql中拼接     
condition.put("name", "john");    
List<CandidateGroup> list = executor.queryListBean(CandidateGroup.class,     
               "selectGroupInfoByNames", condition);//执行查询   
```

以下是数据库无关模式

**Xml代码**

```xml
<property name="selectGroupInfoByNames">  
        <![CDATA[ select *   
from user    
where name like #[name] 
]]>  
    </property>  
```

**Java代码**

```java
Map<String, String> condition = new HashMap<String, String>();//定义包含变量的map对象，key为变量名称，value为变量值，模糊匹配符条件值变量中拼接        
condition.put("name", "%john%");    
List<CandidateGroup> list = executor.queryListBean(CandidateGroup.class,     
               "selectGroupInfoByNames", condition);//执行查询   
```

**3.动态sql**

  [持久层模板sql变量参数设置的三种方式](http://yin-bp.iteye.com/blog/1173652)

[bboss 动态sql使用foreach循环示例](http://yin-bp.iteye.com/blog/1262066)  

[bboss持久层框架动态sql语句配置和使用](http://yin-bp.iteye.com/blog/1112887)

[bboss 持久层sql语句中一维/多维数组类型变量、list变量、map变量、bean对象变量使用说明](http://yin-bp.iteye.com/blog/1477995)

**4.原生态sql使用**

上面的例子都是bboss 持久层or maping的模式，接下来看一个原生sql批处理删除和更新操作的玩法。sql配置：

**Xml代码**  

```xml
<property name="deleteByKey">  
        <![CDATA[ 
            delete from TD_APP_BOM where id=? 
        ]]>  
    </property>  
```

java代码：

**Java代码**

```java
/** 
     * 用同样的sql只删一条记录，根据主键删除台账信息 
     * @throws AppBomException  
     */  
    public boolean delete(String id) throws AppBomException {  
        try {  
            executor.delete("deleteByKey", id);  
        } catch (Throwable e) {  
            throw new AppBomException("delete appbom failed::id="+id,e);  
        }  
        return true ;  
    }  
  
    /** 
     * 用同样的sql批量删除记录，受事务管理控制，如果有记录删除失败，则全部回滚之前的记录，当然也可以不要事务，也就是出错后 
     * 前面的记录成功、后面的记录不入库 
     * @param beans 
     * @return 
     * @throws AppBomException  
     */  
    public boolean deletebatch(String ... ids) throws AppBomException {  
        TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin();  
            executor.deleteByKeys("deleteByKey", ids);  
            tm.commit();  
        } catch (Throwable e) {  
              
            throw new AppBomException("batch delete appbom failed::ids="+ids,e);  
        }  
        finally  
        {  
            tm.release();  
        }  
  
        return true;  
    }  
//不要事务的模式  
    public boolean deletebatch(String ... ids) throws AppBomException {  
          
        try {  
              
            executor.deleteByKeys("deleteByKey", ids);  
              
        } catch (Throwable e) {  
              
            throw new AppBomException("batch delete appbom failed::ids="+ids,e);  
        }  
          
  
        return true;  
    }  
```

另外，对应只有根据主键修改一批数据的更新批处理接口：

sql配置：

**Xml代码**

```xml
<property name="updateByKey">  
        <![CDATA[ 
            update TD_APP_BOM set status = 0 where id=? 
        ]]>  
    </property>  
```

java代码：

**Java代码** 

```java
/** 
     * 用同样的sql只更新一条记录，根据主键更新台账信息 
     * @throws AppBomException  
     */  
    public boolean update(String id) throws AppBomException {  
        try {  
            executor.update("updateByKey", id);  
        } catch (Throwable e) {  
            throw new AppBomException("update appbom failed::id="+id,e);  
        }  
        return true ;  
    }  
  
    /** 
     * 用同样的sql批量更新记录，受事务管理控制，如果有记录更新失败，则全部回滚之前的记录，当然也可以不要事务，也就是出错后 
     * 前面的记录成功、后面的记录不入库 
     * @param beans 
     * @return 
     * @throws AppBomException  
     */  
    public boolean updatebatch(String ... ids) throws AppBomException {  
        TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin();  
            executor.upateByKeys("updateByKey", ids);  
            tm.commit();  
        } catch (Throwable e) {  
              
            throw new AppBomException("batch upadte appbom failed::ids="+ids,e);  
        }  
        finally  
        {  
            tm.release();  
        }  
  
        return true;  
    }  
//不要事务的模式  
    public boolean updatebatch(String ... ids) throws AppBomException {  
          
        try {  
              
            executor.updateByKeys("updateByKey", ids);  
              
        } catch (Throwable e) {  
              
            throw new AppBomException("batch update appbom failed::ids="+ids,e);  
        }  
          
  
        return true;  
    }  
```

  **5.mysql返回自增主键**

参考文档

[bboss持久层返回mysql自增主键功能说明](http://yin-bp.iteye.com/blog/1739260)

**6.分页接口**

[bboss持久层分页接口使用示例](http://yin-bp.iteye.com/blog/1703344)

**7.事务管理**

  持久层事务管理开发可以查阅：[bboss培训文档.ppt](https://github.com/bbossgroups/bboss-document/blob/master/文档/bbossgroups 框架培训教程.ppt?raw=true)

这个文档的第82-86页，有编程式事务、事务模板、声明式事务和注解事务的使用介绍。

事务管理组件TransactionManager介绍：

http://yin-bp.iteye.com/blog/1662509
    

**8.多数据库sql语句配置支持**

bboss持久层sql配置文件为同一个sql引用名称的sql语句提供了多数据库配置支持，持久层组件根据数据源对应的数据库类型执行相对应的sql语句，如果没有对应的sql则执行默认的sql
参考文档：

http://yin-bp.iteye.com/blog/1180111

**9.常用的查询方法**

查询返回map对象和List<Map>集合方法：http://yin-bp.iteye.com/blog/1112998

查询行处理器的使用：http://yin-bp.iteye.com/blog/1175847

bboss预编译批处理api使用介绍:http://yin-bp.iteye.com/blog/1197172

[bboss 持久层框架直接返回基础数据类型和基础数据类型List集合介绍](http://yin-bp.iteye.com/blog/1325224)

**10.bboss持久层多数据源配置管理及多数据库事务控制使用方法**

http://yin-bp.iteye.com/blog/2065059

  [持久层动态创建、启动、停止和使用多个数据源的方法](http://yin-bp.iteye.com/blog/1108772)

**11.bboss持久层操作Clob和Blob字段示例**

http://yin-bp.iteye.com/blog/1939084  

**12.两个通用dao组件SQLExecutor和ConfigSQLExecutor的基本使用方法介绍**

[bbossgroups 3.1SQLExecutor组件api使用实例](http://yin-bp.iteye.com/blog/1035991)

[bbossgroups持久层框架ConfigSQLExecutor组件api实例](http://yin-bp.iteye.com/blog/1112997)

**更多持久层资料请通过以下地址获取：**

http://yin-bp.iteye.com/category/55607    