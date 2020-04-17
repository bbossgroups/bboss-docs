### 关于bboss-persistent持久层框架通过jndi引用外部数据源（datasource）

关于bboss-persistent持久层框架通过jndi引用外部数据源（datasource）的说明

bboss项目下载列表 在sourceforge访问地址为：
https://sourceforge.net/project/showfiles.php?group_id=238653

bboss persistent框架可以引用外部数据源，例如tomcat，weblogic和WebSphere平台提供的datasource,需要在poolman.xml文件中配置，具体的做法可以参考本博客文章《bboss persistent框架数据库连接池配置介绍》，这里补充一点关于jndi名称的配置问题。这里以tomcat为例。

假如在tomcat的context上下文配置文件中配置了如下的datasource：

<vContext path="" crossContext="true"v>

​        <!--  

JAAS -->

​        < Realm

​                className="org.apache.catalina.realm.JAASRealm"

​                appName="PortalRealm"

​                userClassNames="com.liferay.portal.kernel.security.jaas.PortalPrincipal"

​                roleClassNames="com.liferay.portal.kernel.security.jaas.PortalRole"

​        />
​                < Resource

​                name="jdbc/LiferayPool"

​                auth="Container"

​                type="javax.sql.DataSource"

​                driverClassName="oracle.jdbc.driver.OracleDriver"

​                url="jdbc : oracle : thin : @localhost : 1521 : orcl"

​                username="lportal"

​                password="lportal"

​                maxActive="20"

​        />

​               <!--

​        Uncomment the following to disable persistent sessions across reboots.

​        -->

​            <!--

< Manager pathname="" />

-->

​           <!--

​        Uncomment the following to not use sessions. See the property
​        "session.disabled" in portal.properties.

​        -->

​        <!--

< Manager className="com.liferay.support.tomcat.session.SessionLessManagerBase" />

-->

</ Context >

其中的jdbc/LiferayPool即为该数据源jndi查找名称。

下面说明如何在poolman中配置一个连接池，该池直接通过【jdbc/LiferayPool】引用上下文中配置的连接池。poolman.xml文件的配置如下：

< ?xml version="1.0" encoding="gb2312"? >

< poolman >

< datasource external="true" >

​        <!--

​        连接池的逻辑名称，

​        应用程序通过该名称获取对应的数据库连接池的链接

​        持久层接口通过指定该名称在相应的数据库上执行增删改查操作

​         import java.sql.Connection;

​         import java.sql.SQLException;

​         import com.frameworkset.common.poolman.DBUtil;

​         .....
​        

​     Connection con = DBUtil.getConection(“bspf”);

​    -->

< dbname >bspf</ dbname >

​        <!--

数据库链接池启动时是否加载数据库表和视图的元数据 ，应用程序可以调用以下接口获取这些数据：

import com.frameworkset.common.poolman.DBUtil;

import com.frameworkset.common.poolman.sql.TableMetaData;

...

TableMetaData tableMetaData = DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF");

​    System.out.println(DBUtil.getTableMetaData("V_VCH_FIXED_TAX_CF"));

-->

​    < loadmetadata >false</ loadmetadata >

​               <!-- 

​    datasource的jndi查找名称，如果指定了jndiName参数

​    bboss persistent框架将链接池绑定为datasource，应用程序可以通过jndi查找获取数据库链接，例如：

​     Context ctx = new InitialContext();

​     DataSource ds =  (DataSource)ctx.lookup("bspf_datasource_jndiname");

​     -->

< jndiName >bspf_datasource_jndiname</ jndiName >

​          <!--

​            当dataSource的external属性指定为true时，必须指定外部数据源jndi绑定名称

​            如果系统需要通过内部的jndi查找该数据源，还必须指定jndiName名称，比如系统管理中hibernate

​            必需通过内部jndi访问数据源来获取数据库的链接，以及应用hibernate产生自定义数据库主键机制

​    -->

​    < externaljndiName >java:com/env/jdbc/LiferayPool</ externaljndiName >

​              <!--

​    autoprimarykey

​        true表示启用insert自动补全功能，如果在insert sql语句中没有指定主键字段，那么bboss persistent将自动补全该条语句

​        首先分析sql语句得到table name，然后根据该表名到tableinfo表中查找表的主键信息，找到后根据主键配置生成一个主键，补全到sql语句中

​        当执行插入操作的方法时，该主键将作为方法的返回值返回给调用程序。

​    false表示不自动生成，默认值，这样避免分析sql语句，提高系统性能。

​         -->

​    < autoprimarykey >false</ autoprimarykey >

​              <!--

​    cachequerymetadata 是否缓冲查询语句中查询列表中的字段信息

​        true表示缓冲，提高系统新能，一旦系统内存不够时，将自动释放这些缓冲

​        false，表示不缓冲

​         -->

​    < cachequerymetadata >false</ cachequerymetadata >

​               <!--

​    driver 指定数据库驱动程序，外部数据源需要指定数据库的驱动程序是为了识别不同类型的数据库

​         -->

​    < driver >oracle.jdbc.driver.OracleDriver</ driver >

​    < keygenerate >composite</ keygenerate >

​           <!-- 

如果在tableinfo表中配置了表的主键信息，而且主键通过sequence生成，则synsequence元素用来控制是否同步sequence的当前值为最大的未使用的表的主键值+1。

​         -->

​    < synsequence >false</ synsequence >   

​              <!--

​             设置为true，则相关的增删改查，批处理，预编译处理，存储函数调用sql语句将全部被输出的系统后台
​                否则不输出

​     -->

​    < showsql >true</ showsql >

  </ datasource >

</ poolman >

注意：

 < externaljndiName >java:com/env/jdbc/LiferayPool</ externaljndiName >

我们在jdbc/LiferayPool前面添加了java:com/env/，这样才能正确地发现配置在上下文中的datasource，否则将无法获取这个datasource。