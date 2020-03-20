### bbossgroups-3.2 发布，支持对象xml序列化功能

经过一段时间紧张的开发工作，bbossgroups-3.2 发布了，支持对象xml序列化功能,可以方便地对各种复杂数据类型进行序列化和反序列化，甚至是文件对象内容。相比3.1，这个版本做了非常大的改进和功能扩展，优化aop/ioc体系结构，并且基于ioc技术开发出了独具特色的对象序列化技术；mvc、持久层、标签库、任务调度都做了很大的改进；开发文档和培训ppt更加全面，在iteye开辟了bbossgroups专栏:
http://bbossgroups.group.iteye.com/group/wiki，可以在第一时间将框架的最新动态呈现给大家。

总体来说，3.2是自bbossgroups第一个版本发布以来，功能最完善、最稳定的一个版本，完全可以非常好地支撑起j2ee项目各个方面的开发工作。

bbossgroups project contain follow subprojects:

1.bboss-aop, an aop framework.(ioc ,rpc[jms,mina,jgroups,cxf webservice,rmi,netty,rest,组播，多播],
    bean component,cxf webservice component framworkset，jms components frameworkset,plugin security components and so on).

2.bboss-persistent, a persistent framework().

a.灵活的事务管理（声明式事务管理，可编程事务管理，java注解事务管理，jdbctemplate事务管理，五种经典的事务类型，支持事务嵌套，支持多数据库分布式事务）

b.灵活的访问数据库的接口（普通sql操作，预编译sql操作，普通/预编译批处理操作，存储过程，函数）

c.一套经典的数据库操作标签库（增删改查，普通sql操作，预编译sql操作，普通/预编译批处理操作）

d.经典的多数据库连接池配置管理和使用方法（所有的数据库操作接口可以直接指定连接池的名称，方便地实现对不同数据库的操作）

3.bboss-taglib, a web layer taglib framework(list tag,pageine list tag,detail tag ,logic tag,tree tag,tabpane tag,dbutil tag).

4.bboss-event, an event framework(local event,remote distribute event framework base aop rpc framework).

5.bboss-util, an utility framework.

6.bboss-soa:3.2版本新增模块，实现对象-xml序列化与反序列化功能

7.antbuildall, ant build project that build up projects.可以运行antbuildall下的run.bat命令编译所有的子项目，并且更新相应工程的依赖jars。

8.apache-ant-1.7.1 所有工程构建依赖的ant环境，bbossgroups的构建无需依赖外部ant环境，每个子工程下都有相应的执行ant的bat命令文件，直接运行这些bat就可以构建相应的工程，构建的目标文件存放在相应工程的distrib目录下面。

​        bbossaop\run.bat
​        bbossevent\run.bat
​        bboss-mvc\build.bat
​        bboss-persistent\run.bat
​        bboss-taglib\run.bat
​        bboss-util\run.bat

9.bboss-mvc,bboss mvc 框架隶属于开源项目bbossgroups，是基于bboss aop框架开发的轻量级mvc框架，提供以下功能：单方法action，多方法action，注解action 支持restful

​            提供数据绑定功能提供国际化功能提供自定义主题功能提供一套界面展示标签提供数据自动校验功能,

​            bboss-mvc提供多文件上传功能的支持，分页控制器的支持。能够非常方便地和jquery，exjs等流行的技术框架使用。

10.文档 目录包含mvc框架开发文档、framework 开发文档和bboss aop框架的技术使用文档、ppt培训文档等等

11.bbossgroups-min-eclipse.zip 框架最小依赖eclipse工程demo（mvc，taglib，aop，persistent，object servializable）

mvc：包含mvc，taglib，aop/ioc最小依赖eclipse工程和测试用例

persistent:包含persistent框架最小依赖eclipse工程和测试用例

xmlserializable：包含对象xml序列化技术最小依赖eclipse工程和测试用例  

  bboss group project blog:

http://yin-bp.javaeye.com/

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/

release version : bbossgroups-3.2

release date: 2011/06/12

release futures:  

************************************************bbossgroups-3.2******************************

------2011-06-10------------

o 修正SQLExecutor中泛型字段查询API中的类型转换漏洞

------2011-06-09------------

o 处理空字符串向日期类型转换后台报异常的缺陷

------2011-06-09------------

o 注解控制器可以不用添加注解@Controller也可以被框架识别了

------2011-06-8------------

o 修正不正常的提示信息，externaljndiName

o 修复只配置外部数据源的情况下，pool启动堆栈溢出问题

​    java.lang.StackOverflowError

at java.lang.Thread.currentThread(Native Method)

at org.apache.xerces.util.SecuritySupport12$1.run(Unknown Source)

at java.security.AccessController.doPrivileged(Native Method)

at org.apache.xerces.util.SecuritySupport12.getContextClassLoader(Unknown Source)

at org.apache.xerces.util.ObjectFactory.findClassLoader(Unknown Source)

at org.apache.xerces.impl.dv.DTDDVFactory.getInstance(Unknown Source)

at org.apache.xerces.impl.dv.DTDDVFactory.getInstance(Unknown Source)

at

修改程序清单：

