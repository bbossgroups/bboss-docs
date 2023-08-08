### bboss 3.6发布，丰富的功能扩展和改进

[bboss 3.6](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.0) （[官网下载](http://www.bbossgroups.com/file/download.htm?fileName=bbossgroups-3.6.0.zip)，[sf下载](http://sourceforge.net/projects/bboss/files/latest/download?source=files)）发布了，新版本相比之前的版本有了更长足的功能扩展和改进，主要有以下方面（更详细的信息请参阅[release note](https://github.com/bbossgroups/bbossgroups-3.5/blob/bboss3.6.0/README.txt)或者[bboss 博客](http://yin-bp.iteye.com/)）：

1.bboss mvc增加动态令牌机制，有效防止表单重复提交和网站跨站攻击

2.bboss mvc增加word文档、word文档转pdf插件

3.完善bboss mvc文档下载插件

4.完善bboss mvc国际化机制

5.完善控制器方法解析算法，排除属性的get/set方法，排斥标注了ExcludeMethod注解的方法，增强系统安全性

6.mvc控制器方法响应插件MappingJacksonHttpMessageConverter支持jsonp数据响应（跨站跨域通讯协议）

7.改进mvc控制器方法响应插件 StringHttpMessageConverter，增加responseCharset属性，用于全局指定@ResponseBody String类型响应的字符编码

8.改进sql语句管理组件SQLUtil，解析sql配置文件时去掉sql语句前后的空格

9.持久层事务管理TransactionManager组件增加release方法，应用程序在final方法中调用，用来在出现异常时对事务资源进行回收，首先对事务进行回滚，然后回收资源，如果事务没有开启或者已经提交或者已经回滚，则release方法不做任何操作

10.持久层内置数据源apache common dbcp升级到1.4版本，apache common pool升级到1.5.4，同时保持对jdk 1.5 的兼容支持，同时支持在jdk 1.5和jdk 1.6下进行编译和打包 

11.持久层数据源配置文件的datasource元素增加datasourceFile子元素，用来指定定义数据源的ioc配置文件（基于bboss ioc框架），使得持久层可以方便地外接第三方数据源（apache dbcp，proxool,c3p0,Druid等数据源）

12.扩展持久层事务管理框架，提供全局事务管理功能，可以方便地托管和整合bboss/ibatis/mybatis/hibernate等持久层框架事务

13.持久层数据源配置文件的datasource元素增加**<enablejta**>true**</enablejta**>属性配置,使得相应的数据源具备全局事务特性

14.持久层增加对datasource配置文件中对账号和密码的同时加密插件

15.改进持久层模板sql变量解析机制，将正则表达式机制切换为bboss自带的变量解析机制，支持以下类型变量：

基本数据类型

日期类型

上述类型组合复杂类型如下：

   数组（一维数组，多维数组）

List

Map

16.完善sql变量bean类型变量属性引用功能

17.标签库基础类BaseTag和BaseBodyTag实现TryCatchFinally接口

18.逻辑比较标签改进，除了进行字符串比较外还能进行数字和日期类型比较

19.修复notempty标签当collection集合元素为0时不能正常工作的缺陷

20.treedata标签增加rootNameCode属性，用来指定树根节点名称的国际化代码

21.分页头titile标签增加titlecode属性，用来指定分页头标题的国际化代码

22.tabPane标签增加tabTitleCode属性，用来指定tabPane名称的国际化代码

23.优化COMTree和DataInfoImpl中获取accesscontrol安全访问控制对象的方法，提升性能

24.修改在非jquery模式下index标签设置tagnumber属性后，相应的页码上面没有超链接的bug
<pg:index tagnumber="5" sizescope="10,20,50,100"/>

25.改进beaninfo，list标签异常处理方式，将系统级异常输出到日志文件中，日志级别为info级

26.标签库convert标签改进，支持各种类型的key，之前只支持String类型的key，现在支持数字等类型的key

27.[bboss3.6.0分支](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.0)相对于之前的分支版本（[bboss3.5.1分支](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.5.1)和[master分支](https://github.com/bbossgroups/bbossgroups-3.5))的一个变化：

cms.jar中程序包路径

com.bboss更换为

com.frameworkset.platform

这样就和bboss-cms工程中的内容管理标签库保持一致，bboss3.5.1分支和master分支任然保留对com.bboss的支持。

28.cell标签增加encodecount属性，用来指定用utf-8编码输出的次数，有些情况下需要编码2次
例如：

<a href="<%=request.getContextPath() %>/file/downloadFile.htm?fileName=<pg:cell encode="true" encodecount="2" colName="fileName"/>">下载此文件**</a**>

29.完善字符过滤器，utf-8编码时，get方式下，在ie浏览器中可以自动识别中文参数，无需在js中escape编码即可解决中文乱码问题

相关资源信息

release version : [bbossgroups-3.6.0](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.0)

release date: 2012/10/06



bboss website

[http://www.bbossgroups.com](http://www.bbossgroups.com/)



bboss project blog

http://yin-bp.iteye.com/



bboss project sourceforge

http://sourceforge.net/projects/bboss/files/



bboss source project at github

https://github.com/bbossgroups/bbossgroups-3.5