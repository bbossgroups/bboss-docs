### bboss 动态令牌使用示例-ajax请求获取和传递令牌

bboss 动态令牌使用示例-ajax请求获取和传递令牌。bboss动态令牌实现机制参考文档：

[bboss 动态令牌机制轻松搞定网站跨站攻击和表单重复提交问题](http://yin-bp.iteye.com/blog/1662594)

本文内容：

1.如何编写自己的令牌生成控制器（基于bboss mvc）

2.如何通过ajax申请令牌和传递令牌

接下来进入正文。

1.编写令牌生成控制器

Java代码

```java
package com.bboss.common.action;  
  
import javax.servlet.http.HttpServletRequest;  
  
import org.frameworkset.util.annotations.ResponseBody;  
import org.frameworkset.web.token.MemTokenManager;  
  
public class TokenController {  
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
      
    /** 
     * 获取令牌请求 
     * http://localhost:8081/PDP/token/getParameterToken.freepage 
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
}  
```

编写mvc控制器配置文件bboss-token.xml：

Xml代码 

```xml
<properties>  
    <!-- 生成令牌控制配置文件  
    author：biaoping.yin  
    CopyRight：bboss  
    Date:2011.4.13  
    -->  
    <property name="/token/*.freepage" class="com.bboss.common.action.TokenController" />  
</properties>  
```

将bboss-token.xml添加到web.xml中：

Xml代码 

```xml
<servlet>  
        <servlet-name>mvc</servlet-name>  
        <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>/WEB-INF/conf/bboss-*.xml,/WEB-INF/conf/token/bboss-token.xml  
            </param-value>  
        </init-param>  
        <init-param>  
            <param-name>messagesources</param-name>  
            <param-value>/WEB-INF/messages_pdp,/WEB-INF/messages_pdp_common,/WEB-INF/conf/appbom/messages_appbom</param-value>  
        </init-param>  
        <init-param>  
            <param-name>useCodeAsDefaultMessage</param-name>  
            <param-value>true</param-value>  
        </init-param>  
        <load-on-startup>0</load-on-startup>  
    </servlet>  
     <servlet-mapping>  
        <servlet-name>mvc</servlet-name>  
        <url-pattern>*.page</url-pattern>  
    </servlet-mapping>  
    <!-- freepage这种地址securityFilter安全认证过流器将不拦截，安全放行，如果无需任何会话信息  
    可以将请求后缀定义为freepage  
     -->  
     <servlet-mapping>  
        <servlet-name>mvc</servlet-name>  
        <url-pattern>*.freepage</url-pattern>  
    </servlet-mapping>  
```

ok，到此生成令牌的控制器已经写好，我们可以通过以下请求：

http://localhost:8081/PDP/token/getParameterToken.freepage

获取到类似以下格式的令牌：

dt_token_=1518435257 

其中的http://localhost:8081/PDP是应用访问上下文，根据自己的情况进行修改即可。

2.通过ajax申请令牌并使用令牌

我们以jquery的ajax 方法申请令牌，然后构建一个带令牌的url请求，js代码如下：

Java代码

```java
$.ajax({url:"http://localhost:8081/PDP/token/getParameterToken.freepage", //指定申请令牌的url  
            type: "POST",  
            success : function(token){//成功申请令牌，token格式为_dt_token_=1518435257  
                var url = "aaa.page";  
                if(token )//将令牌作为请求参数附加到url中  
                {  
                    if(url.indexOf("?") > 0)  
                    {  
                        url = url + "&"+token;  
                    }  
                    else  
                    {  
                        url = url + "?"+token;  
                    }  
                }  
                window.open(url);//打开带令牌的url对应的页面  
            }  
    });  
```

示例到此完毕。欢迎大家反馈问题和提出宝贵建议。