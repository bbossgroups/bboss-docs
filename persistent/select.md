### bboss 持久层框架使用最佳实践-带连接（connection）参数接口和不带连接参数接口的选择

bboss项目下载列表 在sourceforge访问地址为：
https://sourceforge.net/project/showfiles.php?group_id=238653 

bboss 持久层框架提供了两类访问数据库的接口：带connection参数的接口和不带connection参数的接口。这两类接口在各种场合下面都可以使用，实现的功能都是一样的，但是适用的场合还是不一样的，下面分别描述。 

带连接connection参数的接口

带连接connection参数的接口在访问数据库时不再向bboss数据库连接池申请连接而直接使用外部参数传入的connection，这样一个connection可以供多个数据库操作使用，适用于数据库操作比较密集的环境，这样能够大大地提升应用程序的性能；如果在这种情况下使用不带connection参数的接口，意味着每次访问数据库都要向bboss连接池申请connection，由于连接池是一种串行化的带并发锁定的组件，频繁的申请和释放将导致严重的系统性能开销，最终导致系统性能缓慢。使用带connection参数的方法时需要确保最后关闭connection，防止连接泄漏。使用的步骤如下：

   a.调用DBUtil.getConnection()或者DBUtil.getConnection(String dbname)方法申请一个数据库连接

   b.访问DBUtil或者PreparedDBUtil或者CallableDBUtil的带connection参数的方法传入刚申请的connection对象进行数据库操作

   c.操作完毕后在finally块中关闭连接 

这里以预编译操作接口（其他接口都提供了类似功能不一一枚举）为例，举例如下：

PreparedDBUtil db = new PreparedDBUtil();

  Connection con = null;

  try {

   con = DBUtil.getConection(); //申请连接

   db.preparedDelete("delete from test");

   db.executePrepared(con);//使用连接



   db.preparedInsert("insert into test(id,name) values(?,?)");

   db.setString(1, "1");

   db.setString(2, "name");

   db.executePrepared(con);//使用连接



   db.preparedInsert("insert into test(name) values(?)");

   db.setString(1, "name");

   db.executePrepared(con);//使用连接



   db.preparedInsert("insert into test(name,clobname,blobname) values(?,?,?)");

   db.setString(1, "name");

   db.setClob(2, "content");

   db.setBlob(3, "content".getBytes());

   db.executePrepared(con);//使用连接



   db.preparedSelect("select * from test where name=?");

   //db.setString(1, "1");

   db.setString(1, "name");

   db.executePrepared(con);//使用连接

   for(int i = 0; i < db.size(); i ++)

   {

​    System.out.println(i + " id=" + db.getString(i, "id"));

​    System.out.println(i + " name=" + db.getString(i, "name"));

   }

  } catch (Exception e) {

   // TODO Auto-generated catch block

   e.printStackTrace();

  }

  finally

  {

   try {

   if(con != null)

​        con.close();//关闭连接

   } catch (SQLException e) {

​    // TODO Auto-generated catch block

​    e.printStackTrace();

   }

  }

2.不带连接参数的接口 

不带connection参数的方法适用于以下场景：

​    单一的数据库操作环境

​    事务环境中的数据库操作接口 

下面举例说明： 

- 单一的数据库操作 

PreparedDBUtil db = new PreparedDBUtil();

​    try {

​     db.preparedDelete("delete from test");

​     db.executePrepared();//使用不带连接的方法

} catch (Exception e) {

   // TODO Auto-generated catch block

   e.printStackTrace();

  }

- 事务环境下的多次数据库操作

PreparedDBUtil db = new PreparedDBUtil();

  TransactionManager tm = new TransactionManager();

  try {

   tm.begin();

   db.preparedDelete("delete from test");

   db.executePrepared();



   db.preparedInsert("insert into test(id,name) values(?,?)");

   db.setString(1, "test1");

   db.setString(2, "name");

   db.executePrepared();



   db.preparedInsert("insert into test(name) values(?)");

   db.setString(1, "name auto id");

   db.executePrepared();



   db.preparedInsert("insert into test(name,clobname,blobname) values(?,?,?)");

   db.setString(1, "name with clob and blob");

   db.setClob(2, "content");

   db.setBlob(3, "content".getBytes());

   db.executePrepared();



   db.preparedSelect("select * from test where name=?");

   //db.setString(1, "1");

   db.setString(1, "name");

   db.executePrepared();

   for(int i = 0; i < db.size(); i ++)

   {

​    System.out.println(i + " id=" + db.getString(i, "id"));

​    System.out.println(i + " name=" + db.getString(i, "name"));

​     }

   tm.commit();

  } catch (Exception e) {

   try {

​    tm.rollback();

   } catch (RollbackException e1) {

​    // TODO Auto-generated catch block

​    e1.printStackTrace();

   }

  }




  