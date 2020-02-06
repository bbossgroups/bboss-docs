### bboss将一个组件同时发布为webservice,hessian,http三种服务方法介绍

**1.概述**

bboss提供cxf webservice（基于cxf 2.7.6），hessian(基于4.0.7),http(基于bboss mvc)三种服务的一次性便捷发布机制。

**2.服务定义**

在bboss ioc配置文件中将组件同时配置为webservice、hessian、http三种服务：

Xml代码

```xml
<properties>  
    <!-- 生成令牌控制配置文件  
    author：biaoping.yin  
    CopyRight：bboss  
    Date:2011.4.13  
    -->  
    <property name="/token/*.freepage" ws:servicePort="tokenService"   
    hessian:servicePort="tokenService"  
    class="com.demo.common.action.TokenController" />  
</properties>  
```

从配置中可以知道：

com.demo.common.action.TokenController是服务组件实现类，首先通过name="/token/.freepage" 将其配置为一个mvc控制器，以便接受http请求方式的服务调用，通过ws:servicePort="tokenService" 将其发布为一个cxf webservice服务，通过hessian:servicePort="tokenService"将其发布为一个hessian服务。hessian:servicePort是可选的配置，bboss 默认将所有的组件都发布为hessian服务，默认采用name属性作为hessian服务的唯一标识，无需额外配置，这里指定hessian:servicePort是由于name属性对应的值/token/.freepage作为mvc请求的地址匹配模式，也可以作为hessian服务的标识，但是作为hessian服务的位置地址标识不太友好，所以通过hessian:servicePort指定了hessian的标识为tokenService。

**3.服务接口定义**

为了让TokenController正确地发布为webservice和hessian服务，还必须定义一个接口TokenService：

Java代码 

```java
import javax.jws.WebParam;  
import javax.jws.WebResult;  
import javax.jws.WebService;  
  
@WebService(name = "TokenService", targetNamespace = "com.demo.common.action.TokenService")  
public interface TokenService {  
    public @WebResult(name = "authTempToken", partName = "partAuthTempToken")  
    String genAuthTempToken(  
            @WebParam(name = "appid", partName = "partAppid") String appid,  
            @WebParam(name = "secret", partName = "partSecret") String secret,  
            @WebParam(name = "account", partName = "partAccount") String account)  
            throws Exception;  
  
    public @WebResult(name = "dualToken", partName = "partDualToken")  
    String genDualToken(  
            @WebParam(name = "appid", partName = "partAppid") String appid,  
            @WebParam(name = "secret", partName = "partSecret") String secret,  
            @WebParam(name = "account", partName = "partAccount") String account)  
            throws Exception;  
  
    public @WebResult(name = "publicKey", partName = "partPublicKey")  
    String getPublicKey(  
            @WebParam(name = "appid", partName = "partAppid") String appid,  
            @WebParam(name = "secret", partName = "partSecret") String secret)  
            throws Exception;  
    public @WebResult(name = "tempToken", partName = "partTempToken") String genTempToken() throws Exception;  
}  
```

接口方法和接口上添加了webservice服务相关的注解，要实现接口的服务组件：

Java代码

```java
@WebService(name="TokenService",targetNamespace="com.demo.common.action.TokenService")  
public class TokenController implements TokenService {  
    /** 
     * 获取令牌请求 
     * @param request 
     * @return 
     */  
    public @ResponseBody String getToken(HttpServletRequest request)  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  memTokenManager.buildDToken(request);  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    public @ResponseBody String genTempToken() throws Exception  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  memTokenManager.genTempToken();  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    /** 
     * 获取令牌请求 
     * @param request 
     * @return 
     * @throws Exception  
     */  
    public @ResponseBody String genAuthTempToken(String appid,String secret,String account) throws Exception  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  memTokenManager.genAuthTempToken(appid, secret, account);  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    /** 
     * 获取令牌请求 
     * @param request 
     * @return 
     * @throws Exception  
     */  
    public @ResponseBody String genDualToken(String appid,String secret,String account) throws Exception  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            long dualtime = 30l*24l*60l*60l*1000l;  
            return  memTokenManager.genDualToken(appid, secret, account,dualtime);  
        }  
        else  
        {  
            return null;  
        }  
    }  
    /** 
     * 获取应用公钥 
     * @param appid 
     * @param secret 
     * @return 
     * @throws Exception  
     */  
    public @ResponseBody String getPublicKey(String appid,String secret) throws Exception  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  memTokenManager.getPublicKey(appid, secret);  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    /** 
     * 获取令牌请求 
     * http://localhost:8081/demo/token/getParameterToken.freepage 
     * @param request 
     * @return 
     */  
    public @ResponseBody String getParameterToken(HttpServletRequest request)  
    {  
        MemTokenManager memTokenManager = org.frameworkset.web.token.MemTokenManagerFactory.getMemTokenManagerNoexception();  
        if(memTokenManager != null)//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  memTokenManager.buildParameterDToken(request);  
        }  
        else  
        {  
            return null;  
        }  
    }  
```

服务方法返回参数前都有@ResponseBody 注解，这个是用来标注mvc框架将返回的String数据作为响应返回到客户端。

**4.服务的三种客户端调用方法**

定义好调用参数

Java代码 

```java
String appid = "appid";  
String secret = "xxxxxxxxxxxxxxxxxxxxxx";  
String account = "yinbp";  
```

**hessian服务方式申请token（hessian提供的标准调用模式）**

Java代码

```java
HessianProxyFactory factory = new HessianProxyFactory();  
//String url = "http://10.25.192.142:8081/context/hessian?service=tokenService";  
String url = "http://192.168.1.101:8080"+request.getContextPath()+"/hessian?service=tokenService";  
TokenService tokenService = (TokenService) factory.create(TokenService.class, url);  
String token = tokenService.genAuthTempToken(appid, secret, account);  
//token = tokenService.genDualToken(appid, secret, account);  
```

**webservice方式申请token(cxf提供的服务调用模式)**

Java代码

```java
url = "http://192.168.1.101:8080/demo/cxfservices/tokenService";  
JaxWsProxyFactoryBean WSServiceClientFactory = new  JaxWsProxyFactoryBean();  
WSServiceClientFactory.setAddress(url);  
WSServiceClientFactory.setServiceClass(TokenService.class);  
tokenService = (TokenService)WSServiceClientFactory.create();  
token = tokenService.genAuthTempToken(appid, secret, account);  
//token = tokenService.genDualToken(appid, secret, account);  
```

**http请求方式申请令牌**

Java代码

```java
url = "http://192.168.1.101:8080/demo/token/genAuthTempToken.freepage?appid="+appid + "&secret="+secret + "&account="+account;  
//url = "http://10.25.192.142:8081/demo/token/genDualToken.freepage?appid="+appid + "&secret="+secret + "&account="+account;  
token = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
```

**5.HttpReqeust组件介绍**

HttpReqeust是bboss最新版本3.8.0中提供的http服务组件，专门用来执行http请求，并将处理结果返回给客户端，提供了以下主要方法：

Java代码

```java
//发送请求，参数url对应服务地址，返回String类型的响应  
public static String httpPostforString(String url) throws Exception  
//发送请求，参数url对应服务地址，params中存放了请求的参数对，返回String类型的响应  
public static String httpPostforString(String url,Map<String, Object> params) throws Exception  
//发送请求，参数url对应服务地址，参数params中存放了请求的参数对，参数files中存放了要上传到服务端的文件信息，返回String类型的响应  
public static String httpPostforString(String url,Map<String, Object> params,  
            Map<String, File> files) throws Exception  
```

