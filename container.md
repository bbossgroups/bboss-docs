### bboss应用程序运行容器使用介绍

bboss微服务运行容器使用介绍，通过简单的配置文件，配置主程序需要的依赖库和依赖资源，快速搭建应用程序运行环境容器，切入正题。

微服务容器相关的资源

- [bboss-rt-xxxx.jar](http://search.maven.org/#search|gav|1|g%3A"com.bbossgroups" AND a%3A"bboss-rt")组件,xxxx代表版本号

- 服务主程序配置文件，可以有多个配置文件，每个对应一个主程序,config.properties是默认配置主程序

  导入微服务容器组件：

  **gradle坐标**

  Java代码

  ```java
  group: 'com.bbossgroups', name: 'bboss-bootstrap-rt', version: "6.2.3",transitive: true 
  ```

  **maven坐标**

  Xml代码

  ```xml
  <dependency>  
      <groupId>com.bbossgroups</groupId>  
      <artifactId>bboss-bootstrap-rt</artifactId>  
      <version>6.2.3</version>  
  </dependency>  
  ```

  微服务启动指令：

  基于默认配置config.properties启动微服务容器

  java -Xms1024m -Xmx1024m -Xmn512m -XX:PermSize=128M -XX:MaxPermSize=128M -jar bboss-rt-xxxx.jar

  基于自定义配置config-gradle2.properties启动微服务容器:

  java -Xms1024m -Xmx1024m -Xmn512m -XX:PermSize=128M -XX:MaxPermSize=128M -jar bboss-rt-xxxx.jar  --conf=config-gradle2.properties

  假设应用程序根目录为：run

将bboss启动应用程序帮助类包bboss-rt.jar文件放到run目录下，bboss-rt.jar文件下载的地址：[bboss-rt.jar](http://search.maven.org/#search|gav|1|g%3A"com.bbossgroups" AND a%3A"bboss-rt")

一个简单的示例下载地址：[下载](http://www.bbossgroups.com/tool/download.htm?fileName=genproject.zip)

下载下来后，解压运行runcontainer目录下的startup.bat或者startup.sh就可以看运行主程序的效果了。

示例涉及的主程序源码eclipse工程：[下载](https://codeload.github.com/bbossgroups/genproject/zip/master)

运行容器的功能和配置下面详细介绍：  

**1.配置主程序需要的依赖库和依赖资源**

在run目录下放置config.properties文件，内容如下：

mainclass=testclone.Test

mainclass指定了要运行的主程序，将主程序依赖的资源文件放到run/resources目录下，将主程序依赖的jar和其他库文件放到run/lib目录下，这样就可以写下面的运行指令了。

一个示例配置为：

Java代码

```java
#please set yourself mainclass,this is only a simple example.  
mainclass=testclone.Test  
  
#put yourself property parameter here,you can get these parameters use follow codes in your mainclass:  
#String port = CommonLauncher.getProperty("port","8080");//同时指定了默认值   
#String contextPath = CommonLauncher.getProperty("context","bigdata");//同时指定了默认值   
#  
port=86  
context=bigdata  
  
#put yourself extend libs path here,default this tool will always find jars from libs under this project.  
#extlibs=/WebRoot/WEB-INF/lib  
  
#put yourself extend resource path here,default this tool will always find resource files from resources under this project.  
extresources=/classes  
```

**2.编写和运行指令（linux和windows版）**

linux

运行文件：在run目录下新建startup.sh文件，内容为：

#!/bin/sh
nohup java -Xms1024m -Xmx1024m -Xmn512m -XX:PermSize=128M -XX:MaxPermSize=128M -jar bboss-rt-5.2.2.jar >startup.log &

#指定配置文件方式
#nohup java -Xms1024m -Xmx1024m -Xmn512m -XX:PermSize=128M -XX:MaxPermSize=128M -jar bboss-rt-5.2.2.jar --conf=config-gradle.properties > startup.log &
授予可执行权限：chmod +x startup.sh

ok，可以在run目录下，执行./startup.sh，就可以看执行效果了，如果想让你的程序在后台一直运行，那么可以执行以下指令：

nohup ./startup.sh > run.log &

windows

运行文件：在run目录下新建startup.bat文件，内容为：

java -Xms1024m -Xmx1024m -Xmn512m -XX:PermSize=128M -XX:MaxPermSize=128M -jar bboss-rt-5.2.2.jar

ok，可以在run目录下，执行startup.bat，就可以看执行效果了。

**3.进阶**

bboss-rt.jar工具包会默认加载resources、lib、classes、WebRoot/WEB-INF/classes以及WebRoot/WEB-INF/lib四个目录下的jar、class和资源文件，如果想在config.properties配置一些其他的依赖目录和依赖资源，可以指定extlibs和extresources两个属性，例如：

extlibs=/WebRoot/WEB-INF/lib

extresources=/WebRoot/WEB-INF/classes

多个目录可以用；号分隔，例如：

extlibs=/WebRoot/WEB-INF/lib;/WebRoot/WEB-INF/lib1

extresources=/WebRoot/WEB-INF/classes;/WebRoot/WEB-INF/classes1

如果想在config.properties文件中配置一些其他主程序需要依赖的参数，也是可以的：

port=8080

context=bigdata

那么怎么在主程序中获取这些参数呢，方法如下：

Java代码

```java
import org.frameworkset.runtime.CommonLauncher；  
String port = CommonLauncher.getProperty("port","8080");//同时指定了默认值  
String contextPath = CommonLauncher.getProperty("context","bigdata");//同时指定了默认值
```

如果主程序中需要用到当前运行环境的根目录，则只需要在主程序java类中添加以下方法,即可将根目录文件对象注入到主程序中：

Java代码

```java
public static void setAppdir(File appdir) {  
        approotdir = appdir;  
    }  
```

在最新的bboss版本中提供了一套gradle工程构建打包的环境脚本模板：

1.gradle构建脚本

2.运行shell脚本

下载地址：
https://github.com/bbossgroups/bboss/tree/master/bboss-rt/runfiles

ok,bboss启动应用程序帮助类功能介绍完毕。