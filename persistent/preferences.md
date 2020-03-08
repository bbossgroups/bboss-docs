### bbossgroups 持久层模板sql变量参数设置的三种方式

本文介绍bbossgroups 持久层模板sql变量参数设置的三种方式，切入正题。

**概述**

Map：所有的变量参数名和值以key/value的方式放入，如果值为null则以null方式替换变量

Bean：所有的变量参数和值以都与bean中对应的属性字段名和值相对应，如果值为null则以相应属性类型的sql类型的 null方式替换变量

SQLParams：所有的变量参数和值以都与SQLParams中对应的属性字段名和值相对应，参数的类型根据 

SQLParams.addSQLParam("id", value, SQLParams.INT)方法的第三个参数来指定，如果值为null则以相应类型的sql类型的 null方式替换变量。

如果是insert，delete，update还可以使用List<**Map>**,List**<Bean**>,List**<SQLParams**>来完成批量处理操作（后台全部采用预编译批处理来完成批量数据处理，性能好），下面分别介绍上述三种方式的使用方法。

**1.Map和List**<**Map**>**方式**

Map方式:

Java代码  

```java
              Map datas = new HashMap();  
datas.put("id", 139);  
datas.put("fieldName", "updateWithMapParams139");                 
executor.updateBean("updatesqltemplate", datas);    //更新记录  
```

List<**Map>**方式:

Java代码

```java
Map datas = new HashMap();  
     datas.put("id", 139);  
     datas.put("fieldName", "updateWithMapParams139");  
     List<Map> params_ = new ArrayList<Map>();  
     params_.add(datas);  
     datas = new HashMap();  
     datas.put("id", 140);  
     datas.put("fieldName", "updateWithMapParams140");  
     params_.add(datas);          
     datas = new HashMap();  
     datas.put("id", 141);  
     datas.put("fieldName", "updateWithMapParams141");  
     params_.add(datas);          
     executor.updateBeans("updatesqltemplate", params_);    //批量更新处理多个记录 
```

**2.Bean和List**<**Bean**>**方式**

Bean方式：

Java代码

```java
ListBean bean = new ListBean();  
           bean.setId(139);  
         List<ListBean> result = executor.queryListBean(ListBean.class, "dynamicsqltemplateid", bean);  
          
          bean.setId(-1);  
         result = (List<ListBean>) executor.queryListBean(ListBean.class,"dynamicsqltemplateid", bean);  
         bean.setId(0);  
         result = (List<ListBean>) executor.queryListBean(ListBean.class,"dynamicsqltemplateid", bean);  
```

List**<Bean**>方式:

Java代码

```java
ListBean bean = new ListBean();  
           bean.setId(139);  
List<ListBean> params_ = new ArrayList<ListBean>();  
params.add(bean );  
        executor.updateBeans("updatesqltemplate", params_);//批量更新处理多个记录  
```

**3.SQLParams和List**<**SQLParams**>**方式**

SQLParams方式：

Java代码

```java
SQLParams params = new SQLParams();  
     params.addSQLParam("id", 139, SQLParams.INT);        
     List<ListBean> result = executor.queryListBean(ListBean.class, "dynamicsqltemplateid", params);  
```

List**<SQLParams**>方式:

Java代码

```java
SQLParams params = new SQLParams();  
         params.addSQLParam("id", 139, SQLParams.INT);  
         params.addSQLParam("fieldName", "duoduo139", SQLParams.STRING);  
         List<SQLParams> params_ = new ArrayList<SQLParams>(2);  
         params_.add(params);  
         params = new SQLParams();  
         params.addSQLParam("id", 140, SQLParams.INT);  
         params.addSQLParam("fieldName", "duoduo140", SQLParams.STRING);  
         params_.add(params);  
          
         params = new SQLParams();  
         params.addSQLParam("id", 141, SQLParams.INT);  
           
         params.addSQLParam("fieldName", "duoduo141", SQLParams.STRING);  
         params_.add(params);  
         executor.updateBeans("updatesqltemplate", params_);//批量更新处理多个记录  
```

**补充说明-executor变量定义和相对应的sql配置文件**

executor变量定义：

Java代码

```java
ConfigSQLExecutor executor = = new ConfigSQLExecutor("com/frameworkset/sqlexecutor/sqlfile.xml");  
```

sql配置文件：

Xml代码

```xml
<?xml version="1.0" encoding='gb2312'?>  
<properties>  
    <description>  
<![CDATA[ 
    sql配置文件 
    可以通过名称属性name配置默认sql，特定数据库的sql通过在 
    名称后面加数据库类型后缀来区分，例如： 
    sqltest 
    sqltest-oracle 
    sqltest-derby 
    sqltest-mysql 
    等等，本配置实例就演示了具体配置方法 
 ]]>  
    </description>  
    <property name="sqltest"><![CDATA[select * from LISTBEAN]]>  
    </property>  
    <property name="sqltest-oracle"><![CDATA[select * from LISTBEAN]]>  
    </property>  
    <property name="sqltemplate"><![CDATA[select FIELDNAME from LISTBEAN where FIELDNAME=#[fieldName]]]>  
    </property>  
    <property name="sqltemplate-oracle"><![CDATA[select FIELDNAME from LISTBEAN where FIELDNAME=#[fieldName]  ]]></property>  
    <!-- 动态sql，如果FIELDNAME 不等于null，并且FIELDNAME不为""将FIELDNAME作为查询条件-->  
    <property name="dynamicsqltemplate"><![CDATA[select *  from LISTBEAN  where 1=1  
                    #if($fieldName && !$fieldName.equals("")) and FIELDNAME = #[fieldName] #end  ]]>  
    </property>  
    <!-- 动态sql，如果id 不等于-1，并且id不为0将id作为查询条件-->  
    <property name="dynamicsqltemplateid"><![CDATA[select *  from LISTBEAN  where 1=1  
                    #if($id != -1 && $id != 0) and id = #[id] #end  ]]>  
    </property>  
    <property name="updatesqltemplate"><![CDATA[update LISTBEAN  set FIELDNAME = #[fieldName] where id = #[id] ]]>  
    </property>  
    <property name="queryFieldWithSQLParams"><![CDATA[select count(1)  from LISTBEAN ]]>  
    </property>  
</properties>  
```

三种方式就介绍到此，欢迎感兴趣的朋友深入探讨bbossgroups持久层框架的方方面面。

相关文档：

[bbossgroups持久层框架动态sql语句配置和使用](http://yin-bp.iteye.com/blog/1112887)


  