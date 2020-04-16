### bboss taglib 1.0.1 发布

bboss taglib 1.0.1 发布，下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092&release_id=690235

项目目录结构如下：

directory:

src--demo source code

lib--bboss taglib framework compile but not runtime depends jars

WebRoot--web应用文件夹

update function list:

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

 < driver >oracle.jdbc.driver.OracleDriver</ driver > 

​    < url >jdbc : oracle : thin : @//localhost : 1521/orcl</ url >

​    < username >baseline</ username >

​    < password >baseline</ password >

4.修改WebRoot/WEB-INF/classes/velocity.properties文件中的配置到应用的web-inf目录下templates路径

file.resource.loader.path = D:\workspace\bbosstaglib\WebRoot\WEB-INF\templates 

5.将WebRoot应用部署到tomcat中运行即可

补充：在运行时还要将velocity.properties放到tomcat的 bin目录下，不然会提示找不到velocity.properties的错误，树的收缩和展开会出问题

这个问题将在下一个版本中解决

问题已经修复，修复的程序作为补丁发布在sourceforge的bug列表中，问题详细信息如下：

https://sourceforge.net/tracker/index.php?func=detail&aid=2809480&group_id=238653&atid=1106954

bbosstaglib-package-1.0.1.1.zip 补丁下载地址：

https://sourceforge.net/tracker/download.php?group_id=238653&atid=1106954&file_id=332352&aid=2809480  

下载后，修改src/manager-provider.xml文件中的以下内容：

< properties >

< property name="approot" value="D:/workspace/bbosstaglib/WebRoot" />

</ properties >

将value值修改为你的应用所在的目录，这里为“D:/workspace/bbosstaglib/WebRoot”

然后重新编译该文件到webroot/web-inf/classes目录下即可。  

