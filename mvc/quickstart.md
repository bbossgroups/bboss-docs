# bboss mvc快速入门教程 



# **基于bboss开发项目说明**

要做简单的demo，请参考文档
http://yin-bp.iteye.com/blog/1026261

正儿八经的做项目，参考文档搭bboss平台开发环境

**普通ui版平台：**
http://yin-bp.iteye.com/blog/2390050

**bootstrap版平台：**

http://yin-bp.iteye.com/blog/2356672

自动代码生成工具使用，参考文档：
http://yin-bp.iteye.com/blog/2256948

  如需使用bboss中某个模块，那么这个地方可以找到各模块最小依赖gradle工程，你可以直接在此基础上开启bboss框架开发之旅：
[bboss最佳实践案例](https://github.com/bbossgroups/bestpractice)

最新版本bboss构建指南:《[采用gradle构建和发布bboss方法介绍](http://yin-bp.iteye.com/blog/2295166)》
bboss 工程模块目录一览表


**|--bboss-core**  

bboss ioc、序列化核心工程,构建指令：gradle install

**|--bboss-event** 

bboss分布式事件工程,构建指令：gradle install
**|--bboss-hession** bboss hessian服务发布工程,构建指令：gradle install

**|--bboss-jodconverter-core**

bbossword文档处理jodconverter插件工程,构建指令：gradle install

**|--bboss-mvc** 

bboss mvc工程,,构建指令：gradle install

**|--bboss-persistent**

bboss持久层框架工程,,构建指令：gradle install

**|--bboss-plugin-hibernate**

bboss hibernate事务托管工程,构建指令：gradle install

**|--bboss-plugin-wordpdf**

bbossword文档处理jodconverter插件web工程,构建指令：gradle install

**|--bboss-rpc**

bboss rpc框架工程,,构建指令：gradle install

**|--bboss-schedule**

bboss quartz任务管理工程,构建指令：gradle install

**|--bboss-security**

  bboss会话共享和令牌管理核心工程,构建指令：gradle install

**|--bboss-security-web**

bboss会话共享监控服务和令牌服务工程,构建指令：gradle install

**|--bboss-soa**

  bboss序列化核心工程,构建指令：gradle install

**|--bboss-taglib**

bboss标签库工程,构建指令：gradle install

**|--bboss-util** 

bboss工具包工程,构建指令：gradle install

**|--bboss-velocity**

  bboss版velocity引擎（针对持久层）,构建指令：gradle install

**|--bboss-data**

  bboss版redis和mongodb操作组件,构建指令：gradle install

**|--bboss-websocket**

  bboss版websocket服务发布组件,构建指令：gradle install

**|--bboss-schedule**

  bboss版quartz定时任务组件,构建指令：gradle install
**|--bestpractice**
已经被迁移到独立github项目,构建指令：gradle install

具体demo 工程说明请参考文档：[bboss最佳实践eclipse工程清单及其作用介绍](http://yin-bp.iteye.com/blog/2122876)
bboss demos源码github托管地址：
https://github.com/bbossgroups/bestpractice
svn下载地址
https://github.com/bbossgroups/bestpractice/trunk

**|--database**  
已经被迁移到独立bboss 文档github项目
持久层demo依赖的derby文件数据库存放目录
https://github.com/bbossgroups/bboss-document/tree/master/database

**|--文档**
存放bboss的文档,已经被迁移到独立bboss 文档github项目
[https://github.com/bbossgroups/bboss-document/tree/master/%E6%96%87%E6%A1%A3](https://github.com/bbossgroups/bboss-document/tree/master/文档)

源码下载地址：


**bboss 源码github托管地址：**
https://github.com/bbossgroups/bboss
svn下载地址
https://github.com/bbossgroups/bboss/trunk

**bboss 会话共享源码github托管地址：**
https://github.com/bbossgroups/security
svn下载地址
https://github.com/bbossgroups/security/trunk

**bboss demos源码github托管地址：**
https://github.com/bbossgroups/bestpractice
svn下载地址
https://github.com/bbossgroups/bestpractice/trunk
**基于bboss的开源工作流Activiti5.12 github托管地址**
https://github.com/yin-bp/activiti-engine-5.12
svn下载地址
https://github.com/yin-bp/activiti-engine-5.12/trunk

**自动代码生成框架github源码托管地址和svn下载地址：**
github源码托管地址
https://github.com/bbossgroups/bboss-gencode
svn下载地址
https://github.com/bbossgroups/bboss-gencode/trunk

**bboss大数据抽取工具db-hdfs github托管地址**
https://github.com/bbossgroups/bigdatas
svn下载地址
https://github.com/bbossgroups/bigdatas/trunk

**bboss设计相关文档托管地址**
https://github.com/bbossgroups/bboss-document
svn下载地址
https://github.com/bbossgroups/bboss-document/trunk

除了采用[github clone](https://github.com/bbossgroups/bboss.git)或者[下载](https://github.com/bbossgroups/bboss/archive/master.zip)压缩包的模式，大家还可以选择性地用svn checkout里面每个核心gradle工程，checkout 核心工程svn地址分别为：
https://github.com/bbossgroups/bboss/trunk/antbuildall
https://github.com/bbossgroups/bboss/trunk/apache-ant-1.7.1 (这个要先下载，因为是所有工程构建依赖的ant环境)
https://github.com/bbossgroups/bboss/trunk/bboss-core
https://github.com/bbossgroups/bboss/trunk/bboss-hession
https://github.com/bbossgroups/bboss-plugins/trunk/bboss-jodconverter-core
https://github.com/bbossgroups/bboss/trunk/bboss-mvc
https://github.com/bbossgroups/bboss/trunk/bboss-persistent
https://github.com/bbossgroups/bboss-plugins/trunk/bboss-plugin-hibernate
https://github.com/bbossgroups/bboss-plugins/trunk/bboss-plugin-wordpdf
https://github.com/bbossgroups/bboss-rpc/trunk
https://github.com/bbossgroups/bboss/trunk/bboss-schedule
https://github.com/bbossgroups/security/trunk/bboss-security-web
https://github.com/bbossgroups/security/trunk/bboss-security
https://github.com/bbossgroups/bboss/trunk/bboss-soa
https://github.com/bbossgroups/bboss/trunk/bboss-taglib
https://github.com/bbossgroups/bboss/trunk/bboss-util
https://github.com/bbossgroups/bboss/trunk/bboss-velocity
https://github.com/bbossgroups/bboss/trunk/bbossevent

bboss原始文档和demo database下载
https://github.com/bbossgroups/bboss-document/trunk/database  （这个是derby数据库，mvcdemo会用到）
https://github.com/bbossgroups/bboss-document/trunk/文档

同样也可通过参考上面的地址用svn checkout 特定模块 bboss demo工程。
checkout bboss demo gradle工程的svn地址清单：
https://github.com/bbossgroups/bestpractice/trunk/mvc
https://github.com/bbossgroups/bestpractice/trunk/persistent
https://github.com/bbossgroups/security/trunk/session
https://github.com/bbossgroups/security/trunk/sessionmonitor
https://github.com/bbossgroups/bestpractice/trunk/xmlrequest
https://github.com/bbossgroups/bestpractice/trunk/xmlserializable
https://github.com/bbossgroups/bestpractice/trunk/easyuidatagrid
https://github.com/bbossgroups/bestpractice/trunk/demoproject
https://github.com/bbossgroups/bestpractice/trunk/bbossupload
https://github.com/bbossgroups/bestpractice/trunk/bboss-clientproxy

bboss构建请参考：[采用gradle构建和发布bboss方法介绍](http://yin-bp.iteye.com/blog/2295166)

bboss项目特色参考文档：http://yin-bp.iteye.com/blog/1080824  

# bboss mvc快速入门教程

本文介绍内容：快速搭建使用bboss mvc框架的eclipse工程，然后编写并运行一个简单实例

**1.首先准备好eclipse,并安装好gradle sts插件**  

**2.从github下载demo gradle工程：**https://github.com/bbossgroups/bestpractice或者通过svn下载，svn地址为：https://github.com/bbossgroups/bestpractice/trunk

**3.demo 开发环境搭建，生成eclise工程**


进入命令行模式cd d:/workspace/bestpractice

这样你就搭建好一个完整的demoproject开发环境了，接下来我们需要开发我们的第一个mvc框架示例，我们可以通过eclipse jetty插件来运行和调试这个示例，调试之前首先要在eclipse中按照好jetty插件。

**4.开发自己的第一个mvc例子**

1).新建控制器类web.BbossTestd:/workspace/demoproject/src/web/BbossTest.javaBbossTest编写控制器方法testBboss：

```java
package web;

import org.frameworkset.web.servlet.ModelAndView;

public class BbossTest {	
	public String testBboss(ModelMap model){
                Map data = new HashMap();
                data.put("id","aaaaa");
                data.put("name","dudu0");
                model.addAttribute("data", data);
		return "path:view";
	}
}
```

2).控制器类写好后就可以写相应的，新建xml文件bboss-test.xml存放在以下目录：d:/workspace/demoproject/WebRoot/WEB-INF/conf/bboss-test.xml，内容为

```xml
<?xml version="1.0" encoding='utf-8'?>
<!-- 
bboss-test.xml
描述：demo配置文件
-->
<properties>
    <property name = "/test/*.page" class="web.BbossTest"  path:view="/index1.jsp"/>
</properties>
```

这里需要说明的就是name = "/test/*.page"，部分指定了控制器对应的url映射规则，*号对应控制器web.BbossTest中的方法名，class="web.BbossTest" 指定了控制器类，singlable="true" 部分标识了该控制器为单例模式，path:view="/index1.jsp"指定了别名path:view对应的实际jsp页面，控制器方法跳转时需要用到


配置文件写好后需要配置到web.xml的mvc dispatcher中的contextConfigLocation中，这样bboss mvc框架才会加载这个控制器：

```xml
<servlet>
		<servlet-name>mvcdispather</servlet-name>
		<servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<!--如果有多个目录需要加载，请用,号分隔-->
			<param-value/WEB-INF/conf/bboss-*.xml</param-value>
		</init-param>
		<load-on-startup>0</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>mvcdispather</servlet-name>
		<url-pattern>*.page</url-pattern>
	</servlet-mapping>
	
```

3).编写jsp页面index1.jsp，存放的地址为：d:/workspace/demoproject/WebRoot/index1.jsp内容为：

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>

<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    
    
    <title>My JSP 'index.jsp' starting page</title>
	<meta http-equiv="pragma" content="no-cache">
	<meta http-equiv="cache-control" content="no-cache">
	<meta http-equiv="expires" content="0">    
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
	<meta http-equiv="description" content="This is my page">

  </head>
  
  <body>
  ID:${data.id}<br/>
  ID:${data.name}
  </body>
</html>

```

  4).这样你的例子就做好了，编译一下工程：

gradle war

这样就可以在将war包部署到tomcat即可：
d:/workspace/demoproject/build/libs/demoproject-4.10.8.war

5).启动tomcat，在浏览器重输入以下地址：
http://localhost:8080/demoproject/test/testBboss.page
即可看到你的例子的效果哦。

同时你也可以访问demo中内置的实例：
http://localhost:8080/demoproject/examples/index.page
http://localhost:8080/demoproject/file/fileupload.page

6).如果我们需要对demo进行调试，可以参考文档：

http://yin-bp.iteye.com/blog/2327708

到此，搭建bboss mvc demo开发工程过程和开发例子过程就介绍完了。  