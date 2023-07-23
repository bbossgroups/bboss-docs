### bboss持久层操作hive实例

先在应用中导入bboss 持久层和hive驱动（bboss persistent版本号5.0.1，以实际为准：查看[最新版本号](http://repo1.maven.org/maven2/com/bbossgroups/bboss-persistent/)）：

maven坐标

dependency

  groupId  com.bbossgroups  /groupId

artifactId   bboss-persistent   /artifactId  

version  6.1.0  /version  /dependency   dependency  

groupId   org.apache.hive  /groupId 

 artifactId   hive-jdbc   /artifactId

version   2.1.0   /version   /dependency

**gradle坐标**
compile 'com.bbossgroups:bboss-persistent:6.1.0'
compile 'org.apache.hive:hive-jdbc:'2.1.0'

bboss持久层操作hive实例

**启动hive数据源，dbname为test**

Java代码

```java
try{  
        SQLUtil.startPool("test",//数据源名称  
                "org.apache.hive.jdbc.HiveDriver",//hive驱动  
                "jdbc:hive2://hadoop34:10000/default",//hive链接串  
                "hadoop","hadoop",//数据库账号和口令  
                 "select 1 " //数据库连接校验sql  
                );  
```

**在hive数据源上执行查询，dbname为test：**       
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

