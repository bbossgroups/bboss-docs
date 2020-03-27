### 使用rmi协议，基于cglib实现组件管理和远程方法调用

bbossgroups项目中提供的一套非常简洁但是功能却很丰富的aop框架,本文介绍如何使用使用rmi协议、基于cglib实现组件管理和远程方法调用。

组件配置org/frameworkset/spi/cglib/service-bean-assemble.xml

Xml代码

```xml
<properties>  
    <property id="cglibbean" singlable="true"  class="org.frameworkset.spi.cglib.CGLibService" />  
</properties> 
```

组件实现类：

Java代码

```java
package org.frameworkset.spi.cglib;  
  
  
/** 
 * <p>Title: CGLibService.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2010-6-21 上午10:31:54 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class CGLibService {  
      
    public String sayhello(String name)  
    {  
        System.out.println("remote from "+ name);  
        return "Hello," + name;  
    }  
  
}  
```

测试用例：

Java代码

```java
package org.frameworkset.spi.cglib;  
  
import org.frameworkset.spi.ApplicationContext;  
import org.junit.Test;  
  
/** 
 * <p>Title: CGLibTest.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2010-6-21 上午10:30:57 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class CGLibTest {  
    static ApplicationContext context_provider = ApplicationContext.getApplicationContext("org/frameworkset/spi/cglib/service-bean-assemble.xml");  
    @Test  
    public void test()  
    {  
        //远程调用  
        CGLibService service = (CGLibService)context_provider.getBeanObject("(rmi::172.16.17.216:1099)/cglibbean");  
        System.out.println(service.sayhello("多多"));  
    }  
      
      
    @Test  
    public void localtest()  
    {  
        //本地调用  
        CGLibService service = (CGLibService)context_provider.getBeanObject("cglibbean");  
        System.out.println(service.sayhello("多多"));  
    }  
  
}  
```

可到sourceforge下载最新版本bbossgroups-2.0-RC1，下载地址：

http://sourceforge.net/projects/bboss/files/