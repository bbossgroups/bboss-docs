## bboss ioc快速入门教程

 bboss是一个非常不错的ioc框架，功能类似于spring ioc和google guice，本文结合一个简单的案例来介绍bboss ioc的用法，让你快速的了解和上手使用bboss ioc。

### **1.首先在工程中引入bboss ioc**

**maven坐标：**

Xml代码

```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-core</artifactId>  
    <version>6.2.2</version>  
</dependency> 
```

**gradle坐标：**
Java代码

```java
compile group: 'com.bbossgroups', name: 'bboss-core', version: '6.2.2'  
```

### **2.编写组件实现**

org.gradle.IOCExample

Java代码

```java
package org.gradle;  
  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
  
public class IOCExample {  
    private static final Logger logger = LoggerFactory.getLogger(IOCExample.class);  
    private String name ;  
    private String sex;  
    private String homepage;  
    public void init(){  
        logger.debug("init bean.............");  
    }  
  
    public String exampMethod(){  
        return new StringBuilder().append("name = ").append(name).append(",")  
                .append("sex = ").append(sex).append(",")  
                .append("homepage = ").append(homepage).toString();  
    }  
  
}  
```

### **3.定义外部属性配置-config.properties**

Java代码

```java
name=杰克  
homepage=http://www.bbossgroups.com 
```

### **4.配置bboss ioc**

编写bboss ioc配置文件：exampile.xml,放到工程resources目录

Xml代码

```xml
<!-- 
    bboss ioc配置实例 
-->  
<properties>  
    <!--  
    导入外部属性文件，bboss ioc外部属性参考文档：  
    http://yin-bp.iteye.com/blog/2325602  
    -->  
    <config file="config.properties"/>  
    <!--  
    name="examplebean" 指定组件名称  
    class="org.gradle.IOCExample" 指定组件实现类  
    f:name="${name:jack}" 组件属性name注入,值配置在config.properties文件中，如果外部属性文件中没有配置name则使用默认值jack  
    f:homepage="${homepage}" 组件属性homepage注入,值配置在config.properties文件中  
    f:sex="男" 属性sex注入  
    init-method="init" 组件初始化方法  
    -->  
    <property name="examplebean"  
              class="org.gradle.IOCExample"  
              f:name="${name:jack}"  
              f:homepage="${homepage}"  
              f:sex="男"  
              init-method="init"  
    />  
  
</properties>  
```

**5.测试用例**

Java代码

```java
package org.gradle;  
  
import org.frameworkset.spi.BaseApplicationContext;  
import org.frameworkset.spi.DefaultApplicationContext;  
import org.junit.Test;  
  
/** 
 * Created by 1 on 2017/6/25. 
 */  
public class TestInvoke {  
    @Test  
    public void test(){  
        //初始化ioc容器  
        BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("example.xml");  
        //获取组件实例  
        IOCExample example = context.getTBeanObject("examplebean",IOCExample.class);  
        //调用组件方法  
        String message = example.exampMethod();  
        System.out.println("message:"+message);  
    }  
}  
```

**6.构建和运行**
在构建和运行之前先安装并配置好gradle环境，gradle的安装和配置参考文档：
http://yin-bp.iteye.com/blog/2313145
下载示例：[下载](http://www.bbossgroups.com/tool/download.htm?fileName=testioc.zip)
解压下载的文件，后切换到cmd，在testioc目录下执行命令：
gradle releaseVersion

![](../_images/ioc/4faedb78-2d31-3018-ad5e-40afa45be9ae.png)

然后切换到目录build/dist下面，运行指令：start.bat就可以看效果了：

![](../_images\ioc\8f51b054-4ed0-3e17-a3ba-2d55d4f4ca56.png)

![](../_images\ioc\f4f3425c-c29a-3dd6-88a6-0a6cf1477990.png)

