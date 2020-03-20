### Ajax方式发送XML及接收xml响应实例

本文介绍通过bbossgroups 的mvc框架实现Ajax方式发送XML数据及接收xml响应实例，切入正题。

本文分两部分：

1.Ajax方式发送XML数据及xml响应的接收demo的下载和部署

2.源码分析

第一部分 Ajax方式发送XML数据及xml响应的接收demo的下载和部署

1.从以下地址下载demo的eclipse工程

http://dl.iteye.com/topics/download/600a3e0c-acf9-3288-a54b-77acf15d9b70

2.解压工程到指定的目录下，例如：d:/workspace/xmlrequest

3.将工程导入到eclipse，编译成功即可进入下一环节。  

4.部署demo到tomcat 6（jdk 1.5以上），编写xmlrequest.xml文件，内容如下：

Xml代码

```xml
<?xml version='1.0' encoding='gb2312'?>  
  
<Context docBase="D:\workspace\xmlrequest\WebRoot" path="/xmlrequest" debug="0" reloadable="false" privileged="true">  
</Context>  
```

将该文件放入tomcat的conf\Catalina\localhost目录下，即可

5.启动tomcat，在浏览器中输入以下地址，查看效果：

http://localhost:8080/xmlrequest/xml/index.page  