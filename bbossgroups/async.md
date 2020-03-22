### bbossgroups-3.1 发布，支持组件方法异步调用

bbossgroups-3.1 发布，新增组件异步调用功能，对mvc框架功能做了进一步的改进，丰富了数据绑定机制，标签库修复了jquery ajax提交和装载页面中文乱码问题

整个框架相比以前的版本性能更好，更加简单易用。为了更好地帮助开发人员的使用，提供了以下资源：

bbossgroups源码工程（http://sourceforge.net/projects/bboss/files/bbossgroups-3.1/bbossgroups-3.1-src.zip/download）

mvc框架的demo war包（http://sourceforge.net/projects/bboss/files/bbossgroups-3.1/mvcdemo-war.zip/download）

简单mvc eclipse开发工程，开箱即用（http://sourceforge.net/projects/bboss/files/bbossgroups-3.1/mvcdemo-eclipse.zip/download）

bbossgroups 培训ppt（[http://sourceforge.net/projects/bboss/files/bbossgroups-3.1/bbossgroups%203.1%20in%20action.zip/download](http://sourceforge.net/projects/bboss/files/bbossgroups-3.1/bbossgroups 3.1 in action.zip/download)）

bbossgroups 包含以下子工程:

1.bboss-aop, an aop framework.(ioc ,rpc[jms,mina,jgroups,cxf webservice,rmi,netty,rest,组播，多播],
    bean component,cxf webservice component framworkset，jms components frameworkset,plugin security components and so on).

支持组件方法异步调用。

2.bboss-persistent, a persistent framework().

a.灵活的事务管理（声明式事务管理，可编程事务管理，java注解事务管理，jdbctemplate事务管理，五种经典的事务类型，支持事务嵌套，支持多数据库分布式事务）

b.灵活的访问数据库的接口（普通sql操作，预编译sql操作，普通/预编译批处理操作，存储过程，函数）

c.一套经典的数据库操作标签库（增删改查，普通sql操作，预编译sql操作，普通/预编译批处理操作）

d.经典的多数据库连接池配置管理和使用方法（所有的数据库操作接口可以直接指定连接池的名称，方便地实现对不同数据库的操作）

3.bboss-taglib, a web layer taglib framework(list tag,pageine list tag,detail tag ,logic tag,tree tag,tabpane tag,dbutil tag).

4.bboss-event, an event framework(local event,remote distribute event framework base aop rpc framework).

5.bboss-util, an utility framework.

6.antbuildall, ant build project that build up projects.可以运行antbuildall下的run.bat命令编译所有的子项目，并且更新相应工程的依赖jars。

7.apache-ant-1.7.1 所有工程构建依赖的ant环境，bbossgroups的构建无需依赖外部ant环境，每个子工程下都有相应的执行ant的bat命令文件，直接运行这些bat就可以构建相应的工程，构建的目标文件存放在相应工程的distrib目录下面。

​            bbossaop\run.bat

​            bbossevent\run.bat

​            bboss-mvc\build.bat

​            bboss-persistent\run.bat

​            bboss-taglib\run.bat

​            bboss-util\run.bat

8.bboss-mvc,bboss mvc 框架隶属于开源项目bbossgroups，是基于bboss aop框架开发的轻量级mvc框架，提供以下功能：单方法action，多方法action，注解action 支持restful，提供数据绑定功能提供国际化功能提供自定义主题功能提供一套界面展示标签提供数据自动校验功能,bboss-mvc提供多文件上传功能的支持，分页控制器的支持。能够非常方便地和jquery，exjs等流行的技术框架使用。

9.文档 目录包含mvc框架开发文档、framework 开发文档和bboss aop框架的技术使用文档、ppt培训文档等等  

  bboss group project blog:

http://blog.csdn.net/yin_bp

http://yin-bp.iteye.com/

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/

release version : bbossgroups-3.1

release date: 2011/05/02

-----------------------------------------------------------------------------------------------

release futures:

3.1 版本新增组件异步调用功能，对mvc框架功能做了进一步的改进，丰富了数据绑定类型，标签库修复了jquery ajax提交和装载页面中文乱码问题，整个框架相比以前的版本性能更好，更加简单易用。为了更好地帮助开发人员的使用，提供了以下资源：

mvc框架的demo war包

简单mvc eclipse开发工程，开箱即用

------2011-04-30------------

o mvc框架绑定参数注解指定日期转换格式，以便保证按原始数据格式将参数转换为正确的日期

o mvc框架可以将日期类型(java.util.Date/java.sql.Date/java.sql.Timestamp)转换为long类型数据，也可以将long数据转换为日期类型(java.util.Date/java.sql.Date/java.sql.Timestamp)，也可以进行long数组和日期类型(java.util.Date/java.sql.Date/java.sql.Timestamp)数组的相互转换

o 修复mvc框架控制器组件解析异常：

java.lang.IllegalArgumentException: Class must not be null

at org.frameworkset.util.Assert.notNull(Assert.java:112)

at org.frameworkset.util.annotations.AnnotationUtils.findAnnotation(AnnotationUtils.java:129)

at org.frameworkset.web.servlet.handler.HandlerUtils.determineUrlsForHandler(HandlerUtils.java:1965)

------2011-04-25------------

o 持久层框架中修复获取数字类型的值时，一旦rs中返回null时，没有正确获取数字缺省值的问题
ValueExchange.convert方法

o 标签库中修改字符串过滤器，解决jquery或者ajax数据请求时，分页查询的中文乱码问题，改进字符串过滤器的性能

------2011-04-24------------

o 改进webservice服务装载功能，可以从mvc和所有的applicationcontext中配置和装载webservice服务：

Mvc框架的ws服务无需特殊处理

普通的applicationcontext容器中的ws服务对应的模块配置文件需要配置org/frameworkset/spi/ws/webserivce-modules.xml文件中

------2011-04-21------------

o 增加组件异步调用机制，使用方法参考测试用例：

/bbossaop/test/org/frameworkset/spi/asyn/AsynbeanTest.java

可以通过Async注解标注组件中需要异步执行的方法即可，可以指定超时时间，是否需要返回结果，是否需要回调处理返回结果

------2011-04-20------------  

o 完善Pro对象对ProList，ProSet，ProMap，ProArray的处理机制

o ApplicationContext组件新增一组获取ProArray对象的接口

public ProArray getArrayProperty(String name) ;

public ProArray getProArrayProperty(String name, ProArray defaultValue) ;

o 新增convert标签，支持字典数据值向名称的转换

其中的datas为一个map属性映射值，name对应于key，convert标签通过name获取到对应的属性值
然后显示到页面上，如果对应的值没有那么输出defaultValue对应的值，如果没有设置defaultValue
那么直接输出name。

<pg:convert convertData="datas" colName="name" defaultValue=""/>

pager-taglib.tld

frameworkset.jar

------2011-04-18------------  

o 解决获取空的ProList时导致aop框架启动失败的问题

o 完善事务泄露检测机制，在manager-provider.xml中增加检测页面地址类型配置：

<!-- 数据库事务泄露检测url类型范围配置 -->

<**property name="transaction.leakcheck.files">** 

<**array componentType="String">**

<**property value=".jsp"/>**

<**property value=".do"/>**

<**property value=".page"/>**

<**property value=".action"/>**

<**property value=".ajax"/>**

<**/array>**

<**/property>**

------2011-04-16------------

o 完善带返回值的事务管理模板组件支持泛型类型的返回

public void stringarraytoList(final List<**ListBean>** beans) throws Throwable {

List<**ListBean>** ret = TemplateDBUtil.executeTemplate(

new JDBCValueTemplate<List<**ListBean>**>(){

public List<**ListBean>**  execute() throws Exception {

String sql = "INSERT INTO LISTBEAN (" + "ID," + "FIELDNAME,"

"FIELDLABLE," + "FIELDTYPE," + "SORTORDER,"

" ISPRIMARYKEY," + "REQUIRED," + "FIELDLENGTH,"

"ISVALIDATED" + ")" + "VALUES"

"(#[id],#[fieldName],#[fieldLable],#[fieldType],#[sortorder]"

",#[isprimaryKey],#[required],#[fieldLength],#[isvalidated])";

SQLExecutor.delete("delete from LISTBEAN");

SQLExecutor.insertBeans(sql, beans);

return beans;

}

}

);

}

o 解决主页面通过ajax方式加载多个分页页面时，跳转功能不能正常使用的问题，以及提示信息中文乱码问题

------2011-04-14------------

o mvc中传递给分页标签的导航路径修改为带上下文的绝对地址，以免在使用jquery模式局部分页时，主页面的相对地址和分页对应的页面的相对路径不一致时，不能正确地进行分页导航

------2011-04-13------------

o 控制器方法中增加Map类型参数绑定机制，可以将request中的参数转换为Map对象，当参数是数组时存入数组值，否则存入单个值

------2011-04-11------------

o 完善ConfigSQLExecutor和SQLExecutor组件中所有和bean对象相关的接口，Object bean参数可以是普通的的值对象，也可以是一个SQLParams对象,也可以是一个Map对象

使用方法参考测试用例：

/bboss-persistent/test/com/frameworkset/sqlexecutor/ConfigSQLExecutorTest.java  

------2011-04-11------------

o 新增array元素，通过该元素可以实现各种类型数组数据的注入功能

------2011-04-07------------

o  修改DaemonThread进程，支持从外部指定刷新文件资源的时间间隔。

o 完善ApplicationContext组件的生命周期管理机制

o ApplicationContext组件增加获取long值属性的api

------2011-04-11------------

o 新增array元素，通过该元素可以实现各种类型数组数据的注入功能

------2011-04-07------------

o 增加根据变量名称从配置文件中获取sql语句的来操作数据库组件,对应sql配置文件提供定时刷新机制
  如果检测到sql文件被修改，就从新加载文件（前提是开启刷新机制）

com.frameworkset.common.poolman.ConfigSQLExecutor

具体的使用方法为：

ConfigSQLExecutor executor = new ConfigSQLExecutor("com/frameworkset/sqlexecutor/sqlfile.xml");

Map dbBeans  =  executor.queryObject(HashMap.class, "sqltest");

String result = executor.queryFieldBean("sqltemplate", bean);

配置文件：

<**?xml version="1.0" encoding='gb2312'?>**

<**properties>**

<**description>**

<!

[CDATA[

sql配置文件

可以通过名称属性name配置默认sql，特定数据库的sql通过在

名称后面加数据库类型后缀来区分，例如：

sqltest

sqltest-oracle

sqltest-derby

sqltest-mysql

等等，本配置实例就演示了具体配置方法

]]>

<**/description>**

<**property name="sqltest">**

<**![CDATA[select * from LISTBEAN]]>**

<**/property>**

<**property name="sqltest-oracle">**

<**![CDATA[select * from LISTBEAN]]>**

<**/property>**

<**property name="sqltemplate">**

<!

[CDATA[select FIELDNAME from LISTBEAN where FIELDNAME=#[fieldName]]]>

<**/property>**

<**property name="sqltemplate-oracle">**

<**![CDATA[select FIELDNAME from LISTBEAN where FIELDNAME=#[fieldName]  ]]>**

<**/property>**

<**property name="dynamicsqltemplate">**

<!

[CDATA[select *  from CIM_ETL_REPOSITORY  where 1=1#if($HOST_ID && !$HOST_ID.equals("")) and HOST_ID = #[HOST_ID] #end and PLUGIN_ID = #[PLUGIN_ID] and CATEGORY_ID = #[CATEGORY_ID] and APP = #[APP]]]>

<**/property>**

<**/properties>**

  刷新机制的配置方法：

在manager-provider.xml文件中添加以下配置项即可：

<property name="sqlfile.refresh_interval" value="10000"

/>

当value大于0时就开启sqlfile文件的更新检测机制，每隔value指定的时间间隔就检测一次，有更新就重新加载，否则不重新加载

o 完善ApplicationContext组件的生命周期管理机制

o ApplicationContext组件增加获取long值属性的api

o 完善mvc框架配置文件导入方式

可以用,号分隔导入子目录下的配置文件,例如：  

<**servlet>**

<**servlet-name>**mvcdispather<**/servlet-name>**

<**servlet-class>**org.frameworkset.web.servlet.DispatchServlet<**/servlet-class>**

<**init-param>**

<**param-name>**contextConfigLocation<**/param-name>**

<**param-value>**/WEB-INF/bboss-*.xml,/WEB-INF/conf/bboss-*.xml<**/param-value>**

<**/init-param>**

。。。。。。

<**/servlet>**

------2011-04-05------------

o 控制器方法增加枚举类型，枚举数组类型参数的绑定功能

------2011-04-06------------

o 增加一组查询单个字段的泛型接口，使用方法如下：

String sql = "select REQUIRED from LISTBEAN ";

int id=  SQLExecutor.queryTField(int.class, sql);

long id=  SQLExecutor.queryTField(long.class, "select seq_name.nextval from LISTBEAN ");

String sql = "select FIELDLABLE from LISTBEAN ";

String id=  SQLExecutor.queryTField(String.class, sql);

System.out.println(id);

o 3.0api增加返回List<**HashMap>**结果集的查询接口支持，使用方法如下（以预编译语句为例）：

@Test  

  public void queryListMap() throws SQLException

{

String sql = "select * from LISTBEAN name=?";

List<**HashMap>** dbBeans  =  SQLExecutor.queryListWithDBName(HashMap.class, "mysql", sql,"ttt");

System.out.println(dbBeans);

}

public void queryListMapWithbeanCondition() throws SQLException

{

String sql = "select * from LISTBEAN name=#[name]";

ListBean beanobject = new ListBean();

beanobject.setName("duoduo");

List<**HashMap>** dbBeans  =  SQLExecutor.queryListWithDBName(HashMap.class, "mysql", sql,beanobject);
System.out.println(dbBeans);

}  

@Test

public void queryMap() throws SQLException

{

String sql = "select * from LISTBEAN ";

Map dbBeans  =  SQLExecutor.queryObject(HashMap.class, sql);

System.out.println(dbBeans);

}

------2011-03-31------------

o 跳转路径可以通过path：元素直接指定，而无需注入

具体使用方法，参考demo

WebRoot/WEB-INF/bboss-path.xml

------2011-03-30------------

o 3.0api中完善对java.util.Date类型对象属性数据的处理

o 修复mvc实现分页功能时，通过handleMapping注解指定的url路径无法进行分页的bug，修改的程序如下：

o ioc中属性注入时，如果属性没有定义set方法，会抛出异常，导致类注入初始化失败，修改为提示而不是失败方式

  ------2011-03-20------------

o 改进右键菜单功能，提升右键菜单性能，涉及的功能有：使用右键菜单的树标签和使用右键菜单的列表、分页标签，以及所有其他相关的页面

o 修复jms不能发送长度为0的jms消息bug

/bbossaop/src-jms/org/frameworkset/mq/RequestDispatcher.java

------2011-03-10------------

o 将组件管理模式默认为设置为单例模式

------2011-03-09------------    

o 扩展list,map,set元素类型定义，添加componentType属性，用来标识容器中存放的对象类型，

componentType的取值范围如下：

bean：标识容器元素对象类型是组件对象类型

String：标识容器元素对象类型是String对象

该属性可以用来方便将组件类型的list和字符串类型的list注入到其他组件中。  

相应地在ProList、ProMap、ProSet对象上增加了以下方法：

ProList： public List getComponentList()

ProMap：public Map getComponentMap()

ProSet：public Set getComponentSet()

使用案例如下：  

<property name="/index.htm,/detail.htm"      f:demo_sites="attr:demo_sites"    class="org.frameworkset.web.demo.SiteDemoController" singlable="true"/>  

<**property name="demo_sites">**

<**list componentType="bean">**

<property f:name="listbean"f:cnname="集合po对象绑定实例“

class="org.frameworkset.web.demo.SiteDemoBean">

<property name="controllerClass"

 value="D:/workspace/bbossgroup-2.0-RC2-mvc/bboss-mvc/test/org/frameworkset/spi/mvc/ListBeanBindController.java"/>

<property name="configFile" 

value="D:/workspace/bbossgroup-2.0-RC2-mvc/bboss-mvc/WebRoot/WEB-INF/bboss-listbean.xml"/>

<**property name="visturl">**

<**list componentType="String">**

<**property value="/databind/showstringarraytoList.htm"/>**

<**property value="/databind/showlist.htm"/>**

<**/list>**

<**/property>**

<**property name="formlist">**

<**list  componentType="bean">**

<property f:formPath="D:/workspace/bbossgroup-2.0-RC2-mvc/bboss-mvc/WebRoot/jsp/databind/table.jsp"f:charset="UTF-8"class="org.frameworkset.web.demo.FormUrl">

<**property name="description">**

<!

[CDATA[表单table.jsp, 对应于/databind/showlist.htm跳转页面]]>

<**/property>**

<**/property>**

<property f:formPath="D:/workspace/bbossgroup-2.0-RC2-mvc/bboss-mvc/WebRoot/jsp/databind/stringarraytoList.jsp"class="org.frameworkset.web.demo.FormUrl">

<**property name="description">**

<!

[CDATA[表单stringarraytoList.jsp, 对应于/databind/showstringarraytoList.htm跳转页面]]>

<**/property>**

<**/property>**

<property f:formPath="D:/workspace/bbossgroup-2.0-RC2-mvc/bboss-mvc/WebRoot/jsp/databind/tableinfo.jsp"class="org.frameworkset.web.demo.FormUrl">

<**property name="description">**

<!

[CDATA[表单/bboss-mvc/WebRoot/jsp/databind/tableinfo.jsp, 对应于/databind/showbean.htm跳转页面]]>

<**/property>**

<**/property>**

<**/list>**

<**/property>**

<**property name="description">**

<**![CDATA[集合po对象绑定实例 字符串数组转List数据绑定实例]]>**

<**/property>**

<**/property>**

<**/list>**

<**/property>**

类如下：SiteDemoController

public class SiteDemoController {

private List<**SiteDemoBean>** demo_sites;

public String index(ModelMap model)

{

model.addAttribute("demobeans", demo_sites);

return "index";

}  

  public String detail(ModelMap model,@RequestParam(name="demoname") String demoname)

{

SiteDemoBean bean = null;

for(int i = 0; i < demo_sites.size(); i ++)

{

SiteDemoBean bean_ = demo_sites.get(i);

if(demoname.equals(bean_.getName()))_

{

{

bean = bean_;

break;

}

}

model.addAttribute("demobean", bean);

return "seconddetail";

}

  /**

\* @return the demo_sites

*/

public List<**SiteDemoBean>** getDemo_sites() {

return demo_sites;

}

/**

\* @param demoSites the demo_sites to set

*/

public void setDemo_sites(List<**SiteDemoBean>** demoSites) {

demo_sites = demoSites;

}

}    

------2011-03-30------------

o 3.0api中完善对java.util.Date类型对象属性数据的处理

------2011-03-06------------

o 修复树标签复选框点击事件firefox兼容性问题

o 修复树标签默认选中节点上面设置点击事件时Boolean值向String转换异常问题

------2011-03-04------------

o 增加一个根据参数启动数据源的api，可以控制数据源是连接池数据源还是非连接池数据源
DBUtil中增加以下静态方法：

public static void startPool(String poolname,String driver,String jdbcurl,String username,String password,

​        String readOnly,

​        String txIsolationLevel,

​        String validationQuery,

​        String jndiName,  

​        int initialConnections,

​        int minimumSize,

​        int maximumSize,

​        boolean usepool,

​        boolean  external,

​        String externaljndiName   


​        )

o 数据源配置文件中增加usepool元素 ，可以控制数据源是连接池数据源还是非连接池数据源

<?xml version="1.0" 

encoding="gb2312"?>

<**poolman>**

<**datasource>**

<**dbname>**bspf<**/dbname>**

<**loadmetadata>**false<**/loadmetadata>**

<**jndiName>**jdbc/mysql-ds<**/jndiName>**

<**driver>**oracle.jdbc.driver.OracleDriver<**/driver>**

<**usepool>**false<**/usepool>**

<**url>**jdbc: oracle: thin: @//172.16.25.139:1521/dtjf<**/url>**

<**username>**dtjf<**/username>**

<**password>**dtjf<**/password>**

<**txIsolationLevel>**READ_COMMITTED<**/txIsolationLevel>**

<**logAbandoned>**true<**/logAbandoned>**

<**readOnly>**false<**/readOnly>**

<**keygenerate>**composite<**/keygenerate>**

<**autoprimarykey>**false<**/autoprimarykey>**

<**showsql>**false<**/showsql>**

<**/datasource>**

<**/poolman>**      

  o 调整非连接池数据源的监控数据采集和相关属性展示接口

------2011-03-02------------

o 修改odbc 驱动下使用o/r mapping查询，大字段处理异常问题

o 扩展SQLExecutor组件增加所有查询List结果集方法对泛型的支持

o 修复double类型数据向int类型转换的问题，新增单个值转换为数组的功能，支持数字类型数组之间的相互转换

各子项目新增功能和修改功能清单请参考每个项目中的readme.txt文件。  