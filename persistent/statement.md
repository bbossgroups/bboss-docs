### bbossgroups持久层框架动态sql语句配置和使用

动态sql配置文件内容：

Xml代码 

```xml
<?xml version="1.0" encoding="UTF-8"?>  
  
<properties>  
    <property name="getRequesterDaoListInfo">  
        <![CDATA[ 
            select r.*, o.ORG_NAME as service_requester_org_name from TD_ESB_REQUESTER r left join TD_SM_ORGANIZATION o  
             on r.SERVICE_REQUESTER_ORG = o.ORG_ID 
             where 1=1 
            #if($service_requester_code && !$service_requester_code.equals("")) 
             and SERVICE_REQUESTER_CODE = #[service_requester_code] 
            #end 
            #if($service_requester_account && !$service_requester_account.equals("")) 
             and SERVICE_REQUESTER_ACCOUNT like #[service_requester_account] 
            #end 
            #if($service_requester_name && !$service_requester_name.equals("")) 
             and SERVICE_REQUESTER_NAME like #[service_requester_name] 
            #end 
            #if($service_requester_org && !$service_requester_org.equals("")) 
             and SERVICE_REQUESTER_ORG = #[service_requester_org] 
            #end 
             
            order by create_time desc 
        ]]>  
    </property>  
      
      
</properties>  
```

动态sql语句中包含两种类型的变量：

#[service_requester_name] 这种变量将被转换成预编译绑定参数变量

$service_requester_code 这种变量直接被替换成相应的变量值

除了sql参数变量外，我们在sql中使用以下逻辑结构

Java代码

```java
#if/#elseif/#end   
```

来动态生成sql语句，以下是一个简单的结构实例：

Java代码

```java
#if( $foo < 10 )   
#elseif( $foo == 10 )   
#elseif( $bar == 6 )   
#else   
#end   
```

根据传入的变量的值做相应的处理，从而在运行时动态生成sql语句，逻辑表达中使用变量的语法为$开头加变量名称：$service_requester_name。

判断变量是否为null的语法为：

Java代码

```java
#if($service_requester_name)   
```

逻辑表达式使用的Velocity模板引擎语法，非常灵活，我们可以在动态sql模板中使用几乎所有Velocity模板引擎的功能，具体使用方法可以参考Velocity模板引擎官网资料。

加载sql配置文件并定义ConfigSQLExecutor 组件：

Java代码

```java
public static final String DBNAME = "stsmc";      
    private static ConfigSQLExecutor executor = new ConfigSQLExecutor("com/bboss/tjbb/dao/impl/stxftj.xml");   
```

只要确保com/bboss/tjbb/dao/impl/stxftj.xml文件存放在classpath环境中即可，一般是在classes目录下。

dao组件中执行动态sql并返回执行结果：

Java代码

```java
package com.bboss.esb.uddi.requester.dao.impl;    
        
    import com.bboss.esb.uddi.requester.dao.RequesterDao;    
    import com.bboss.esb.uddi.requester.entity.Requester;    
    import com.frameworkset.common.poolman.ConfigSQLExecutor;    
    import com.frameworkset.util.ListInfo;    
        
    public class RequesterDaoImpl implements RequesterDao {    
            
        public static final String dbName= "stsmc";      
        private static ConfigSQLExecutor executor = new ConfigSQLExecutor("com/bboss/tjbb/dao/impl/stxftj.xml");     
        
        
        public ListInfo getRequesterDaoListInfo(String sortKey, boolean desc,    
                long offset, int pagesize, Requester queryCondObj) throws Exception {    
            // 还是分页查询哦    
   
           queryCondObj.setService_requester_name("%" +   
           queryCondObj.getService_requester_name() + "%");//设定模糊查询条件值  
           queryCondObj.setService_requester_account("%" +   
           queryCondObj.getService_requester_account() + "%");//设定模糊查询条件值  
            return executor.queryListInfoBeanWithDBName(Requester.class,     
                    dbName, "getRequesterDaoListInfo", offset, pagesize, queryCondObj);    
        }    
        
    }    
```

这里的dbName是在poolman.xml文件中配置的一个数据源。上文方法演示了如何通过ConfigSQLExecutor 组件读取动态sql实现分页查询的功能。至于数据源的配置请参考博文：

http://yin-bp.iteye.com/blog/1112892

需要额外说明的是怎么对like查询的值进行处理，如何添加%号，需要在dao方法中添加%号，否则不能正常查询出结果，我们配置的动态sql语句中有两个查询条件是SERVICE_REQUESTER_NAME like #[service_requester_name]和SERVICE_REQUESTER_ACCOUNT like #[service_requester_account]

我们在dao方法中如此设置%号：

Java代码

```java
queryCondObj.setService_requester_name("%" +   
          queryCondObj.getService_requester_name() + "%");//设定模糊查询条件值  
          queryCondObj.setService_requester_account("%" +   
          queryCondObj.getService_requester_account() + "%");//设定模糊查询条件值  
```

相关文档：

[bbossgroups 持久层模板sql变量参数设置的三种方式](http://yin-bp.iteye.com/blog/1173652)