com.frameworkset.common.poolman.sql.PoolMan

com.frameworkset.common.poolman.util.JDBCPool

com.frameworkset.common.poolman.util.SQLManager

com.frameworkset.common.poolman.util.SQLUtil

public static JDBCPool getJDBCPoolByJNDIName(String jndiname)

​        {

​            JDBCPool pool = SQLUtil.getSQLManager().getPoolByJNDIName(jndiname,true);

​            return pool;

​       }

​    public static JDBCPool getJDBCPoolByJNDIName(String jndiname,boolean needcheckStart)

​    {

​        JDBCPool pool = SQLUtil.getSQLManager().getPoolByJNDIName(jndiname,needcheckStart);

​        return pool;

​    }

------2011-06-06------------

o 完善responsebody注解功能,增加datatype和charset两个属性

datatype：json,xml等值，用来指出输出数据的content类型

charset：用来指出reponse响应字符编码

详细使用方法请参考测试用例

org.frameworkset.web.enumtest.EnumConvertController

org.frameworkset.web.http.converter.json.JsonController

------2011-06-04------------

o 逻辑标签可以独立于list和beaninfo标签使用,增加以下属性：

<**attribute>**

<**name>**requestKey<**/name>**

<**rtexprvalue>**true<**/rtexprvalue>**

<**/attribute>**

<**attribute>**

<**name>**sessionKey<**/name>**

<**rtexprvalue>**true<**/rtexprvalue>**

<**/attribute>**

<**attribute>**

<**name>**pageContextKey<**/name>**

<**rtexprvalue>**true<**/rtexprvalue>**

<**/attribute>**

<**attribute>**

<**name>**parameter<**/name>**

<**rtexprvalue>**true<**/rtexprvalue>**

<**/attribute>**

<**attribute>**

<**name>**actual<**/name>**

<**rtexprvalue>**true<**/rtexprvalue>**

<**/attribute>**

通过以上属性，可以方便地制定逻辑标签的期望值，

requestKey：指定从request的attribute属性中获取实际值，

sessionKey：指定从session的attribute属性中获取实际值，

pageContextKey：指定从pageContext的attribute属性中获取实际值，

parameter:指定从request的parameter中获取实际值

actual:直接指定实际值，可以是具体的值，也可以是一个el变量

上述属性还可以和property属性结合起来获取值对象中的属性值

------2011-06-04------------

o request和session标签增加日期dateformat格式属性

------2011-06-03------------

o 修复config标签enablecontextmenu属性不能正常工作的漏洞

------2011-06-2------------

o 修复使用Column注解设置对象属性与列名映射时，导致sql语句中绑定相应的属性变量值失败的漏洞

------2011-05-28-----------  

o 修复quartz引擎加载带参数方法任务时，后台抛空指针异常，这是3.1版本产生的新问题，影响新
版本的使用

o 完善任务调度管理组件org.frameworkset.task.TaskService

完善stopService方法和startService方法，解决停止引擎后，无法启动引擎的问题，增加方法stopService(boolean force)

增加方法，一边在执行public void deleteJob(String jobname, String groupid)方法后重新加载作业

public void startExecuteJob(String groupid, String jobname)

------2011-05-28------------

o 支持Map<Key,PO>类型参数绑定机制，通过这种机制可以非常方便地将表单提交过来的一个对象集合数据
根据key对应的字段值，形成Map索引对象。

使用方法如下：

public String mapbean(@MapKey("fieldName") Map<String,ListBean> beans, ModelMap model) {

String sql = "INSERT INTO LISTBEAN (" + "ID," + "FIELDNAME,"\+ "FIELDLABLE," + "FIELDTYPE," 

"SORTORDER,"\+ " ISPRIMARYKEY," + "REQUIRED," + "FIELDLENGTH,"\+ "ISVALIDATED" + ")" 

"VALUES"

\+ "(#[id],#[fieldName],#[fieldLable],#[fieldType],#[sortorder]"

\+ ",#[isprimaryKey],#[required],#[fieldLength],#[isvalidated])";

TransactionManager tm = new TransactionManager();

try {

if(beans != null)

{

List temp =  convertMaptoList( beans);

tm.begin();

SQLExecutor.delete("delete from LISTBEAN");

SQLExecutor.insertBeans(sql, temp);

tm.commit();

model.addAttribute("datas", temp);

}

} catch (Exception e) {

try {

tm.rollback();

} catch (RollbackException e1) {

// TODO Auto-generated catch block

e1.printStackTrace();

}

// TODO Auto-generated catch block

e.printStackTrace();

}

return "path:mapbean";

}  

详情请参考测试用例：

/bboss-mvc/WebRoot/WEB-INF/bboss-mapbean.xml

------2011-05-26------------

o 新增empty和notempty两个逻辑标签使用方法和null、notnull一样

empty判断指定的字段的值是否是null，或者空串，如果条件成立，则执行标签体中的逻辑

notempty判断指定的字段的值既不是null也不是空串，则执行标签体得内容

------2011-05-24------------

o 修改null和notnull标签不能正确工作的问题

o 修复detail标签的提示信息不是很正确的问题

