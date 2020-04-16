### 获取数据库连接-bboss persistent

bboss persistent下载地址

[https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=302766&release_id=647144](https://sourceforge.net/project/showfiles.php?group_id=238653&amp;package_id=302766&amp;release_id=647144)

获取数据库连接-bboss persistent

bboss persistent获取数据链接的两种方式

1.从缺省数据库连接池中获取数据库连接:

​          java.sql.Connection connection = DBUtil.getConection();

​        poolman中缺省的数据库连接池就是在poolman.xml文件中配置的第一个连接池

2.从指定的数据库连接池中获取数据库连接

​        java.sql.Connection connection = DBUtil.getConection(dbname);

​        dbname为poolman.xml中< dbname ></ dbname >节点中间指定的数据库连接池的名称,例如:

​        < dbname >bspf</ dbname >

3.获取数据库连接的事务性

通过上述两种方式获取到的connection根据不同的环境具有不同特点，主要有两种环境，一种是事务环境，另一种是无事务环境，分别描述如下：

事务环境是指在数据库操作执行的上下文中使用TransactionManager组件开启了一个事务的环境。

如果在事务环境下获取connection，那么connection的事务将与上下文中的事务结合在一起，也就是说connection的事务随着上下文环境事务的提交而提交，也随着上下文事务的回滚而回滚，而且connection的回滚也会导致整个上下文事务的回滚。同时connection的setAutocommit方法和close方法将不会影响上下文事务，就是说不会起作用。

在事务环境下获取数据库链接后，不需显示关闭connection，也不需要显示commit，但是需要显示rollback，事务的提交和链接的关闭由外部事务自动关闭和提交。

在没有事务的环境下获取connection时，connection具有原始的jdbc connection的所有特性和方法，这里就不多说了。

4 .直接使用数据库连接需要注意的地方 

数据库连接池有效地管理着一组高度重用的数据库连接，如果通过持久层框架提供的组件（DBUtil,PreparedDBUtil,CallableDBUtil）操作数据库时，数据库连接的使用和管理由这些组件自动管理，无需应用程序去处理；但是如果程序员如果直接从数据库连接池中获取连接使用的话，那就一定要确保连接使用完毕后关闭该链接，释放操作过程中使用的所有资源，操作的一般标准模式为：

​            Connection con = null;

​            Statement stmt = null;

​            ResultSet rs = null;

​            try {

​                    con = DBUtil.getConection();

​                    //use the con varable。

​                    stmt = con.create Statement;

​                    rs = stmt.execute some sql ;                   

​            } catch (SQLException e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​            finally

​            {

​                    if(rs != null)

​                    {

​                            try {

​                                    rs.close();

​                            } catch (SQLException e) {

​                                    // TODO Auto-generated catch block

​                                    e.printStackTrace();

​                            }

​                    }

​                    if(stmt != null)

​                    {

​                            try {

​                                    stmt.close();

​                            } catch (SQLException e) {

​                                    // TODO Auto-generated catch block

​                                    e.printStackTrace();

​                            }

​                        }

​                        if(con != null)

​                        {
​                                try {

​                                        con.close();

​                                } catch (SQLException e) {

​                                        // TODO Auto-generated catch block

​                                        e.printStackTrace();

​                                }

​                        }

​                }

 