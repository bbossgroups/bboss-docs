# 持久层快速入门系列一

# 1.导入bboss
先在应用中导入bboss 持久层：bboss persistent最新版本号6.1.9，以实际为准，查看[最新版本号](https://repo1.maven.org/maven2/com/bbossgroups/bboss-persistent/)

**maven坐标**

Xml代码

```xml
<dependency>   
    <groupId>com.bbossgroups</groupId>   
    <artifactId>bboss-persistent</artifactId>   
    <version>6.1.9</version>   
</dependency> 
```

**gradle坐标**

Java代码

```java
compile 'com.bbossgroups:bboss-persistent:6.1.9'  
```

# 2.bboss持久层操作实例

## 2.1 启动数据源

启动数据源，dbname为test

Java代码

```java
try{  
        SQLUtil.startPool("test",//数据源名称  
                "com.mysql.jdbc.Driver",//oracle驱动  
                "jdbc:mysql://localhost:3306/bboss",//mysql链接串  
                "root","123456",//数据库账号和口令  
                 "select 1 " //数据库连接校验sql  
                );  
```

## 2.2 在数据源上执行查询

在数据源上执行查询，dbname为test：

Java代码 

```java
List<HashMap> datas = SQLExecutor.queryListWithDBName(HashMap.class,"test", "select * from t_hive");  
            for(int i = 0; datas != null && i < datas.size(); i ++)  
            {  
                        System.out.println(datas.get(i));  
            }  
        } catch(SQLException e) {  
            e.printStackTrace();  
        }  
```

## 2.3 一个简单的加载sql配置文件的dao实例

sql配置文件，文件必须在classes路径下：com/test/sql/test.xml

Xml代码 

```xml
<properties>    
    <property name="tdSmUserJobOrgSelect">    
        <![CDATA[  
            select * from td_sm_userjoborg where user_id=#[userId]  
        ]]>    
    </property>    
</properties> 
```

## 2.4 创建一个加载配置文件的通用dao：
Java代码

```java
com.frameworkset.common.poolman.ConfigSQLExecutor dao= new com.frameworkset.common.poolman.ConfigSQLExecutor("com/test/sql/test.xml");    
//指定dbname为test    
String dbname="test";  
List<HashMap> datas = dao.queryListWithDBName(HashMap.class,dbname, "tdSmUserJobOrgSelect");    
            for(int i = 0; datas != null && i < datas.size(); i ++)    
            {    
                        System.out.println(datas.get(i));    
            }    
        } catch(SQLException e) {    
            e.printStackTrace();    
        }   
```

也可以不指定dbname：
Java代码 

```java
List<HashMap> datas = dao.queryList(HashMap.class, "tdSmUserJobOrgSelect");    
            for(int i = 0; datas != null && i < datas.size(); i ++)    
            {    
                        System.out.println(datas.get(i));    
            }    
        } catch(SQLException e) {    
            e.printStackTrace();    
        }  
```

**注意：sql配置文件中的sql语句支持热加载，在线修改实时生效，对于开发和调试非常方便**

# 3.获取bboss持久层连接池信息
动态获取bboss持久层配置的所以连志池的名称和配置信息：

Java代码 

```java
import com.frameworkset.common.poolman.util.JDBCPoolMetaData;  
import com.frameworkset.common.poolman.DBUtil;  
            DBUtil dbUtil = new DBUtil();  
        Enumeration enum_ = dbUtil.getAllPoolnames();  
        while(enum_.hasMoreElements()){  
            String poolname = (String)enum_.nextElement();  
            JDBCPoolMetaData metadata =    
                 DBUtil.getPool(poolname).getJDBCPoolMetadata();  
}  
```

# 4.设置sql配置文件保存文件目录
默认情况下sql配置文件都在classes path目录下面，如果想将sql保存到外部文件目录，则可以通过全局方法指定sql文件存放目录：
```java
String sqlMappingDir = "d:/sqlconf";
PoolManConfiguration.setSqlMappingDir(sqlMappingDir);

```
更多bboss 持久层文档，请参考：http://yin-bp.iteye.com/blog/2181720


  