###################################################################################

------2011-05-20------------

o 修复引用容器datasource漏洞

o 修复通过模板启动数据源参数设置问题

o 修复一系列空指针问题

o 修复使用外部数据源时，当外部数据源是oracle时无法获取数据源的元数据问题,必须制定相应数据库的驱动程序，否则无法初始化正确的dbadapter

------2011-05-20-----------

o 增加rmi服务发布和rmi客服端组件获取功能

发布服务方法，通过 rmi:address指定发布服务的唯一地址：

<property name="rmi_service_test"

rmi:address="rmi_service_test"

class="org.frameworkset.spi.remote.rmi.RMIServiceTest"/>

服务器port的指定在rmi协议配置文件中：

/bbossaop/resources/org/frameworkset/spi/manager-rpc-rmi.xml

<!--

服务器绑定端口

-->
<property name="connection.bind.port" value="1099" 

/>

rmi客服端组件配置和获取：

配置

<property name="rmi_service_client_test" factory-

class="org.frameworkset.spi.remote.rmi.RMIUtil" factory-method="lookupService">

<**construction>**

<property name="servicaddress" value="//172.16.25.108:1099/rmi_service_test"

/>

<**/construction>**

<**/property>**

获取：

BaseApplicationContext context ;

@Before

public void init()

{

context = ApplicationContext.getApplicationContext("org/frameworkset/spi/remote/rmi/rmi-client.xml");

}

@Test

public void test() throws RemoteException

{

RMIServiceTestInf test = (RMIServiceTestInf)context.getBeanObject("rmi_service_client_test");

System.out.println(test.sayHello("多多"));

}

------2011-05-17------------

o 修复sqlexecutor对日期类型timestamp处理丢失时分秒的缺陷

------2011-05-15-----------

o 改进国际化机制，每个组件容器相关的国际化文件必须和组件容器的根配置文件在同级目录，并且名称以下格式命名：

messages_en_US.properties

messages_zh_CN.properties

o 改进sql配置文件刷新机制，将3.1版本中每个sql配置文件定义一个守护进程改为单一守护进程检测机制，即所有的sql文件是否

变更的检测由一个守护进程完成，这个进程中维护一个文件队列，刷新事件配置也由manager-provider.xml文件改为：

/bbossaop/src/aop.properties中配置：

sqlfile.refresh_interval=5000

同时将sql配置文件的管理容器改为SOAFileApplicationContext，以便提升系统性能

------2011-05-11-----------

o 改进ApplicationContext，WebApplicationContext容器，分离出SOAApplicationContext、BaseApplicationContext和DefaultApplicationContext三个类型的容器，他们的职责分别为：

ApplicationContext：和原来的功能ApplicationContext的功能一致，包括基本的aop/ioc功能，远程服务，全局属性管理，拦截器，包含声明式事务管理，是BaseApplicationContext的子类

SOAApplicationContext/SOAFileApplicationContext：两个轻量级的ioc容器，包含全局属性管理，不包含远程服务，拦截器，不包含声明式事务管理，是DefaultApplicationContext的子类

DefaultApplicationContext：和ApplicationContext的区别就是不包含远程服务的功能，和远程服务相关的组件没有依赖关系，是BaseApplicationContext的子类

BaseApplicationContext：是一个抽象容器，所有的上层容器都间接或直接实现了这个容器，提供公共的基础功能。

WebApplicationContext：是mvc框架的控制器和业务组件管理容器，是DefaultApplicationContext的子类，拥有DefaultApplicationContext的所有功能。

------2011-05-11------------

o 完善restful实现机制，可以支持对控制器方法级别的restful全地址映射关系并修复了部分缺陷，3.0版本需要根据类级别和方法级别地址组合才能实现

  restful风格的功能，而且存在缺陷。

o aop框架中实现对象与xml相互转化功能

  ------2011-05-06------------

o 修复循环依赖机制漏洞

o 修复quartz任务调度导致模块中没有配置任何任务时，后台抛出类型转换异常

o 修复3.0版本中没有将组件实例机制定义为单例模式的漏洞，但是ppt培训教程中却明确指出该版本中的组件默认为单例模式

上述3个漏洞修复的程序为：

/bbossaop/src/org/frameworkset/spi/assemble/BeanAccembleHelper.java

/bbossaop/src/org/frameworkset/spi/assemble/Pro.java

/bbossaop/src/org/frameworkset/spi/assemble/ProviderParser.java  

  ------2011-05-06------------

o 修复分页标签偶尔找不到vm模板文件的漏洞

上述漏洞修复的程序为：

/bboss-util/src/com/frameworkset/util/VelocityUtil.java

------2011-05-03------------

o 修复在事务环境下通过JDBCPool的方法

public TableMetaData getTableMetaDataFromDatabase(Connection con,
String tableName)

获取不到特定数据源的表元数据的问题

------2011-05-03------------

o 修复preDBUtil.preparedSelect(params, dbName, sql, offset, long)

在查询没有数据的情况下，preDBUtil.getMeta()返回的是null；


各子项目新增功能和修改功能清单请参考每个项目中的readme.txt文件。  