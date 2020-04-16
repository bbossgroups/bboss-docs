### bboss persistent 1.0.3 发布，功能变更清单见正文

下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=302766&release_id=647144

 

archive directory:

src--source code

test--test source code

conf--contain database pool config files

listener--database transaction leak listener file

lib--bboss persistent framework depends jars

dist--bboss persistent release package.

update function list:

\------------------------------

 1.0.3 - 2009-4-13

\------------------------------

 o 单独使用poolman，未建立表tableinfo，后台报SQLException异常

修改程序：

com/frameworkset/common/poolman/management/BaseTableManager.java

 o sql server 2005翻页查询异常问题

原因是sqlserver中需要这样创建statement

Statement stmt = con.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE,ResultSet.CONCUR_READ_ONLY);

但是poolman中是这样获取Statement ：

stmt=conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE,ResultSet.CONCUR_UPDATABLE);

因此报下面的错误：

com.microsoft.sqlserver.jdbc.SQLServerException: 不支持此游标类型/并发组合。

修改方法：

通过适配器方法来获取游标类型：

修改的程序

com\frameworkset\orm\adapter\DB.java

com\frameworkset\orm\adapter\DBMSSQL.java

com\frameworkset\common\poolman\StatementInfo.java

com\frameworkset\common\poolman\DBUtil.java

o 增加获取DataSource的接口

修改的程序：

com\frameworkset\common\poolman\util\SQLUtil.java

增加以下三个方法

获取缺省连接池对应的datasource

public static DataSource getDataSource()

获取给定数据库连接池名对应的datasource

 public static DataSource getDataSource(String dbname)

通过jndi名称查找数据源

  public static DataSource getDataSourceByJNDI(String jndiname) throws NamingException     

com/frameworkset/common/poolman/handle/type/CallableStatementResultSet.java

增加以下方法：

public Object getObject(int arg0, Map arg1) throws SQLException


com/frameworkset/common/poolman/util/JDBCPool.java

增加方法：

public static DataSource find(String jndiName) throws NamingException

增加全局变量：

public static Context ctx = null;

o 新增bboss jndi 上下文环境

新增jndi上下文环境，解决系统脱离应用服务器单独运行时无法进行jndi绑定的问题。

新增和修改的程序如下：

com/frameworkset/common/poolman/jndi/DummyContext.java（新增）

com/frameworkset/common/poolman/jndi/DummyContextFactory.java（新增）

com/frameworkset/common/poolman/util/JDBCPool.java（修改）

 

o DBUtil执行两个表的联合查询，如果两表的查询字段中包含相同字段名但没有指定这些字段的别名时，查询结果中前面的表字段的值被后面的表字段的值覆盖。例如：

select table_a.id,table_b.id from table_a,table_b;

代码段如下：

DBUtil dbUtil = new DBUtil();

  dbUtil.executeSelect("select table_a.id,table_b.id from table_a,table_b ");

  System.out.println("table_a.id" + dbUtil.getInt(0, 0));

  System.out.println("table_b.id" + dbUtil.getInt(0, 1));

查询的结果显示table_a.id的值变为table_b.id字段的值。

原因分析：

执行本次查询后，结果集元数据为：

[id,id]

可见两个查询字段的列名相同，有没有指定别名字段，由于DBUtil临时存放结果集的数据结构为一个hash表，key为字段名，值为相应字段的值，这样就导致原来的字段的值被前面的字段的值覆盖。

解决办法：

A. 为字段指定不同的别名

B. 修改原来的存储机制，为相同的列名建立不同的别名

建立别名的规则为：列名+#$+列索引，例如：

将原来的[id,id]转换为[id,id#$_1]


修改的程序清单为：

/common_old_dbcp/src/com/frameworkset/common/poolman/sql/PoolManResultSetMetaData.java

/common_old_dbcp/src/com/frameworkset/common/poolman/ResultMap.java

/common_old_dbcp/src/com/frameworkset/common/poolman/Record.java

/common_old_dbcp/src/com/frameworkset/common/poolman/DBUtil.java

测试脚本：

create table TABLE_A

(

  ID  NUMBER(10),

  ID1 NUMBER(10)

)

/

create table TABLE_b

(

  ID  NUMBER(10),

  ID1 NUMBER(10)

)

/

insert into table_a (id,id1) value(1,11)

/

insert into table_b (id,id1) value(2,22)

/

测试程序：

/common_old_dbcp/test/com/frameworkset/common/TestTwoTablewithSameCol.java

执行查询结果正确。

o  sqlserver中如果数据库表有列的类型是char(1),那么, DBUtil查询这个字段的时候,就报错出错的原因可能是poolman.xml文件中指定的数据库驱动程序不正确。

< jndiName >hb_datasource_jndiname</ jndiName >

< driver >com.microsoft.sqlserver.jdbc.SQLServerDriver</ driver >

正确的配法为：

< jndiName >hb_datasource_jndiname</ jndiName >

< driver >com.microsoft.jdbc.sqlserver.SQLServerDriver</ driver >

现在不明确是否存在com.microsoft. sqlserver.jdbc.SQLServerDriver驱动程序，估计这个驱动程序是sqlserver 2005版本的驱动程序。

需要修正程序：

/common_old_dbcp/src/com/frameworkset/orm/adapter/DBFactory.java

以便能够正确地找到sql server的适配器（不同数据库的适配器，是通过驱动程序查找的）。修正后，问题解决。

o  改造oracle分页机制

修改程序：

com/frameworkset/orm/adapter/DBOracle.java

\------------------------------

 1.0.2 - DEC 24, 2008

\------------------------------

 o Improved  blob/clob handle perfermence.

 o Fixed maybe memery leaker in handle blob/clob columns.

First version of bboss persitent released.

\------------------------------

 1.0.1 - DEC 14, 2008

\------------------------------

First version of bboss persitent released.

[http://blog.csdn.net/yin_bp]