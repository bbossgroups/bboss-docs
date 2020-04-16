### j2ee 框架 bbossgroups 1.0 发布

bbossgroups 包含以下子项目

1.bboss-persistent, a persistent framework（持久层框架）.

  参考bbossgroups框架来实现持久层的操作：

  a.灵活的事务管理（声明式事务管理，可编程事务管理，java注解事务管理，jdbctemplate事务管理，五种经典的事务类型，支持事务嵌套，支持多数据库分布式事务）

  b.灵活的访问数据库的接口（普通sql操作，预编译sql操作，普通/预编译批处理操作，存储过程，函数）

  c.一套经典的数据库操作标签库（增删改查，普通sql操作，预编译sql操作，普通/预编译批处理操作）

  d.经典的多数据库连接池配置管理和使用方法（所有的数据库操作接口可以直接指定连接池的名称，方便地实现对不同数据库的操作）

  e.提供了简单的o/r mapping查询接口

  f.提供了多种行处理器来提升查询操作的性能

  g.可以方便地实现blob，clob字段的处理

  h.提供分页操作接口（预编译和普通），可以方便地址各种数据库的分页操作

2.bboss-taglib, a web layer taglib framework(list tag,pageine list tag,detail tag ,logic tag,tree tag,tabpane tag,dbutil tag)（标签库框架）.

3.bboss-aop, an aop framework.(ioc ,rpc[jms,mina,jgroups,cxf webservice],bean component,cxf webservice component framworkset and so on).（aop框架，rpc框架）

4.bboss-event, an event framework(local event,remote distribute event framework base aop rpc framework).（分布式事件框架）

5.bboss-util, an utility framework.（工具包）

6.antbuildall, ant build project that build up projects.（子项目ant构建脚本）

7.bbossevent-client, an event remote client test project.（事件框架测试工程）

8.bboss-client, an rpc client test project.(jms,mina,jgroups,cxf webservice).(aop rpc框架测试客服端工程)

9.bboss-ws, bboss webserive framework test project.（aop框架webservice服务测试工程）

10.bbossgroups document 目录包含framework 开发文档和bboss aop框架的技术使用文档

bbossgroups project blog:

http://blog.csdn.net/yin_bp

http://yin-bp.iteye.com/

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/  

从bbossgroup 1.0开始，已经将以前的持久层框架，aop框架，标签库框架，事件框架，工具框架，全部作为bbossgroup 的子项目一起发布

新增antbuildall [ant complile for all bboss group projects],可以运行antbuildall下的run.bat命令编译所有的子项目，并且更新相应工程的引用jars。

release version : bbossgroups-1.0

release date: 2010/03/18

release files:Contain all sub projects source files,distribute files,All projects dependended jars,So the file size is some bigger,do not warry,every sub project can be downloaded alone.

bbossgroups 最新版本 1.0，整合了原来所有的子项目（持久层框架，标签库框架，aop框架，事件框架），下载地址：

https://sourceforge.net/projects/bboss/files/  