# 采用gradle构建和发布bboss方法介绍 

采用gradle构建和发布bboss版本及从maven中央库下载bboss方法介绍

## **1.概述**

bboss是国内最早采用gradle来构建和发布版本的开源框架之一，那么gradle是个什么东东？以下公式可以大概表述一下意思：

**gradle=ant+maven**

尤其是结合eclipse jetty插件和idea tomcat插件直接可以在开发工具中中调试web应用（改了代码不用重启tomcat或者jetty），真是太棒了。

从bboss v4.10.8版本开始,[bbossgroups](https://github.com/bbossgroups)旗下所有项目全部采用gradle来打包构建并发布到[maven中央库](http://search.maven.org/#search|ga|1|bbossgroups)，项目清单如下：

- 1.bboss ioc
- 2.bboss mvc
- 3.bboss 持久层
- 4.bboss taglib
- 5.bboss util
- 6.bboss 序列化
- 7.bboss 分布式事件(devent)
- 8.bboss quartz定时任务插件
- 9.bboss hession插件
- 10.bboss velocity
- 11.bboss session（bboss security）
- 12.bboss data(redis,mongodb操作组件)
- 13.bboss gencode(代码生成工具)
- 14.bboss site(官网工程)
- 15.bboss hibernate plugin
- 16.bboss websocket
- 17.bboss rpc（webservice服务等）
- 18.bboss bigdatas(db to hdfs etl  tool)
- 19.bboss genproject(开发平台环境搭建工具)
- 20.bboss bestpractice(bboss最佳实践demos)

[gradle官方文档](https://docs.gradle.org/current/userguide/installation.html)

## **2.采用gradle生成bboss eclipse/idea 工程及发布和构建bboss版本**

首先从github下载bboss源码，github地址：https://github.com/bbossgroups/bboss

下载完毕后，进入cmd命令行模式，切换到bboss session存放目录，例如cd d:/security

直接通过idea和eclipse的gradle插件，可以将对应的gradle工程导入elcipse或者idea即可  发布版本到本地maven库：
gradle publish

## 3.采用gradle生成bboss session eclipse/idea 工程及发布和构建bboss session版本

首先从github下载bboss session源码，github地址：https://github.com/bbossgroups/security
下载完毕后，进入cmd命令行模式，切换到bboss session存放目录，例如
cd d:/security
直接通过idea和eclipse的gradle插件，可以将对应的gradle工程导入elcipse或者idea即可

发布版本到本地maven库：
gradle publish  



## **4.bboss和bboss session maven中央库下载地址**

[http://search.maven.org/#search%7Cga%7C1%7Cbbossgroups](http://search.maven.org/#search|ga|1|bbossgroups)

## 5.gradle构建说明

frameworkset-util.jar->bboss-util.jar**标签库相关包：**frameworkset.jar->bboss-taglib.jar

**持久层包：**

## **6 bboss gradle工程导入eclipse**

参考文档:[《bboss gradle工程导入eclipse介绍》](http://yin-bp.iteye.com/blog/2313145)

