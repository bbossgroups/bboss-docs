### bbossgroups 3.1中webservice引擎使用方法

bbossgroups 3.1中webservice引擎使用方法可以参考bbossgroups培训教程的25-28页，下载地址：

http://dl.iteye.com/topics/download/5e8d0f07-53c2-34f1-a0d8-ee43369774ea

也可以参考CXF WEBSERVICE测试用例：

http://dl.iteye.com/topics/download/910322f9-0cb7-312b-935a-732504c43f63

**框架包请及时更新最新版本**

[bboss ant构建指南](http://yin-bp.iteye.com/blog/1462842)

bbossgroups项目资源下载：

http://yin-bp.iteye.com/admin/blogs/1080824

14.9.2 通过aop组件配置cxf组件工厂调用方式

利用aop框架中的工厂组件管理模式，可以非常方便的获取cxf webservice服务的客服端调用接口，从而方便地实现webservice服务调用。

14.9.2.1 客服端配置文件

Xml代码

```xml
<properties>  
   <property name="WSServiceClient" factory-bean="WSServiceClientFactory" factory-method="create"/>    
         
     <property name="WSServiceClientFactory" class="org.apache.cxf.jaxws.JaxWsProxyFactoryBean">    
         <property name="serviceClass" value="org.frameworkset.web.ws.WSService"/>    
         <property name="address" value="http://localhost:8080/bboss-mvc/cxfservices/mysfirstwsservicePort"/>             
    </property>  
</properties>  
```

说明：

WSServiceClient-代表webservice服务客服端接口组件名称，客服端调用程序通过该名称获取服务调用接口实例，该实例通过工厂模式（组件创建工厂WSServiceClientFactory的create方法创建）获取。

WSServiceClientFactory-组件创建工厂（非静态），webservice客服端通过该工厂的create实例方法来创建服务调用接口实例。在该工厂的定义中可以看出，为了创建webservice服务调用接口，需要指定两个属性serviceClass和address，通过serviceClass属性指定了webservice服务对应的接口，address指定了webservice服务地址。

14.9.2.2 调用方法

Java代码

```java
public class WSClient {  
    ApplicationContext context ;   
    @Before  
    public void init()  
    {  
        context = ApplicationContext.getApplicationContext("org/frameworkset/web/ws/wsclient.xml");   
    }  
    @Test  
    public void test()  
    {  
        org.frameworkset.web.ws.WSService wsservice =  (WSService)context.getBeanObject("WSServiceClient");  
        System.out.println(wsservice.sayHello("多多"));  
    }  
  
}  
```

14.10 3.1版本对webservice服务发布管理做了部分调整

3.1版本对webservice服务发布管理做了部分调整，使得开发人员可以非常方便地发布自己的webservice服务，这里只做调整部分的说明，至于服务的定义、部署、调用可以参考《bbossgroups培训ppt》中25页-28页，这里就不做过多的说明。

14.10.1 服务发布调整

改进webservice服务装载功能，可以从mvc控制器配置文件和所有的applicationcontext对应的配置文件中配置和装载webservice服务：

在Mvc框架控制器文件中配置的ws服务会在webservice引擎启动时自动装载。

普通的applicationcontext容器对应的配置文件中配置的ws服务不能自动加载，我们需要将这些配置文件单独装配到

org/frameworkset/spi/ws/webserivce-modules.xml文件中，以便webservice引擎启动时通过扫描

org/frameworkset/spi/ws/webserivce-modules.xml中装配的组件配置文件来装载其中配置的webservice服务。

org/frameworkset/spi/ws/webserivce-modules.xml文件时3.1版本中新加的用来装配独立applicationcontext中配置的ws服务的部署描述文件。  

3.1版本任然兼容旧版的webservice服务发布方法，即配置在

/bbossaop/resources/org/frameworkset/spi/manager-rpc-webservices.xml中的cxf.webservices.config属性中配置的服务任然会被加载和发布。

14.10.2 org/frameworkset/spi/ws/webserivce-modules.xml装载服务实例

Xml代码

```xml
<properties>  
<!--   
        webservice服务组件装配文件,每个文件作为单独的容器来处理,这里装配的是classpath上下文中需要独立加载的webservice服务  
        mvc框架中需要加载的webservice服务只需要在对应的组件中标注servicePort即可，当webservice引擎启动时会加载这两种模式下的  
        所有webservice服务        
        需要注意的是，webservice引擎需要在mvc框架启动后在启动  
     -->  
    <property name="cxf.webservices.modules">  
        <array componentType="String">  
            <property value="org/frameworkset/spi/ws/protocol-ws.xml"/>  
        </array>  
    </property>  
    <property name="cxf.webservices.loader.order" value="mvc,cxf.webservices.modules">          
    </property>  
      
    <!-- 本组件依赖于bboss-mvc.jar -->  
    <property name="webapplicationcontext" factory-class="org.frameworkset.web.servlet.support.WebApplicationContextUtils" factory-method="getWebApplicationContext"/>      
</properties>  
```

14.10.3 服务定义调整

Bbossgroups中通过在property元素上指定ws:servicePort 属性来标识webservice服务。3.1之前的服务定义是通过在property元素上设置servicePort属性来标识一个webservice服务的，例如：

Xml代码

```xml
<property name="rpc.webservice.RPCCall"   
                      singlable="true"   
                      servicePort="RPCCallServicePort"        
class="org.frameworkset.spi.remote.webservice.RPCCall"/>  
```

3.1版本中标识webservice服务的属性变更为ws: servicePort,服务发布引擎通过识别带ws:前缀的属性来识别webservice服务，并发布该服务，例如：

Xml代码

```xml
<property name="rpc.webservice.RPCCall"   
                      singlable="true"   
                      ws:servicePort="RPCCallServicePort"     
class="org.frameworkset.spi.remote.webservice.RPCCall"/>  
```

[ws.zip](http://dl.iteye.com/topics/download/910322f9-0cb7-312b-935a-732504c43f63) (5 KB)

下载次数: 69