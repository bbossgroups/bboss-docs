### bboss 动态令牌机制轻松搞定表单重复提交和请求校验签名单点登录

bboss 动态令牌机制轻松搞定表单重复提交和请求校验签名单点登录，本文详细介绍之。

最新代码请参考文档获取：

[bbossgroups 项目下载地址](http://yin-bp.iteye.com/blog/1080824)

**一、概述**

   bboss在安全方面下了不少功夫，为了解决表单重复提交问题提供了动态令牌机制，具体内容如下：

1.增加动态令牌检测过滤器（可独立使用，也可与安全认证过滤器结合使用）

2.增加dtoken标签（用来在表单或者请求中投放动态令牌）

3.增加assertdtoken标签（用来在接收请求的服务器jsp页面头部判断客户端是否正确传递令牌，并检测令牌是否有效，如果没有令牌或者令牌无效，那么拒绝客户端请求）

4.assertdtoken注解（用来在接收请求的服务器mvc控制器方法执行前判断客户端是否正确传递令牌，并检测令牌是否有效，如果没有令牌或者令牌无效，那么拒绝客户端请求）

5.assertticket注解，通过ticket实现系统中sso时，添加了该注解的mvc请求方法要求客户端必须传递ticket参数

令牌的过滤器的详细配置请参考web.xml文件：

[/bboss-mvc/WebRoot/WEB-INF/web.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/web.xml)
令牌过滤器独立使用配置示例）

令牌服务配置文件tokenconf.xml：

[/bboss-security/resources/tokenconf.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-security/resources/tokenconf.xml)

提供三种类型的动态令牌：

1.临时令牌（一次有效）

2.带应用和用户认证签名ticket的临时令牌（一次性有效）

3.带应用和用户认证签名ticket的有效期令牌（指定的时间内有效）

动态令牌机制可以有效解决以下问题：

1.防止表单重复提交

2.请求校验签名及单点登录功能

动态令牌提供了三种存储机制：

1.本地内存存储

2.数据库存储

3.mongodb存储

存储机制是以插件的模式存在，可以自行扩展实现基于redios和memcache的分布式存储机制。数据库令牌存储机制sql脚本：

[/bboss-security/src-token/org/frameworkset/web/token/token.sql](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-security/src-token/org/frameworkset/web/token/token.sql)

  **bboss动态令牌可以在单机模式下使用，也可以在集群环境下使用。集群环境使用时，令牌必须使用数据库存储或者mongodb存储。**

本文介绍**防止表单重复提交**功能，该功能只需要使用临时令牌即可。基于后面两种签名令牌实现请求校验签名及单点登录功能。

**二、过滤器组件**

我们通过web.xml中配置的令牌过滤器来拦截需要做令牌检测的url请求，过滤器组件如下：

1.org.frameworkset.web.token.TokenFilter(单独使用的令牌过滤器)TokenFilter配置示例如下：

Xml代码  

```xml
<filter>  
  
        <filter-name>tokenFilter</filter-name>  
        <filter-class>org.frameworkset.web.token.TokenFilter</filter-class>  
          
        </init-param>  
    <!-- 防止跨站请求过滤器相关参数结束 -->  
    </filter>  
         <filter-mapping>  
        <filter-name>tokenFilter</filter-name>  
        <url-pattern>*.jsp</url-pattern>  
    </filter-mapping>  
    <filter-mapping>  
        <filter-name>tokenFilter</filter-name>  
        <url-pattern>*.page</url-pattern>  
    </filter-mapping>  
```

令牌管理配置文件tokenconf.xml：

**令牌服务器配置文件**

Xml代码

```xml
<properties>  
    <!-- 令牌服务配置-->  
    <property name="token.TokenService" class="org.frameworkset.web.token.TokenService"  
        destroy-method="destroy">  
           
            <property name="ticketdualtime" value="172800000"/>  
            <property name="temptokenlivetime" value="3600000"/>            
            <property name="dualtokenlivetime" value="2592000000"/>  
            <property name="tokenscaninterval" value="1800000"/>  
            <property name="tokenstore" refid="attr:tokenStoreService"/>  
        <!--             <property name="tokenstore" value="mem"/>-->  
              
            <!--   
            <property name="tokenstore" value="mongodb|org.frameworkset.web.token.MongodbTokenStore"/>  
            <property name="tokenstore" value="db|org.frameworkset.web.token.DBTokenStore"/>  
            <property name="tokenstore" value="mem|org.frameworkset.web.token.MemTokenStore"/>-->  
              
            <property name="enableToken" value="true"/>  
<!--             <property name="ALGORITHM" value="RSA"/> -->  
            <property name="tokenfailpath" value="/common/jsp/tokenfail.jsp"/>  
           
    </property>  
    <property name="tokenStoreService" class="org.frameworkset.web.token.MongodbTokenStore">  
        <property name="validateApplication" class="com.xxx.application.service.impl.SYSValidationApplication"/>  
    </property>  
</properties>  
```

参数说明：

tokenfailpath-指定动态令牌校验失败跳转地址，如果没有指定直接响应403错误

tokenstore-指定令牌存储机制，目前提供两种机制：

​            mem：将令牌直接存储在内存空间中

​            mongodb：将令牌存储在mongodb中

db：将令牌存储在db中

ticketdualtime-应用ticket有效时间，必须和web session的有效期保持一致，获取比session会话时间要长，用于签名token的获取，在用户登录应用时申请一个与登录用户关联的ticket。

temptokenlivetime- 临时令牌和签名临时令牌在系统中的存活时间,默认为1小时，超过指定的时间，系统将自动清除超时的令牌，如果指定为-1将不检测， 单位为毫秒， session中的令牌不受影响

dualtokenlivetime-有效期令牌存活时间，超过该时间的有效期签名令牌将被系统自动回收。

tokenscaninterval-指定令牌超时检测时间间隔 单位为毫秒 默认为30分钟，如果需要检测，那么只要令牌持续时间超过tokendualtime 对应的令牌将会被清除，session中的令牌不受影响。

tokenStoreService-令牌存储服务，在其中注入了validateApplication服务,这个校验插件可以在申请令牌和ticket时对应用key和secret进行有效性校验，校验组件必须实现接口：

Java代码

```java
org.frameworkset.web.token.ValidateApplication  
public interface ValidateApplication {  
    public boolean checkApp(String appid,String secret) throws TokenException;  
}  
```

ALGORITHM-临时签名令牌和有效期签名令牌加密机制，默认为RSA，非对称加密算法，也可以采用ECC非对称加密算法。

如果几个应用系统采用统一的令牌服务器时，则应用系统只需要进行客户端的配置即可：

Xml代码

```xml
<!-- 令牌服务配置-->  
<property name="token.TokenService" class="org.frameworkset.web.token.TokenService"  
    >  
       
        <property name="tokenstore" factory-class="com.caucho.hessian.client.HessianProxyFactory" factory-method="create">  
            <construction>  
                <property value="org.frameworkset.web.token.TokenStore"/>       
                <property>  
                    <![CDATA[http://localhost:8081/bboss-mvc/hessian?service=tokenStoreService&container=tokenconf.xml]]>  
                </property>     
            </construction>  
        </property>  
                <property name="client" value="true"/>  
<property name="appid" value="tas"/>  
        <property name="secret" value="2d66d96f-ada4-4e12-a4e4-f4541c0b4bea"/>   
        <property name="enableToken" value="true"/>  
        <!-- 指定动态令牌校验失败跳转地址  
        如果没有指定直接采用redirecturl对应的地址作为跳转地址 -->  
        <property name="tokenfailpath" value="/common/jsp/tokenfail.jsp"/>  
       
</property>  
```

应用系统通过hessian服务http://localhost:8081/bboss-mvc/hessian?

service=tokenStoreService&container=tokenconf.xml获取令牌和校验令牌。

2.org.frameworkset.web.interceptor.AuthenticateFilter（抽象安全认证过滤器，是TokenFilter的子类，所有安全认证过滤器的实现类只需要配置令牌管理的相关参数既可以实现动态令牌机制），具体的配置示例如下：

Xml代码 

```xml
<filter>  
        <filter-name>securityFilter</filter-name>  
        <filter-class>org.frameworkset.web.interceptor.MyFirstAuthFilter</filter-class>  
        。。。。。。。  
    </filter>  
  
  
    <filter-mapping>  
        <filter-name>securityFilter</filter-name>  
        <url-pattern>*.jsp</url-pattern>  
    </filter-mapping>  
    <filter-mapping>  
        <filter-name>securityFilter</filter-name>  
        <url-pattern>*.page</url-pattern>  
    </filter-mapping>  
```

令牌存储mongodb服务器连接配置文件：

[/bboss-security/resources/mongodb.xml](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-security/resources/mongodb.xml)

根据mongodb的部署模式有两种配置方式：集群模式和单机模式

集群配置文件：

Xml代码 

```xml
<properties>  
    <!-- 增加mongodb数据源配置和client工厂类 -->  
    <property name="default" factory-class="org.frameworkset.nosql.mongodb.MongoDB"  
        init-method="init" destroy-method="close" factory-method="getMongoClient">  
          
          
        <property name="serverAddresses" >  
            10.0.15.134:27017  
            10.0.15.134:27018  
            10.0.15.38:27017  
            10.0.15.39:27017  
        </property>  
        <property name="option" >  
            QUERYOPTION_SLAVEOK  
        </property>  
              
        <property name="writeConcern" value="JOURNAL_SAFE"/>  
          
        <property name="readPreference" value="NEAREST"/>       
          
          
    </property>  
</properties>  
```

单mongodb服务器配置：

Xml代码

```xml
<properties>  
    <!-- 增加mongodb数据源配置和client工厂类 -->  
    <property name="default" factory-class="org.frameworkset.nosql.mongodb.MongoDB"  
        init-method="init" destroy-method="close" factory-method="getMongoClient">  
          
  
        <property name="serverAddresses" >  
            192.168.1.100:27016  
        </property>  
        <property name="option" ></property>  
              
          
        <property name="writeConcern" value="JOURNAL_SAFE"/>  
          
        <property name="readPreference" value=""/>          
          
          
    </property>  
</properties>  
```

**三、dtoken标签**

有了令牌过滤器后，我们就需要在在客户端生成动态令牌，并将其放置在：

1.表单中input hidden元素中

2.url的参数串中

3.ajax请求的json参数中

这些都可以通过dtoken标签来实现，dtoken定义在[pager-taglib.tld](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/pager-taglib.tld)文件中，三种方式分别举例说明如下：

1.表单中input hidden元素中

Html代码

```html
<form action="sayHelloNumber.page" method="post">  
            <pg:dtoken/>  
              
            <table cellspacing="0" >  
                <tbody>  
                    <tr><td>  
                            请输入您的幸运数字：  
                        <input name="name" type="text">  
                        </td>  
                    </tr>  
                    <tr>  
                        <td>  
                            来自服务器的问候：  
                            aa</td>  
                          
  
                          
                    </tr>  
                    <tr>  
                        <td><input type="submit" name="确定" value="确定"></td>                         
                    </tr>  
                </tbody>  
            </table></form>
```

<pg:dtoken/>标签将生成一个隐藏的动态令牌参数，将随着表单的提交而提交，该令牌只在本次请求中有效，不能被重复使用，这样就达到了解决了表单重复提交和跨站攻击的问题。

2.url的参数串中

<a href="/aaa/xxx.page?<pg:dtoken element='param'/>"

通过element='param'指定令牌以参数名=令牌的方式追加在url的参数中，随着url一起提交。该令牌只在本次url请求中有效，不能被重复使用，这样就达到了解决了表单重复提交和跨站攻击的问题。

3.ajax请求的json参数中

$("#queryresult").load("sayHelloEnums.page",{sex:$("#sex").val(),<pg:dtoken element="json"/>});
通过element=="json"指定令牌以参数名:令牌的json参数格式追加在ajax参数中，随着ajax请求一起被提交。该令牌只在本次ajax请求中有效，不能被重复使用，这样就达到了解决了表单重复提交和跨站攻击的问题。

**四、assertdtoken标签和assertdtoken注解**

通过上述三种方式能够有效地防止表单重复提交，但是能不能有效地防止跨站攻击呢，答案是不能，因为服务端在校验令牌时，首先检测有没有令牌被提交过来，如果没有则忽略令牌检查，认为请求是合法的，这样黑客可以根据该规则尝试将令牌参数从请求参数中去除来达到攻击的目的。为了防止这个漏洞，我们需要借助于assertdtoken标签和assertdtoken注解在服务器端强制要求令牌参数。

assertdtoken标签主要用于接收请求的jsp页面头部，例如：

<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>

<pg:assertdtoken/>

只有assertdtoken检测到令牌并且令牌合法，才执行后续的处理操作，否则直接跳转到过滤器参数tokenfailpath对应的地址或者抛出403异常。

assertdtoken注解用在bboss mvc控制器方法上，用来标注mvc控制器方法强制要求进行动态令牌校验，只有检测到令牌并且令牌合法，才继续开始执行控制器方法，否则直接跳转到过滤器参数tokenfailpath对应的地址或者抛出403异常。

assertdtoken注解使用实例：

Java代码

```java
@AssertDToken  
    public String sayHelloNumber(@RequestParam(name = "name") int ynum,  
            ModelMap model)  
    {  
  
        if (ynum != 0)  
        {  
            model.addAttribute("serverHelloNumber", "幸运数字为[" + ynum + "]！");  
        }  
        else  
            model.addAttribute("serverHelloNumber", "幸运数字为[" + ynum  
                    + "]！，好像有点不对哦。");  
  
        return "path:sayHello";  
    }  
```

**五、生成令牌的java方法**

组件接口：org.frameworkset.web.token.TokenService

组件支持调用模式：hessian模式，webservice服务模式，http服务模式（后续章节详细介绍）

令牌组件提供了以下接口方法：

Java代码

```java
 public abstract String genToken(ServletRequest request, String fid,  
            boolean cache) throws TokenException;  
  
    public abstract String buildDToken(String elementType,  
            HttpServletRequest request) throws TokenException;  
  
    public abstract String buildDToken(String elementType, String jsonsplit,  
            HttpServletRequest request, String fid) throws TokenException;  
  
    /** 
     * 生成隐藏域令牌,输出值为： 
     * <input type="hidden" name="_dt_token_" value="-1518435257"> 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public abstract String buildHiddenDToken(HttpServletRequest request)  
            throws TokenException;  
  
    /** 
     * 生成json串令牌 
     * 如果jsonsplit为'，则输出值为： 
     * _dt_token_:'1518435257' 
     * 如果如果jsonsplit为",则输出值为： 
     * _dt_token_:"1518435257" 
     * @param jsonsplit 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public abstract String buildJsonDToken(String jsonsplit,  
            HttpServletRequest request) throws TokenException;  
  
    /** 
     * 生成url参数串令牌 
     * 输出值为： 
     * _dt_token_=1518435257 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public abstract String buildParameterDToken(HttpServletRequest request)  
            throws TokenException;  
  
    /** 
     * 只生成令牌，对于这种方式，客户端必须将该token以参数名_dt_token_传回服务端，否则不起作用 
     * 输出值为： 
     * 1518435257 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public abstract String buildDToken(HttpServletRequest request)  
            throws TokenException;  
  
    public abstract String buildDToken(String elementType, String jsonsplit,  
            HttpServletRequest request, String fid, boolean cache)  
            throws TokenException;  
  
  
    public abstract String genDualTokenWithDefaultLiveTime(String appid,  
            String secret, String ticket) throws Exception;  
  
    public abstract String genTicket(String account, String worknumber,  
            String appid, String secret) throws TokenException;  
  
    public abstract boolean isEnableToken();  
获取带认证的临时一次性令牌-返回的token包含令牌和用户账号信息  
public String genAuthTempToken(  
            String appid,//应用标识  
            String secret,//应用口令  
            String ticket) //访问票据-用于单点登录  
            throws Exception;  
获取带认证的有效期令牌-返回的token包含令牌和用户账号信息  
public  String genDualToken(  
            String appid, //应用标识  
            String secret, //应用口令  
             String ticket,//访问票据-用于单点登录  
  
 long dualtime) 令牌有效时间  
            throws Exception;  
获取给应用颁发的公钥  
public  String getPublicKey(  
            String appid, //应用标识  
            String secret) //应用口令  
            throws Exception;  
获取一次性的临时令牌  
public String genTempToken() throws Exception;  
```

具体的使用方法为：

Java代码

```java
@WebService(name="TokenService",targetNamespace="com.sany.common.action.TokenService")  
public class TokenController implements TokenService {  
    /** 
     * 获取令牌请求 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public @ResponseBody String getToken(HttpServletRequest request) throws TokenException  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().buildDToken(request);  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    public @ResponseBody String genTempToken() throws Exception  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().genTempToken();  
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
    public @ResponseBody String genAuthTempToken(String appid,String secret,String ticket) throws Exception  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().genAuthTempToken(appid, secret, ticket);  
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
    public @ResponseBody String genDualToken(String appid,String secret,String ticket) throws Exception  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            long dualtime = 30l*24l*60l*60l*1000l;  
            return  TokenHelper.getTokenService().genDualToken(appid, secret, ticket,dualtime);  
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
    public @ResponseBody String genDualTokenWithDefaultLiveTime(String appid,String secret,String ticket) throws Exception  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
  
            return  TokenHelper.getTokenService().genDualTokenWithDefaultLiveTime(appid, secret, ticket);  
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
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().getPublicKey(appid, secret);  
        }  
        else  
        {  
            return null;  
        }  
    }  
      
    /** 
     * 获取令牌请求 
     * http://localhost:8081/SanyPDP/token/getParameterToken.freepage 
     * @param request 
     * @return 
     * @throws TokenException  
     */  
    public @ResponseBody String getParameterToken(HttpServletRequest request) throws TokenException  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().buildParameterDToken(request);  
        }  
        else  
        {  
            return null;  
        }  
    }  
    public @ResponseBody String genTicket(String account,String worknumber,String appid,String secret) throws TokenException  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().genTicket( account, worknumber, appid, secret);  
        }  
        else  
        {  
            return null;  
        }  
    }  
}  
```

令牌校验接口实例-令牌校验服务：

Java代码

```java
@WebService(name="CheckTokenService",targetNamespace="com.sany.common.action.CheckTokenService")  
public class CheckTokenContoller implements CheckTokenService{  
      
    public @ResponseBody(datatype="json") TokenResult checkToken(String appid,String secret,String token) throws TokenException  
    {  
          
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
              
            return  TokenHelper.getTokenService().checkToken(appid,secret,token);  
        }  
        else  
        {  
            return null;  
        }  
    }  
    public @ResponseBody Integer checkTempToken(String token) throws TokenException  
    {  
        if(TokenHelper.isEnableToken())//如果开启令牌机制就会存在memTokenManager对象，否则不存在  
        {  
            return  TokenHelper.getTokenService().checkTempToken(token);  
        }  
        else  
        {  
            return TokenStore.token_request_validateresult_notenabletoken;  
        }  
    }  
  
  
}  
```

动态令牌机制可以有效解决以下问题：

1.防止表单重复提交

2.请求校验签名及单点登录功能。

  **六.ticket和签名令牌获取方法**

下面介绍一下ticket和token获取的三种方法（hessian，webservice，http）

参数准备：

String appid = "appid";

String secret = "xxxxxxxxxxxxxxxxxxxxxx";

String account = "yinbp";

String worknumber = "10006673";  

hessian

Java代码

```java
客户端获取令牌服务组件- hessian服务方式申请token  
//hessian服务方式申请token  
HessianProxyFactory factory = new HessianProxyFactory();  
//String url = "http://localhost:8081/context/hessian?service=tokenService";  
String url = "http://localhost:8081/SanyPDP/hessian?service=tokenService";  
TokenService tokenService = (TokenService) factory.create(TokenService.class, url);  
//通过hessian根据账号或者工号获取ticket  
  
String ticket = tokenService.genTicket(account, worknumber, appid, secret);  
String token = tokenService.genAuthTempToken(appid, secret, ticket);  
```

webservice

Java代码

```java
客户端获取令牌服务组件- webservice服务方式申请token  
url = "http://localhost:8081/SanyPDP/cxfservices/tokenService";  
JaxWsProxyFactoryBean WSServiceClientFactory = new  JaxWsProxyFactoryBean();  
WSServiceClientFactory.setAddress(url);  
WSServiceClientFactory.setServiceClass(TokenService.class);  
tokenService = (TokenService)WSServiceClientFactory.create();  
//通过webservice根据账号或者工号获取ticket  
//String ticket = tokenService.genTicket(account, worknumber, appid, secret);  
token = tokenService.genAuthTempToken(appid, secret, ticket);  
```

http:

客户端获取令牌服务组件- http服务方式申请token

Java代码

```java
url = "http://localhost:8081/SanyPDP/token/genAuthTempToken.freepage?appid="+appid + "&secret="+secret + "&ticket="+ticket;  
//url = "http://10.25.192.142:8081/SanyPDP/token/genDualToken.freepage?appid="+appid + "&secret="+secret + "&account="+account;  
token = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
//通过http根据账号或者工号获取ticket  
//url = "http://10.25.192.142:8081/SanyPDP/token/genTicket.freepage?appid="+appid + "&secret="+secret + "&account="+account + "&worknumber="+worknumber;  
//String ticket = = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url); 
```

令牌校验服务和令牌申请服务类似这里不做进一步介绍。

**七.ticket实现sso**

org.frameworkset.web.token.ws.TokenService //获取ticket组件

获取ticket服务webservice地址：
http://10.0.15.223/BbossToken/cxfservices/tokenService?wsdl

获取ticket服务hessian地址：
http://10.0.15.223/BbossToken/hessian?service=tokenService

org.frameworkset.web.token.ws.CheckTokenService//校验服务组件

校验服务webservice地址：
http://10.0.15.222/BbossToken/cxfservices/checktokenService?wsdl

校验服务hessian地址：
http://10.0.15.223/BbossToken/hessian?service=checktokenService

两步实现ticketsso ：

第一步 来源应用系统登录时使用appid，appsecret，userAccount（账号），userWorknumber（工号）向令牌服务器申请ticket并保存到本地缓存中

Java代码

```java
String appid = "tas";  
String secret = "2d66d96f-ada4-4e12-a4e4-f4541c0b4bea";  
  
String account = "marc";//如果使用工号则loginType为2，否则为1  
  
String worknumber = "10006857";  
  
  
HessianProxyFactory factory = new HessianProxyFactory();  
  
String url = "http://10.0.15.223/SanyToken/hessian?service=tokenService";//令牌服务器获取ticket服务地址  
TokenService tokenService = (TokenService) factory.create(TokenService.class, url);  
//通过hessian根据账号或者工号获取ticket  
String ticket = tokenService.genTicket(account, worknumber, appid, secret);  
```

第二步，使用ticket访问目标系统，将ticket作为参数传递给目标系统，目标系统请求过滤器拦截到ticket参数，拿到目标系统存储的appid和secret信息，与ticket一起到令牌服务器校验ticket的合法性，如果appid和secret校验合法，并且ticket也合法，则返回用户身份信息（账号和工号）给目标应用系统。

源系统通过ticket单点访问第三方系统url实例：

http://aa.com.cn/myapp/test.page?_dt_ticket_= + ticket ;

目标系统校验ticket并获取sso的用户信息方法：

Java代码 

```java
HessianProxyFactory factory = new HessianProxyFactory();  
String url = "http://10.0.15.223/BbossToken/hessian?service=checktokenService";//令牌服务器校验ticket服务地址  
org.frameworkset.web.token.ws.CheckTokenService  checkTokenService = (CheckTokenService) factory.create(org.frameworkset.web.token.ws.CheckTokenService.class, url);  
org.frameworkset.web.token.ws.TokenCheckResponse tokenCheckResponse = checkTokenService.checkTicket(appid, secret, ticket);  
System.out.println(tokenCheckResponse.getResultcode());  
System.out.println(tokenCheckResponse.getUserAccount());  
System.out.println(tokenCheckResponse.getWorknumber());  
```

tokenCheckResponse.getResultcode()得到的值为"ok"时表示ticket校验成功，否则返回的就是具体的错误码。

  **八、bboss动态令牌实现原理总结**

bboss动态令牌机制采用动态令牌和session相结合的方式产生客户端令牌，一次请求产生一个唯一令牌，令牌识别采用客户端令牌和服务端session标识混合的方式进行判别，如果客户端令牌和服务端令牌正确匹配，则允许访问，否则认为用户为非法用户并阻止用户访问并跳转到tokenfailpath参数对应的地址，默认为空（直接响403）。  

令牌存储机制通过参数tokenstore指定，包括两种，内存存储和session存储，默认为session存储，当令牌失效（匹配后立即失效，或者超时失效）后，系统自动清除失效的令牌；采用session方式存储令牌时，如果客户端页面没有启用session，那么令牌还是会存储在内存中。

令牌生命周期：客户端的令牌在服务器端留有存根，当令牌失效（匹配后立即失效，或者超时失效）后，系统自动清除失效的令牌；当客户端并没有正确提交请求，会导致服务端令牌存根变为垃圾令牌，需要定时清除这些 垃圾令牌；如果令牌存储在session中，那么令牌的生命周期和session的生命周期保持一致，无需额外清除机制；如果令牌存储在内存中，那么令牌的清除由令牌管理组件自己定时扫描清除，定时扫描时间间隔由tokenscaninterval参数指定，单位为毫秒，默认为30分钟，存根保存时间由tokendualtime参数指定，默认为1个小时。

可以通过enableToken参数配置指定是否启用令牌检测机制，true检测，false不检测，默认为false不检测。

最后再总结一下：

1.tokenFilter拦截需要进行令牌校验客户端请求并执行校验逻辑、同时管理令牌的生命周期和提供定时清楚垃圾令牌机制

2.dtoken标签在客户端表单、url请求和ajax请求中投放动态令牌，仅仅在客户端放置动态随机令牌，可有效防止表单重复提交问题，但是不能彻底解决跨站攻击问题（因为狡猾的黑客会想尽办法去除令牌，绕过服务端令牌校验机制，从而达成跨站攻击目标），那么我们需要assertdtoken标签和assertdtoken注解来防止这种欺骗行为。

3.assertdtoken标签在接收请求的服务器jsp页面头部判断客户端是否正确传递令牌，并检测令牌是否有效，如果没有令牌或者令牌无效，那么拒绝客户端请求

4.assertdtoken注解在接收请求的服务器mvc控制器方法执行前判断客户端是否正确传递令牌，并检测令牌是否有效，如果没有令牌或者令牌无效，那么拒绝客户端请求

