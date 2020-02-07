### bboss 发布和使用hessian服务方法介绍

hessian是一款性能非常不错的RPC通讯组件，最近抽空将bboss和hessian做了个整合，可以简单方便地将bboss ioc管理的组件直接发布为hessian服务，本文详细介绍之。

##### 一、bboss hessian属性

bboss ioc为hessian组件定义了一组扩展属性，说明如下：

Java代码 

```java
hessian:api 服务接口  
hessian:servicePort 指定服务标识  
                  hessian:serializable xml|bin 序列化类型,默认为bin  
                  hessian:debug default false used by serializable="bin".  
                  hessian:sendCollectionType used by serializable="bin". default true Set whether to send the Java collection type for each serialized collection.  
                  hessian:serializerFactory used by serializable="bin".default com.caucho.hessian.io.SerializerFactory  
```

服务定义示例：

Xml代码

```xml
 <property name="tokenservice" hessian:servicePort="tokenService"  
    class="com.demo.common.action.TokenController" />  
  
<property name="tokenservicebin" hessian:servicePort="tokenService"   
hessian:debug="true" hessian:sendCollectionType="true"   
hessian:serializerFactory="com.caucho.hessian.io.SerializerFactory"  
    class="com.demo.common.action.TokenController" />  
  
<property name="tokenserviceforxml" hessian:servicePort="tokenService"    
hessian:serializable="xml"   
    class="com.demo.common.action.TokenController" />  
```

这些服务定义只需要放置在ioc配置文件中即可，通过[bboss ioc容器装载和实例化](http://yin-bp.iteye.com/blog/1153798)。

##### 二、hessian服务部署

依托bboss ioc模块，hessian服务发布非常简单，首先配置hessian 服务dispatchservlet用来接收hessian服务调用请求：

**服务名称来自请求参数的配置方法**：

在web.xml文件中配置HessionDispatchServlet

Xml代码 

```xml
<servlet>  
                    <servlet-name>HessionRemote</servlet-name>  
                    <servlet-class>org.frameworkset.spi.remote.hession.HessionDispatchServlet</servlet-class>  
                      
                </servlet>  
                <servlet-mapping>  
                    <servlet-name>HessionRemote</servlet-name>  
                    <url-pattern>/hessian</url-pattern>  
                </servlet-mapping>  
```

客户端通过以下方式传递服务名称：

Java代码

```java
String url = "http://10.25.192.142:8081/context/hessian?service=tokenService";  
CommonUserManagerInf tokenService = (CommonUserManagerInf) factory.create(CommonUserManagerInf.class, url);  
        Result result = tokenService.getUserByUserAccount("yinbp");
```

**服务名称来自请求地址（restful）的配置方法**

Xml代码 

```xml
<servlet>  
        <servlet-name>HessionRemote</servlet-name>  
        <servlet-class>org.frameworkset.spi.remote.hession.HessionDispatchServlet</servlet-class>  
        <init-param>  
            <param-name>restful</param-name>  
            <param-value>true</param-value>  
        </init-param>  
    </servlet>  
    <servlet-mapping>  
        <servlet-name>HessionRemote</servlet-name>  
        <url-pattern>/hessian/*</url-pattern>  
    </servlet-mapping>  
```

客户端通过以下方式传递服务名称：

Java代码

```java
String url = "http://localhost/hessian/commonuserService";  
        CommonUserManagerInf tokenService = (CommonUserManagerInf) factory.create(CommonUserManagerInf.class, url);  
        Result result = tokenService.getUserByUserAccount("yinbp");  
```

这样所有的bboss ioc容器中的组件（这些组件需要实现特定的服务接口）即可作为hessian服务接收客户端调用了。

##### 三、客户端调用hessian服务

**定义服务url**

String url = "http://localhost:8080/context/hessian?

container=bboss.hessian.mvc&containertype=mvc&service=basicservice";//指定容器标识和容器类型及服务标识

url = "http://localhost:8080/context/hessian?service=basicservice";//默认获取mvc容器中的组件

**url参数说明**：

container：服务端ioc容器标识，一般是ioc容器根xml文件对应的类包路径，

   例如：[org/frameworkset/spi/remote/hession/server/hessian-service.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/bboss3.6.2/bboss-hession/test/org/frameworkset/spi/remote/hession/server/hessian-service.xml)

​               mvc类型容器值为bboss.hessian.mvc(也是container的默认值)

containertype： 容器类型

mvc mvc容器

simple 对应ioc容器类型为DefaultApplicationContext

 其他值 对应ioc容器类型为ApplicationContext

service：服务标识，ioc组件的名称

**创建bin模式客户端代理**

Java代码 

```java
HessianProxyFactory factory = new HessianProxyFactory();  
                ServiceInf basic = (ServiceInf) factory.create(org.frameworkset.spi.remote.hession.server.ServiceInf.class, url);  
              
                System.out.println("Hello: " + basic.hello("John"));  
```

**创建xml模式客户端代理**   

Java代码

```java
BurlapProxyFactory factory = new BurlapProxyFactory();  
                ServiceInf basic = (ServiceInf) factory.create(org.frameworkset.spi.remote.hession.server.ServiceInf.class, url);  
              
                System.out.println("Hello: " + basic.hello("John"));  
```

**通过bboss-ioc配置和获取客户端**

Xml代码

```xml
<property name="clientservice" factory-class="com.caucho.hessian.client.HessianProxyFactory" factory-method="create">  
                    <construction>  
                        <property value="org.frameworkset.spi.remote.hession.server.ServiceInf"/>       
                        <property value="http://localhost:8080/context/hessian?service=basicservice"/>      
                    </construction>  
                </property>  
```

Java代码

```java
DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/remote/hession/client/hessian-client.xml");  
                //获取客户端组件实例  
                ServiceInf basic = context.getTBeanObject("clientservice", ServiceInf.class);  
```

使用bboss工厂模式，调用HessianProxyFactory的create方法创建hessian服务客户端调用组件,同时我们可以采用bboss ioc依赖注入特征，将hessian客户端的相关参数(connectionTimeout,readTimeout等)设置到HessianProxyFactory中.

[org/frameworkset/spi/remote/hession/client/hessian-client.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/bboss3.6.2/bboss-hession/test/org/frameworkset/spi/remote/hession/client/hessian-client.xml)

ok，bboss 发布和使用hessian服务方法就介绍到此，欢迎大家留言交流。

