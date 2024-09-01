
#   **工具介绍**

工具包含以下功能

1. 数据库管理 数据库快速查询，数据库管理，数据库连接池监控

2. 代码生成 代码自动生成工具

3. 数据库连接池监控 活动链接、连接池配置（最大连接数、最小连接数、空闲连接数等）、正在使用链接监控
4. bboss mvc、ioc、持久层配置、Elasticsearch客户端配置等等SPI组件监控
5. 虚拟机内存使用情况监控，FreeMemory、TotalMemory、MaxMemory



**工具下载地址：**

[bboss-gencode v6.2.5](https://doc.bbossgroups.com/files/gencode.zip)

工具源码地址

https://gitee.com/bboss/bboss-gencode

https://github.com/bbossgroups/bboss-gencode

下面介绍具体用法。  

# 1. 工具安装

从bboss官网下载安装包：[下载](https://doc.bbossgroups.com/files/gencode.zip)
下安装包后直接解压，解压后的目录结构为：

![](images/bboss/82c791eb-376b-3d10-b8fa-15e383c3aead.gif)

  运行解压目录下的startup.bat（linux下执行startup.sh）
然后在chrome或者火狐浏览器下访问地址：
http://localhost/gencode



##   1.1.从github下载源码构建安装

https://gitee.com/bboss/bboss-gencode

https://github.com/bbossgroups/bboss-gencode


源码下载完毕，解压到目录d:/bboss-gencode(指定自己的目录即可)，采用gradle进行构建：

1.参考以下文档安装gradle并将gradle设置到环境变量

构建参考文档：https://esdoc.bbossgroups.com/#/bboss-build

2.在命令行执行

cd d:/bboss-gencode

以war包方式发布版本（可以部署到tomcat等容器中运行工具），则执行指令：

gradle :gencode-web-app:releaseRuntimeWar

以zip包方式发布版本（内置jetty容器，解压zip包，linux/mac/unix等环境运行startup.sh,windows环境运行里面的startup.bat即可），则执行指令：

gradle :gencode-web-app:releaseRuntimeZip

3.构建成功后(gradle :gencode-web-app:releaseRuntimeZip)：

windows环境下运行d:/bboss-gencode/gencode-web-app/build/dist/gencode/startup.bat

linux/unix/mac os环境下运行startup.sh

同时在d:/bboss-gencode/gencode-web-app/build/distributions会发布出一个带jetty容器独立运行的zip包和可

以部署到tomcat的war包：

gencode-xxxx.zip

gencode-xxxx.war

xxxx是版本号，以最新实际版本为准。  

![](images/bboss/4883fc8d-4d5a-332f-976a-36e4261ab232.png)

启动工具应用，在浏览器端（支持谷歌或者火狐浏览器）访问以下地址，可以在界面中配置数据源，配置表单，生成源代码并打包下载生成的源码文件，浏览源码部署集成说明：

输入以下地址，打开工具主界面：

http://localhost/gencode/monitor/monitor_console.jsp

![image-20231210113359563](images\gen-main.png)

如果看到以上界面说明安装成功，进入代码生成页面：

http://localhost/gencode  


![](images/bboss/520da9f5-93c8-3d21-b90d-5d9289d54332.gif)

以上都是在内置的jetty容器中运行自动代码生成工具，如果需要在tomcat等容器中运行，则需要将构建生成的gencode.war包部署到tomcat中即可。

## 1.2 配置工具

可以在application.properties中配置生成的源码存放目录以及配置数据库sqlite的存放目录。如果需要定制一些配置，可以修改解压目录下的resources/application.properties文件：
resources/application.properties内容如下：  

```properties
mainclass=org.frameworkset.gencode.web.service.JettyStart
port=80
context=gencode
#extlibs=/WebRoot/WEB-INF/lib
#extresources=/WebRoot/WEB-INF/classes

sqlitepath=C:/gencode/gencodedb
sourcepath=C:/gencode/sourcecode
#在idea调试时，多工程下面，需要配置webappbase下面包含WebRoot目录，避免找不到WebRoot目录，发布环境下去掉不需要配置
#webappbase=C:/workspace/bbossgroups/bboss-gencode/gencode-web-app
#linux、unix下面的路径
#sqlitepath=/app/data/gencode/gencodedb
#sourcepath=/app/data/gencode/sourcecode
#在idea调试时，多工程下面，需要配置webappbase下面包含WebRoot目录，避免找不到WebRoot目录，发布环境下去掉不需要配置
#webappbase=/app/data/gencode/tempjspjava
```

  修改启动的端口和应用上下文
port=80
context=gencode

如果需要修改代码的存放目录（默认为运行目录下的sourcecode目录），就打开配置属性sourcepath并修改：
\#sourcepath=d:/sourcecode

如果需要修改存放表单配置的sqlite数据库的路径（不设置的话默认为运行目录），就打开配置属性sqlitepath并修改：
\#sqlitepath=d:/gencodedb  

# 2. 数据源管理

为了能够对数据库中的表生成代码和进行数据库管理，需要配置相应的数据源,参考下图：  

![image-20231210104725726](images\dsmanager.png)

## 2.1.数据源管理

  点击新增DS即可，然修改相关属性（注意数据源名称不能重复，不能使用gencode这个内置数据源名称）。
典型的数据源配置参考：

oradle

数据源驱动: oracle.jdbc.driver.OracleDriver

数据源地址：

```properties
jdbc:oracle:thin:@2.197.40.177:1521:ora177
```

mysql

数据源驱动: com.mysql.cj.jdbc.Driver

数据源地址：

```properties
jdbc:mysql://localhost:3306/myproject?useUnicode=true&characterEncoding=utf-8
```

postgresql

驱动：org.postgresql.Driver

数据源地址：

```properties
jdbc:postgresql://101.13.66.127:17700/bboss?currentSchema=bboss
```

clickhouse

驱动：com.github.housepower.jdbc.ClickHouseDriver

数据源地址

```properties
jdbc:clickhouse://101.13.6.4:29000,101.13.6.7:29000,101.13.6.6:29000/bboss
```

同时也可以修改和删除已有数据源。  

## 2.2.数据源启动和停止

数据源需要启动后才能使用源。

启动

![image-20231210112555962](images\datasource-start.png)

停止

![image-20231210113122231](images\datasource-stop.png)

# 3.自动生成代码工具

在介绍之前首先了解一下bboss自动代码生成工具能帮助我们做哪些事情。

通过自动代码生成框架，根据模板可以自动生成数据库表的增、删、改、分页查询、列表查询、国际化功能对应的java、jsp程序和配置文件，包括：

1. mvc控制器
2. 业务组件
3. PO实体类
4. jsp文件 可以定制不同风格的界面模板，目前提供了一套bboss平台的基础ui风格和一套bboss普通ui风格模板
5. cxf webservice服务类文件
6. hessian服务类文件
7. sql配置文件
8. ioc/mvc组件装配部署和服务发布配置文件.
9. 国际化属性文件和国际化配置
10. 代码和配置文件集成配置部署readme说明文件  

**自动代码生成框架功能特点：**

- 提供友好的基于bootstrap的表单配置界面
- 提供源码打包下载功能
- 提供基于eclipse的代码格式化功能
- 可以依据自动生成的使用说明文件快速将代码集成部署到基于bboss或者bboss平台开发的项目中
- 支持mysql、oracle、sqlites、sqlserver等主流数据库。
- 支持自定义前端UI界面风格，可以根据需要定制自己需要的界面风格模板



## 3.1 选择数据库表并生成代码

选择数据源：

![](images/bboss/63be8ab7-327b-3166-92e3-1f8ea72ee279.gif)

选择表

![](images/bboss/83fe0f2f-4823-3c7b-a838-9e0e77a6eaae.gif)

  然后点击进入表单配置，即可
如果需要重新加载数据源中的表结构，可以点击刷新表结构

## 3.2 配置表单

选择好表并进入表单界面：  

![](images/bboss/882ebbb6-d886-3597-9ed3-1028477a70b3.gif)

在表单配置界面可以配置三部分内容：

![](images/bboss/276e3dca-2b60-361b-9192-147367e4a507.gif)

![](images/bboss/36016012-5df4-3c96-8b03-471b4a50ce86.gif)

###   **3.2.1基本信息配置**

模块名称 指定模块英文名称
模块中文名称 指定模块中文名称
包路径 指定java程序存放的包路径
jsp相对路径 指定jsp文件存放的相对路径
系统名称  指定模块对应的系统名称，一般不需要指定
界面风格  default|common 指定ui风格模块，**default针对bboss平台**(参考文档：[基于bboss开发平台eclipse开发工程生成工具介绍](http://yin-bp.iteye.com/blog/2230399)搭建bboss平台开发环境)，**common针对bboss框架（可以将这种风格模块生成的代码集成到eclipse工程：bboss-gencode/commonstyle，然后按照自动生成部署说明文档部署运行代码即可**。
代码控制参数 控制生成的代码范围，取值范围（可以根据需要进行选择配置）：
geni18n 选中则生成国际化配置功能
clearSourcedir 生成代码时清除之前的文件
genRPC  生成webservice和hessian服务
autopk  自动生成主键 默认采用UUID生成主键，也可以结合tableinfo表中表主键生成
print  自定生成打印功能（暂未实现）
genwf  自动生成工作流功能（暂未实现）

excel版本号 设置excel导出功能，暂未实现
唯一标识字段  指定表的唯一标识字段
主键SEQ名称  tableinfo表中主键配置信息（针对bboss平台）

分页机制 提供两种分页机制选项：
【默认分页机制】适用数据库oracle，mysql，maradb，sqlite，postgres
【Rownumber Over(Order By)】适用数据库oracle，mysql，maradb，sqlite，postgres，derby，mssql server2005/2008，db2

指定DB操作数据源 可以设置服务组件中通用dao执行DB操作的数据源，不指定时在poolman中配置的第一个数据源上执行所有DB操作

### **3.2.2版权信息配置**

作者
公司
版本号

### **3.2.3字段信息配置**

字段信息配置可以指定每个字段的配置：
java类型
中文名称
日期格式
数字格式
查询条件
查询方式
日期范围查询
排序字段
默认排序字段:多个排序字段时，指定一个默认排序字段
排序方式
列表字段  控制字段在列表中显示、隐藏（作为隐藏域）、忽略（不在列表页面出现，也不隐藏）
编辑控制： 控制字段在编辑页面显示、隐藏、忽略、只读、可编辑、必填
添加控制： 控制字段在编辑页面显示、隐藏、忽略、只读、可编辑、必填
查看控制： 控制字段在编辑页面显示、隐藏、忽略
默认值
类型校验
显示长度 根据指定长度在列表页面对字段值进行截取
替换串 根据指定长度在列表页面对字段值进行截取，截取部分用替换串进行替换
字段注释：维护PO对象属性注释，默认采用表字段注释作为PO对象属性注释，如果不填写字段注释或者表字段没有注释，采用字段中文名称作为注释

字段列表中除了可以配置字段外，可以调整字段的顺序，只要鼠标拖拉字段到对应的位置就可以进行排序。

配置完毕后点击暂存和生成代码即可,代码生成好后立马可以查看部署说明、下载代码、在线浏览源码。  

![](images/bboss/27e7e0c4-8128-3b88-80bc-45c5261ea013.gif)

## **3.3 代码配置历史记录管理**



![](images/bboss/662e9dc7-352f-346e-ada0-85a189aad6d2.gif)

## **3.4 在线浏览代码**

![](images/bboss/d6e92a33-b752-3aaa-a9eb-351408ef7bd1.gif)

![](images/bboss/969214ab-59e0-3f26-8e07-7172d948c477.gif)

![](images/bboss/ceaac884-49e8-35ce-916f-7ec987bf01e4.gif)



## 3.5 表单配置界面效果

![点击查看原始大小图片](images\gencode-form5.gif)

![点击查看原始大小图片](images\gencode-form6.gif)

![点击查看原始大小图片](images\gencode-deploy.gif)

![点击查看原始大小图片](images\gencode-form7.gif)
![点击查看原始大小图片](images\gencode-formconf.gif)

![点击查看原始大小图片](images\gencode-form3.gif)

![点击查看原始大小图片](images\gencode-form2.gif)

![点击查看原始大小图片](images\gencode-form1.gif)

![点击查看原始大小图片](images\gencode-form0.gif)

**自动生成的代码和配置文件片段：**

![点击查看原始大小图片](images\gencode-simpl.gif)

![点击查看原始大小图片](images\gencode-sql.gif)

![点击查看原始大小图片](images\gencode-service.gif)

![点击查看原始大小图片](\images\gencode-ioc.gif)

![点击查看原始大小图片](images\gencode-0.gif)

# 4.数据库管理工具

## 4.1快速查询

![image-20231211153458224](images\datasource\quickquery.png)

选择数据源

选择表

设置记录数

设置查询条件

## 4.2 数据库管理

![image-20231211154214224](images\datasource\dm.png)

可以选择数据源，执行各种类型sql：查询、修改、删除、插入以及建表、删表、建库等等



## 4.3 连接池监控

![image-20231211154453711](images\datasource\pm.png)

可以查看所有已经启动的数据源情况

数据源详情

![image-20231211154609239](images\datasource\dmdetail.png)

实时链接信息

![image-20231211154748190](images\datasource\connections.png)