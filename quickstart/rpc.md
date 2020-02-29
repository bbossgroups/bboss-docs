### bboss rpc使用介绍

[bboss 3.4](https://github.com/bbossgroups/bboss)及后续版本在原有的rpc功能基础上做了非常大的改进，支持丰富的协议簇（http/netty/mina/jms/webservice/rmi/jgroups/restful）。bboss rpc客户端可以类似于webservice和rmi的客户端方式对bboss ioc容器、mvc容器中配置的任何组件发起远程调用。为了支撑这个功能，3.4版中新增了组件：
org.frameworkset.spi.ClientProxyContext

该组件提供了以下能力而在本地无需服务组件的实现类和配置文件，只需接口以及接口依赖的相关的类即可：

1.对mvc容器中配置的组件的远程调用

2.对其他类型容器中配置的组件的远程调用

三个主要的接口如下：  

**1.获取MVC容器中的服务组件调用代理**

/**

\* 获取MVC容器中的服务组件调用代理

\* @param <**T>** 泛型类型

\* @param name  服务组件访问地址

\* @param type  组件接口类型，使用泛型来实现接口的自动类型转换

\* @return 服务组件调用代理

*/

public static <**T>**  T getWebMVCClientBean(String name,Class<**T>**  type)  

**2.获取ApplicationContext类型容器中的服务组件调用代理**

/**

\* 获取ApplicationContext类型容器中的服务组件调用代理

\* @param <**T>** 泛型类型

\* @param context 容器标识，一般是容器初始化的配置文件路径

\* @param name  服务组件访问地址

\* @param type  组件接口类型，使用泛型来实现接口的自动类型转换

\* @return 服务组件调用代理

*/

public static<**T>** T getApplicationClientBean(String context,String name,Class<**T>** type)  

**3.获取DefaultApplicationContext类型容器中的服务组件调用代理**

/**

\* 获取DefaultApplicationContext类型容器中的服务组件调用代理

\* @param <**T>** 泛型类型

\* @param context 容器标识，一般是容器初始化的配置文件路径

\* @param name  服务组件访问地址

\* @param type  组件接口类型，使用泛型来实现接口的自动类型转换

\* @return 服务组件调用代理

*/

public static <**T>** T getSimpleClientBean(String context,String name,Class<**T>** type)

每种接口的使用实例如下，全部基于http协议实现：  

**1.获取mvc容器中组件的远程服务调用接口，mvc容器由服务端mvc框架自动初始化**

Java代码

```java
ClientInf mvcinf = ClientProxyContext.getWebMVCClientBean(  
                "(http::localhost:8080/bboss-mvc/http.rpc)" +  
                "/client.proxy.demo?user=admin&password=123456",  
                ClientInf.class);  
mvcinf.helloworld("aaaa，多多");  
```

**2.获取DefaultApplicationContext类型容器中的服务组件调用代理**

Java代码

```java
//服务器端容器org/frameworkset/spi/ws/webserivce-modules.xml必须是以下方式创建  
//      DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/ws/webserivce-modules.xml");  
        ClientInf simpleinf = ClientProxyContext.getSimpleClientBean("org/frameworkset/spi/ws/webserivce-modules.xml",//容器标识  
                                                                    "(http::localhost:8080/bboss-mvc/http.rpc)/client.proxy.simpledemo?user=admin&password=123456",//服务组件地址   
                                                                    ClientInf.class);//服务接口  
simpleinf.helloworld("aaaa，多多");  
```

**3.获取服务器端默认容器中组件的远程服务调用接口**

Java代码

```java
ClientInf defaultinf = ClientProxyContext.getApplicationClientBean( "(http::localhost:8080/bboss-mvc/http.rpc)" +  
                "/client.proxy.simpledemo?user=admin&password=123456", ClientInf.class);  
defaultinf.helloworld("aaaa，多多");  
```

**4.http协议补充说明**

服务端必须在web.xml文件中配置以下servlet

Xml代码

```xml
<servlet>  
  <display-name>RPCHttpServLet</display-name>  
  <servlet-name>RPCHttpServLet</servlet-name>  
  <servlet-class>org.frameworkset.spi.remote.http.RPCHttpServLet</servlet-class>  
 </servlet>  
 <servlet-mapping>  
  <servlet-name>RPCHttpServLet</servlet-name>  
  <url-pattern>*.rpc</url-pattern>  
 </servlet-mapping> 
```

**5.http协议串说明**

`http::localhost:8080/bboss-mvc/http.rpc`

协议部分 ip部分 port部分 应用上下文  匹配rpcservlet串

我们用()将上面的url串括起来，然后再添加服务端组件标识和认证参数user和password：

/client.proxy.simpledemo?user=admin&password=123456

client.proxy.simpledemo为服务端组件，?user=admin&password=123456中的user参数就是认证账户，password参数就是认证口令，如果服务开启了认证机制，就需要在客户端设置这两个参数，反之无需配置。

我们可以在服务组件方法中通过org.frameworkset.spi.security.SecurityContext获取客户端传递过来的账户信息,静态方法：

Java代码

```java
SecurityContext securityContext = SecurityContext.getSecurityContext();  
String user = securityContext.getUser();  
String password = securityContext.getPassword();  
```

**6.通过配置文件来配置客户端调用组件的实例**

可以通组件工厂模式来在aop配置文件中配置一个客户端代理组件，我们这里是以http协议为列，从mvc容器中获取服务client.proxy.demo的客户端调用实例。

先看配置文件：

org/frameworkset/spi/remote/clientproxy/consumer.xml

consumer.xml文件的内容如下：

Xml代码

```xml
<properties>  
    <property name="clientservice" factory-class="org.frameworkset.spi.ClientProxyContext" factory-method="getWebMVCClientBean">  
        <construction>  
            <property name="servicaddress" value="(http::localhost:8080/bboss-mvc/http.rpc)/client.proxy.demo?user=admin&password=123456"/>         
            <property name="serviceclass" value="org.frameworkset.spi.remote.clientproxy.ClientInf"/>       
        </construction>  
    </property>     
</properties>  
```

consumer.xm中配置了名称为clientservice的组件，该组件实例通过代理类org.frameworkset.spi.ClientProxyContext的静态方法getWebMVCClientBean来创建，通过构造器construction指定了getWebMVCClientBean方法需要的两个参数：

参数一 服务地址信息

`http::localhost:8080/bboss-mvc/http.rpc)/client.proxy.demo?user=admin&password=123456`

参数二 服务接口类信息

org.frameworkset.spi.remote.clientproxy.ClientInf

这样我们就可以加载consumer.xml文件创建一个DefaultApplicationContext类型的容器，然后获取到组件的客户端调用实例，代码如下：

Java代码

```java
//定义容器对象  
        DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/remote/clientproxy/consumer.xml");  
        //获取客户端组件实例  
        ClientInf client = context.getTBeanObject("clientservice", ClientInf.class);  
        //发起远程方法调用  
        client.helloworld("aaa");  
```

通过这种方式，我们就可以把之前通过代码调用ClientProxyContext创建客户端代理转换为通过aop容器管理创建客户端调用代理模式，这两种方式是等价的。

**服务配置：**

Xml代码

```xml
<property name="mysfirstwsservice"   enablerpc="true" class="org.frameworkset.web.ws.WSServiceImpl"/>  
```

**理论上bboss ioc容器中配置的组件都可以作为远程服务调用，但是必须通过enablerpc属性开启为远程服务，对应的业务组件才能作为远程服务发布，**

**enablerpc="true" 开启**

**enablerpc="false" 禁用**

**7.总结**

本文介绍了通过ClientProxyContext来获取bboss中三种不同类型容器（mvc容器、独立容器、默认容器)中配置的组件客户端调用代理的方法，并以http协议为列介绍了使用方法；介绍了客户端如何传递认证信息的方法；介绍了协议串的配置方法和含义；同时也对比了通过代码直接创建代理和通过aop配置文件创建代理的两种方式，实际情况可以任意选择。