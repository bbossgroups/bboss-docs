### bboss taglib 1.0.2 发布

bboss 项目文件清单：https://sourceforge.net/projects/bboss/files/

bboss taglib 1.0.2 发布 ，下载地址：[https://sourceforge.net/projects/bboss/files/Tag%20framework/bboss-taglib-1.0.2.zip](https://sourceforge.net/projects/bboss/files/Tag framework/bboss-taglib-1.0.2.zip)

**功能说明如下：**

directory:

bbosstaglib

 src--demo source code

 lib--bboss taglib framework compile but not runtime depends jars

 WebRoot--web应用文件夹

common_old_tag

 src--bboss taglib framework source code

 lib--bboss taglib framework compile but not runtime depends jars

tab

 src--bboss tabpane taglib source code

 lib--bboss tabpane taglib depends jars

 doc--some bboss tabpane taglib document

common_old_util

 src--bboss util source code

 lib--bboss util depends jars

update function list:

\----------------------------------------

1.0.2 - 2009-6-16

\----------------------------------------

o 增加radio树测试用例

o 更新持久层框架和aop框架的jar包

o 增加所有标签库的源代码

\----------------------------------------

1.0.1 - 2009-6-16

\----------------------------------------

基本功能介绍：

o 树标签

o 右键菜单 ，目前只支持一级右键菜单，多级菜单还存在bug，后续版本进行完善

o 抽屉式标签

o 分页列表标签

o 部署工作相对简单：

1.首先在你的数据库中创建一个用户

2.在刚才创建的数据库用户中执行tableinfo.SQL脚本，为标签的展示提供数据

3.修改WebRoot/WEB-INF/classes/poolman.xml文件中的数据库账号和密码为刚才创建的用户，修改对应的驱动程序

修改的内容如下：

 < driver >oracle.jdbc.driver.OracleDriver</ driver> 

​    < url >jdbc:oracle : thin : @//localhost : 1521/orcl</ url >

​    < username >baseline</ username >

​    < password >baseline</ password >

4.将WebRoot应用部署到tomcat中运行即可