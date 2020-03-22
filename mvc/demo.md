### bbossgroups mvc demo构建部署方法

bbossgroups mvc demo部署方法：

1.环境准备

jdk 1.6或以上

tomcat 7或以上版本

2.下载最新的源码包

[bboss-site（定期发布）](https://github.com/bbossgroups/bboss-site)

3.下载后解压，进入bboss-site工程目录，执行build.bat命令，构建demo应用war包bboss-site.war

4.然后修改构建出来的包bboss-site.war\WEB-INF\classes\poolman.xml文件中的内容：

Xml代码 

```xml
<url>jdbc:derby:D:/database/cimdb</url>   
```

即可,demo数据库文件在github中可以找到：

https://github.com/bbossgroups/bboss-document/tree/master/database/cimdb

5.将bboss-site.war拷贝到tomcat的webapps，启动tomcat

6.tomcat启动完毕后，在浏览器重输入以下地址：

http://localhost:8080/bboss-site/index.htm

整个部署过程就完成了。
  