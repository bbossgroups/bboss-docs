### bboss持久层数据库适配器编写和注册方法

bboss持久层数据库适配器编写和注册方法

bboss持久层默认提供了以下数据库的适配器：

- as400

- db2app

- db2net

- cloudscape

- hypersonic

- interbase

- instantdb

- mssql

- mysql

- mariadb

- oracle

- postgresql

- sapdb

- sybase

- weblogic

- axion

- informix

- odbc

- msaccess

- derby

- sqlite

  随着数据库技术的发展，不断有新的数据库技术出现，因此我们需要针对这些新的数据库实现特定的适配器，本文介绍如何实现具体的适配器，以国内的达梦数据库为示例。

  **1.编写适配器**

  所有的适配器都需要继承抽象类:

  com.frameworkset.orm.adapter.

  DB可以任意扩展实现抽象类中DB的方法，比如生成分页sql的方法，如果不需要则无需覆盖。扩展适配器的分页机制实例：http://yin-bp.iteye.com/blog/1278360

  达梦数据库适配器：

  Java代码 

```java
package com.frameworkset.orm.adapter;  
import java.sql.Connection;  
import java.sql.SQLException;  
  
import com.frameworkset.orm.adapter.DB;  
  
public class DBDM extends DB{  
  
    @Override  
    public String toUpperCase(String in) {  
        // TODO Auto-generated method stub  
        return in;  
    }  
  
    @Override  
    public String getIDMethodType() {  
        // TODO Auto-generated method stub  
        return NO_ID_METHOD;  
    }  
  
    @Override  
    public String getIDMethodSQL(Object obj) {  
        // TODO Auto-generated method stub  
        return "";  
    }  
  
    @Override  
    public void lockTable(Connection con, String table) throws SQLException {  
        // TODO Auto-generated method stub  
          
    }  
  
    @Override  
    public void unlockTable(Connection con, String table) throws SQLException {  
        // TODO Auto-generated method stub  
          
    }  
```

**2.注册适配器**

写好后，在poolman.xml中进行配置注册即可：

Xml代码

```xml
<poolman>    
    <adaptor dbtype="dm">com.frameworkset.orm.adapter.DBDM</adaptor>   
     <adaptor dbtype="dm.jdbc.driver.DmDriver">com.frameworkset.orm.adapter.DBDM</adaptor>  
....  
</poolman>  
```

这里需要注册了两次，一次以dm数据库的驱动程序dm.jdbc.driver.DmDriver进行注册，一个以dm作为别名进行注册，这样便于在编写多数据库sql语句时使用这个别名配置特定数据库的sql语句，bboss持久层配置多数据库语句的文档请参考：

http://yin-bp.iteye.com/blog/1180111

注意：
adaptor 的dbtype属性值可以是一个别名，或者直接是对应数据库的驱动包路径，例如：oracle.jdbc.driver.OracleDriver

上面也用达梦数据库的驱动dm.jdbc.driver.DmDriver注册了我们自己写的达梦数据库适配器，同时也用一个简写的别名dm注册了达梦数据库适配器。

默认的dbtype别名有：

- as400

- db2app

- db2net

- cloudscape

- hypersonic

- interbase

- instantdb

- mssql

- mysql

- mariadb

- oracle

- postgresql

- sapdb

- sybase

- weblogic

- axion

- informix

- odbc

- msaccess

- derby

- sqlite

  如果dbtype使用的别名与默认别名重复，则将覆盖默认的数据库适配器，如果不想，则需要指定其他的别名，比如下面的dbtype="myoracle"，这样我们要引用这个适配器的话，需要在datasource中明确指定这个别名：

  Xml代码

```xml
<datasource>  
       ...........  
   <dbtype>myoracle</dbtype>  
..............  
</datasource>  
```

如果数据源指定了dbtype，则bboss持久层加载数据源会通过对于的dbtype检索注册的数据库适配器，否则会通过数据库驱动包路径检索对应的数据库适配器。


  