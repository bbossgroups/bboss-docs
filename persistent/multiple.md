### 持久层动态创建、启动、停止和使用多个数据源的方法

本文介绍bbossgroups 持久层框架动态创建、启动、停止和使用多个数据源的方法，直接看代码，欢迎大家一起讨论，有疑问可相互交流。

//启动一个连接池数据源

DBUtil.startPool(dbname, dbdriver, dburl, dbuser, dbpassword,validationQuery);

//启动一个非连接池数据源

DBUtil.startNoPool(dbname, dbdriver, dburl, dbuser, dbpassword,validationQuery);

停止数据源：

DBUtil.stopPool(dbname);//停止指定数据源名称

DBUtil.stopPool();//停止默认数据源（poolman.xml中的排在第一个位置的数据源）

Java代码

```java
@Test  
    public void stopPool()  
    {  
        try {  
            DBUtil.stopPool(null);  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void startPool()  
    {  
        try {  
            DBUtil.getJDBCPoolMetaData(null);  
            DBUtil.startPool(null);  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
      
    @Test  
    public void testExternalMeta()  
    {  
        try {  
            DBUtil.startPool(null);  
            JDBCPoolMetaData meta = DBUtil.getJDBCPoolMetaData("mq");  
            JDBCPool pool = DBUtil.getPool("mq");  
            PoolmanStatic sss = new PoolmanStatic();  
            TransferObjectFactory.createTransferObject(meta, sss);  
            System.out.println(pool.getStartTime());  
            System.out.println(pool.getStopTime());  
            System.out.println(pool.getStatus());  
            sss.isExternal();  
            DBUtil.stopPool("bspf");  
            System.out.println(pool.getStartTime());  
            System.out.println(pool.getStopTime());  
            System.out.println(pool.getStatus());  
              
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
      
    @Test  
    public void testMeta()  
    {  
        try {  
            DBUtil.startPool(null);  
            JDBCPoolMetaData meta = DBUtil.getJDBCPoolMetaData("bspf");  
            JDBCPool pool = DBUtil.getPool("bspf");  
            PoolmanStatic sss = new PoolmanStatic();  
            TransferObjectFactory.createTransferObject(meta, sss);  
            System.out.println(pool.getStartTime());  
            System.out.println(pool.getStopTime());  
            System.out.println(pool.getStatus());  
            sss.isExternal();  
            DBUtil.stopPool("bspf");  
            PreparedDBUtil db = new PreparedDBUtil();  
            db.preparedSelect("Select 1 from dual");  
            db.executePrepared();  
            System.out.println(pool.getStartTime());  
            System.out.println(pool.getStopTime());  
            System.out.println(pool.getStatus());  
              
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
      
       
      
    @Test  
    public void startstopExterPool()  
    {  
        try {  
              
//          DBUtil.stopPool("mq");  
            System.out.println("DBUtil.statusCheck(\"mq\"):"+DBUtil.statusCheck("mq"));  
            System.out.println("DBUtil.statusCheck(\"bspf\"):"+DBUtil.statusCheck("bspf"));  
            System.out.println("DBUtil.statusCheck(\"kettle\"):"+DBUtil.statusCheck("kettle"));  
            DBUtil dbutil= new DBUtil();  
            try {  
                  
                dbutil.executeSelect("mq", "Select 1 from dual");  
                System.out.println("dbutil.size():" + dbutil.size());  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
              
            DBUtil.startPool("mq");  
            dbutil = new DBUtil();  
            dbutil.executeSelect("mq","Select 1 from dual");  
            System.out.println("dbutil.size():" + dbutil.size());  
            System.out.println("DBUtil.statusCheck(\"mq\"):"+DBUtil.statusCheck("mq"));  
            System.out.println("DBUtil.statusCheck(\"bspf\"):"+DBUtil.statusCheck("bspf"));  
            System.out.println("DBUtil.statusCheck(\"kettle\"):"+DBUtil.statusCheck("kettle"));  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void testStartPool()  
    {  
        TransactionManager tm = new TransactionManager();  
        try  
        {  
            DBUtil dbutil = new DBUtil();  
            String name = "db", driver="oracle.jdbc.driver.OracleDriver", jdbcurl="jdbc:oracle:thin:@//172.16.25.219:1521/orcl", username="baseline", password="baseline", readOnly="true", validationQuery="select 1 from dual";  
            DBUtil.startPool("db", driver, jdbcurl, username, password, readOnly, validationQuery);  
            tm.begin();  
              
            dbutil.executeSelect("db","Select 1 from dual");  
            tm.commit();  
            System.out.println("dbutil.size():" + dbutil.size());  
            DBUtil.stopPool("db");  
            PreparedDBUtil db = new PreparedDBUtil();  
            db.preparedSelect("db","Select 1 from dual");  
            db.executePrepared();  
              
//          dbutil.executeSelect("db","Select 1 from dual");  
              
        }  
        catch(Exception e)  
        {  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void testDBStatusStatic() throws SQLException  
    {  
        int numactive = DBUtil.getNumActive("mysql");  
        int numIdle = DBUtil.getNumIdle("mysql");  
        DBUtil.getConection("mysql");  
        List<PoolableConnection> objects = (List<PoolableConnection>)DBUtil.getTraceObjects("mysql");  
        for(int i = 0;  objects != null && i < objects.size(); i ++)  
        {  
            PoolableConnection con = objects.get(i);  
            System.out.println(con.getAutoCommit());  
            System.out.println(con.toString());  
        }  
        System.out.println();  
//      int numactive = DBUtil.getNumActive("bspf");  
          
    }  
    @Test  
    public void testStartPoolFromConf()  
    {  
        String configfile = "custom_poolman.xml";  
        SQLUtil.startPoolFromConf(configfile);  
        DBUtil dbutil = new DBUtil();  
        try {  
            dbutil.executeSelect("bspf_custom_1","Select * from tableinfo");  
            System.out.println("bspf_custom_1:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        try {  
            dbutil.executeSelect("bspf_custom","Select * from tableinfo");  
            System.out.println("bspf_custom:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void startPoolFromConf()  
    {  
        String configfile = "custom_poolman.xml";  
        String dbnamespace = "test";  
        SQLUtil.startPoolFromConf(configfile,dbnamespace);  
        DBUtil dbutil = new DBUtil();  
        try {  
            dbutil.executeSelect("test:bspf_custom_1","Select * from tableinfo");  
            System.out.println("test:bspf_custom_1:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        try {  
            dbutil.executeSelect("test:bspf_custom","Select * from tableinfo");  
            System.out.println("test:bspf_custom:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        try {  
            dbutil.stopPool("test:bspf_custom_1");  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
          
        try {  
            dbutil.stopPool("test:bspf_custom");  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void startPoolFromConfWithAllParams()  
    {  
        String configfile = "custom_poolman.xml";  
        String dbnamespace = "test";  
        String[] dbnames = new String[]{"bspf_custom_1"};  
        SQLUtil.startPoolFromConf(configfile,dbnamespace,dbnames);  
        DBUtil dbutil = new DBUtil();  
        try {  
            dbutil.executeSelect("test:bspf_custom_1","Select * from tableinfo");  
            System.out.println("test:bspf_custom_1:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        try {  
            dbutil.executeSelect("test:bspf_custom","Select * from tableinfo");  
            System.out.println("test:bspf_custom:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
    @Test  
    public void startPoolFromConfNulldbnamespace() {  
        String configfile = "custom_poolman.xml";  
        String dbnamespace = null;  
        String[] dbnames = new String[]{"bspf_custom_1"};  
        SQLUtil.startPoolFromConf(configfile,dbnamespace,dbnames);  
        DBUtil dbutil = new DBUtil();  
        try {  
            dbutil.executeSelect("bspf_custom_1","Select * from tableinfo");  
            System.out.println("bspf_custom_1:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        try {  
            dbutil.executeSelect("bspf_custom","Select * from tableinfo");  
            System.out.println("bspf_custom:"+dbutil.size());  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
      
    @Test  
    public void testStartPoolFromTempate() throws SQLException  
    {  
        DBUtil.getConection();  
        String poolname = "bspf-custom";  
        String driver = "org.apache.derby.jdbc.EmbeddedDriver";  
        String jdbcurl = "jdbc:derby:D:/workspace/bbossgroups-3.1/bboss-mvc/database/cimdb";  
        String username = "";   
        String password = "";  
        String readOnly = "false";  
        String txIsolationLevel = "READ_COMMITTED";  
        String validationQuery = "select 1 from dual";  
        String jndiName = "jdbc/derby-ds-custom";  
        int initialConnections = 2;  
        int minimumSize = 0;  
        int maximumSize = 10;  
        boolean usepool = true;  
        boolean  external = true;  
        String externaljndiName = "jdbc/derby-ds";          
        boolean showsql = true;       
        SQLUtil.startPool( poolname, driver, jdbcurl, username, password,  
                 readOnly,  
                 txIsolationLevel,  
                 validationQuery,  
                 jndiName,     
                 initialConnections,  
                 minimumSize,  
                 maximumSize,  
                 usepool,  
                  external,  
                 externaljndiName        , showsql        
                );  
    }  
```

