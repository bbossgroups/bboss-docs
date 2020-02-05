### bboss wordpdf构建部署介绍

  **下载**
源码下载地址：[bboss-wordpdf](https://github.com/bbossgroups/bboss-plugins/archive/master.zip)

下载完毕后将master.zip改名为bboss-plugins.zip,然后解压，本文解压目录为：
d:/bboss-plugins  

  **构建部署步骤**

1.通过gretty gradle插件运行demo工程bboss-plugin-wordpdf-web

2.运行前先执行/bboss-plugins的install任务，编译构建所有插件模块：  

  cd bboss-plugins

gradle install

构建成功后，先启用gretty插件（注意：第一次构建工程，需要关闭gretty插件，默认关闭）

修改/bboss-plugins/gradle.properties中属性为true，即可启用插件：

enable_gretty=true
    然后运行以下指令,启动tomcat和demo应用（运行demo之前需要做一些准备工作，请参考本文后面的内容）

gradle :bboss-plugin-wordpdf-web:tomcatStart

启动后可以在浏览器端访问以下地址：  

  http://localhost/bboss-plugin-wordpdf-web/wordpdf/wordpdfswftool.jsp

http://localhost/bboss-plugin-wordpdf-web/wordpdf/wordpdfswf.jsp

http://localhost/bboss-plugin-wordpdf-web/wordpdf/wordpdf.jsp

http://localhost/bboss-plugin-wordpdf-web/wordpdf/word.jsp

http://localhost/bboss-plugin-wordpdf-web/FlexPaper_2.0.3/index_ooo.html
    **准备demo运行环境**

注意：运行demo工程前，还需要安装liferoffice和swftool并启动soffice进程，安装方法请参考文档:[[bboss-plugin-wordpdf/文档转换部署文档.doc](https://github.com/bbossgroups/bboss-plugins/blob/master/bboss-plugin-wordpdf/文档转换部署文档.doc?raw=true)],  

安装完毕后，修改配置文件/bboss-plugins/bboss-plugin-wordpdf-web/WebRoot/WEB-INF/bboss-wordpdf.xml中相关属性对应路径swftoolWorkDir(swftool安装目录)、officeHome(libreoffice安装目录)、templatedir(word模板所在目录),resultdir(转换文档存放目录)：
Java代码

```java
<properties>  
    <property name="/wordpdf/*.page"           
        f:flashpaperWorkDir="D:\FlashPaper\FlashPaper2.2\"     
        f:templatedir="D:/d/workspace/bbossgroups/bboss-plugins/bboss-plugin-wordpdf"  
        f:swftoolWorkDir="c:/environment/SWFTools/"       
        f:officeHome = "c:/environment/LibreOffice 5"     
        f:resultdir="d:/test"  
        class="org.frameworkset.web.wordpdf.NewPrinterController"/>  
</properties> 
```

