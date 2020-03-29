### bbossgroups-2.0-RC 发布

bbossgroups project contain follow subprojects:

1.bboss-aop, an aop framework.(ioc ,rpc[jms,mina,jgroups,cxf webservice,rmi,netty,restful],bean component,cxf webservice component framworkset and so on).

2.bboss-persistent, a persistent framework().

a.灵活的事务管理（声明式事务管理，可编程事务管理，java注解事务管理，jdbctemplate事务管理，五种经典的事务类型，支持事务嵌套，支持多数据库分布式事务）

b.灵活的访问数据库的接口（普通sql操作，预编译sql操作，普通/预编译批处理操作，存储过程，函数）

c.一套经典的数据库操作标签库（增删改查，普通sql操作，预编译sql操作，普通/预编译批处理操作）

d.经典的多数据库连接池配置管理和使用方法（所有的数据库操作接口可以直接指定连接池的名称，方便地实现对不同数据库的操作）

3.bboss-taglib, a web layer taglib framework(list tag,pageine list tag,detail tag ,logic tag,tree tag,tabpane tag,dbutil tag).

4.bboss-event, an event framework(local event,remote distribute event framework base aop rpc framework).

5.bboss-util, an utility framework.

6.antbuildall, ant build project that build up projects.

7.bbossevent-client, an event remote client test project.

8.bboss-client, an rpc client test project.(jms,mina,jgroups,cxf webservice,rmi,netty,restful).9.bboss-ws, bboss webserive framework test project.10.文档 目录包含framework 开发文档和bboss aop框架的技术使用文档

bboss group project blog:

http://blog.csdn.net/yin_bp

http://yin-bp.iteye.com/

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/

从bbossgroup 1.0开始，已经将以前的持久层框架，aop框架，标签库框架，事件框架，工具框架，全部作为bbossgroup 的子项目一起发布

新增antbuildall [ant complile for all bboss group projects],可以运行antbuildall下的run.bat命令编译所有的子项目，并且更新相应工程的引用jars。  

release version : bbossgroups-2.0-RC

release date: 2010/07/11

release files:Contain all sub projects source files,distribute files,All projects dependended jars,So the file size is some bigger,do not warry,every sub project can be downloaded alone.

各子项目新增功能和修改功能清单请参考每个项目中的readme.txt文件。