通过以下一组接口你可以非常方便地动态创建和管理连接池，包括启动，停止，状态检测，可以根据需要调用相应的启动和创建方法，呵呵

Java代码

```java
DBUtil.stopPool     
DBUtil.startPool     
DBUtil.statusCheck     
DBUtil.startPool("db", driver, jdbcurl, username, password, readOnly, validationQuery);        
SQLUtil.startPoolFromConf(configfile);        
SQLUtil.startPoolFromConf(configfile,dbnamespace);        
SQLUtil.startPoolFromConf(configfile,dbnamespace,dbnames);       
SQLUtil.startPool( poolname, driver, jdbcurl, username, password,        
                 readOnly,        
                 txIsolationLevel,        
                 validationQuery,        
                 jndiName,           
                 initialConnections,        
                 minimumSize,        
                 maximumSize,        
                 usepool,        
                  external,        
                 externaljndiName        , showsql              
                );      
```

补充说明，bboss数据库类型名称和适配器映射关系：

   adapters.put("as400", DBDB2400.class);   

​    adapters.put("db2app", DBDB2App.class);

​    adapters.put("db2net", DBDB2Net.class);

​    adapters.put("cloudscape", DBCloudscape.class);

​    adapters.put("hypersonic", DBHypersonicSQL.class);

​    adapters.put("interbase", DBInterbase.class);

​    adapters.put("instantdb", DBInstantDB.class);

​    adapters.put("mssql", DBMSSQL.class);

​    adapters.put("mysql", DBMM.class);

​    adapters.put("oracle", DBOracle.class);

​    adapters.put("postgresql", DBPostgres.class);

​    adapters.put("sapdb", DBSapDB.class);

​    adapters.put("sybase", DBSybase.class);

​    adapters.put("weblogic", DBWeblogic.class);

​    adapters.put("axion", DBAxion.class);

​    adapters.put("informix", DBInformix.class);

​    adapters.put("odbc", DBOdbc.class);

​    adapters.put("msaccess", DBOdbc.class);

   adapters.put("derby", DBDerby.class);

   adapters.put("", DBNone.class);














  