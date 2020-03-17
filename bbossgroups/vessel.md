### bboss 组件容器的使用方法浅析

  本文重点介绍bboss 中的4大组件容器的特点及使用方法

**4大组件容器**

**[1] ApplicationContext**

org.frameworkset.spi.ApplicationContext

包括基本的aop/ioc功能，业务组件、dao组件管理，远程服务，全局属性管理，拦截器，包含声明式事务管理

**[2] WebApplicationContext**

org.frameworkset.web.servlet.context.WebApplicationContext

管理所有mvc框架中的控制器，包括基本的aop/ioc功能，业务组件、dao组件管理，不提供远程服务（和远程服务协议包无关联）

**[3] DefaultApplicationContext**

org.frameworkset.spi.DefaultApplicationContext

包括基本的aop/ioc功能，业务组件、dao组件管理，不提供远程服务（和远程服务协议包无关联）

**[4] SOAApplicationContext/SOAFileApplicationContext**

org.frameworkset.spi.SOAApplicationContext

org.frameworkset.spi.SOAFileApplicationContext

两个轻量级的ioc容器，包含aop/ioc功能、全局属性管理，业务组件、dao组件管理，不包含远程服务、拦截器、不包含声明式事务管理，是DefaultApplicationContext的子类，二者主要用来实现对象xml序列化功能，前者从xml串中反序列化组件，后者从xml文件中反序列化组件  

4大组件容器相对于相同的配置文件都是单实例的，也就是说在应用程序中的任何地方通过以下方法获取到的ioc容器实例都是同一个实例（除了在第一次会加载配置文件并初始化文件中包含的组件外，之后都不会进行初始化加载），而且这些方法是多线程安全的：

Java代码

```java
WebApplicationContext context = org.frameworkset.web.servlet.support.WebApplicationContextUtils.getWebApplicationContext();//获取mvc容器实例  
  
BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/beans/testapplicationcontext.xml");//获取普通ioc容器实例  
```

  **4大组件容器的初始化和操作示例**

本文涉及的组件配置文件如下：

[org/frameworkset/spi/beans/testapplicationcontext.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bbossaop/test/org/frameworkset/spi/beans/testapplicationcontext.xml)

[org/frameworkset/soa/datasource-sql.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-soa/test/org/frameworkset/soa/datasource-sql.xml)

**[1] ApplicationContext初始化和使用示例**

org.frameworkset.spi.ApplicationContext初始化：

Java代码   

```java
ApplicationContext context = ApplicationContext.getApplicationContext("org/frameworkset/spi/beans/testapplicationcontext.xml");  
```

使用示例：

本地服务组件实例获取方法

Java代码

```java
RestfulServiceConvertor convertor = context.getTBeanObject("rpc.restful.convertor",RestfulServiceConvertor.class);  
```

远程服务组件实例获取方法（以rmi协议为例，其他协议类似，更多信息参考博客其他文章）：

Java代码

```java
RestfulServiceConvertor convertor = (RestfulServiceConvertor)context.getBeanObject("(rmi::172.16.17.216:1099)/rpc.restful.convertor");  
```

**[2]WebApplicationContext**

org.frameworkset.web.servlet.context.WebApplicationContext

WebApplicationContext的初始化是在bboss mvc框架启动过程中自动初始化的，你、只需要在web.xml文件中做如下配置即可：

Xml代码

```xml
<servlet>  
        <servlet-name>mvcdispather</servlet-name>  
        <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>/WEB-INF/bboss-*.xml,  
                        /WEB-INF/conf/bboss-*.xml</param-value>  
        </init-param>  
        <load-on-startup>0</load-on-startup>  
    </servlet>  
          
    <servlet-mapping>  
        <servlet-name>mvcdispather</servlet-name>  
        <url-pattern>*.page</url-pattern>  
    </servlet-mapping>   
```

WebApplicationContext容器将会加载contextConfigLocation属性中配置的所有配置文件，形成一个的mvc 框架组件容器。

我们可以在程序这样获取WebApplicationContext容器的实例：

Java代码

```java
WebApplicationContext context = org.frameworkset.web.servlet.support.WebApplicationContextUtils.getWebApplicationContext();//获取实例  
  
//通过以下方式获取mvc容器中的组件实例方法  
DeskTopMenuShorcutManager m = context.getTBeanObject("deskTopMenuShorcutManager", DeskTopMenuShorcutManager.class);  
```

**[3]DefaultApplicationContext**

org.frameworkset.spi.DefaultApplicationContext

DefaultApplicationContext的实例定义和获取组件实例方法为：

Java代码 

```java
BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/beans/testapplicationcontext.xml");  
        RestfulServiceConvertor convertor = context.getTBeanObject("rpc.restful.convertor",RestfulServiceConvertor.class);  
```

**[4]SOAApplicationContext/SOAFileApplicationContext**

org.frameworkset.spi.SOAApplicationContext

org.frameworkset.spi.SOAFileApplicationContext

**SOAApplicationContext的实例化和获取组件实例示例代码：**

Java代码

```java
String content = "<?xml version=\"1.0\" encoding=\"gbk\"?>" +  
            "<esb>"+  
                "<call>"+  
                  
                "<!-- 调度中心需要的数据开始 -->"+  
                      
                    "<property name=\"soamethodcall\" " +  
                        "class=\"org.frameworkset.soa.SOAMethodCall\" "+  
                        "f:requestor=\"requestor\" "+  
                        "f:requestid=\"1000000\" "+  
                        "f:password=\"requestpassword\" "+  
                        "f:encypt=\"encrypt\" "+  
                        "f:encyptalgorithem=\"algorithm\" "+  
                        "f:serviceid=\"hilaryserviceid\" "+  
                        "f:issynchronized=\"true\" >"+  
                        "<!-- 调度中心需要的数据结束 -->"+  
                        "<!-- 调度中心提交给服务提供方的服务方法信息 -->"+  
                        "<property name=\"soamethodinfo\" class=\"org.frameworkset.soa.SOAMethodInfo\" " +  
                                                        "f:methodName=\"methodname\">"+  
                            "<property name=\"paramTypes\">"+  
                                "<array componentType=\"Class\">"+  
                                    "<property >String</property>"+  
                                    "<property >String</property>"+  
                                    "<property >String[]</property>"+  
                                "</array>"+  
                            "</property>" +  
                            "<property name=\"params\">"+  
                                "<array componentType=\"object\">"+  
                                    "<property name=\"condition\">1=1</property>"+  
                                    "<property name=\"orderby\">order by ${A}</property>"+  
                                    "<property>"+  
                                    "   <array componentType=\"String\">"+  
                                        "<property>A</property>"+  
                                        "<property>B</property>"+  
                                        "</array>"+  
                                    "</property>"+  
                                "</array>"+  
                            "</property>" +  
                        "</property>" +  
                    "</property>"+  
                "</call>"+  
            "</esb>";  
//从xml字符串实例化SOAApplicationContext对象   
        SOAApplicationContext context  = new SOAApplicationContext(content);  
//获取xml串中包含的组件对象实例    
        SOAMethodCall object = context.getTBeanObject("soamethodcall",SOAMethodCall.class);  
```

**SOAFileApplicationContext的实例化和获取组件实例示例代码：**

Java代码

```java
SOAFileApplicationContext context = new SOAFileApplicationContext("org/frameworkset/soa/datasource-sql.xml");  
//获取xml串中包含的组件对象实例    
        SOAMethodCall object = context.getTBeanObject("soamethodcall",SOAMethodCall.class);  
```

