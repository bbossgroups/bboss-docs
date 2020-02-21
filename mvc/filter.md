### bboss 安全认证过滤器功能介绍

[bboss 3.6](https://github.com/bbossgroups/bbossgroups-3.5)新增了安全认证过滤器抽象类org.frameworkset.web.interceptor.AuthenticateFilter

废除之前版本中的mvc安全认证拦截器org.frameworkset.web.interceptor.AuthenticateInterceptor

AuthenticateFilter过滤器具备两个功能：

身份检测-保证合法用户访问页面

权限检测-保证合法用户在具备访问权限的情况下才能访问页面

AuthenticateFilter过滤器具有以下属性

**preventDispatchLoop** = false;//循环跳转检测开关，true启用，false禁用，默认为false

**http10Compatible** = true; //whether to stay compatible with HTTP 1.0 clients,true标识兼容，false标识不兼容，默认为true

**redirecturl** = "/login.jsp"; //指定检测失败重定向地址，默认为login.jsp,即安全认证检测失败跳转向指定的页面

/**

*认证和权限检测失败页面跳转方式，有以下三个值，默认为redirect

*include

*redirect

*forward

*/

**directtype** = "redirect";

//明确指出需要检测的页面范围，多个用逗号分隔，可选，如果没有配置则扫描所有页面（忽略patternsExclude指定的相关页面）

//可以是指定包含通配符*的页面地址，用来模糊匹配多个页面

**patternsInclude**

//明确指出不需要检测的页面范围，多个用逗号分隔，可选，如果没有配置则扫描所有页面或者扫描patternsInclude指定的页面

//可以是指定包含通配符*的页面地址，用来模糊匹配多个页面

**patternsExclude**

//明确指出需要做权限检测的页面范围，多个用逗号分隔，可选，如果没有配置则扫描所有页面（忽略permissionExclude指定的相关页面）

//可以是指定包含通配符*的页面地址，用来模糊匹配多个页面

**permissionInclude**

//明确指出不需要检测的页面范围，多个用逗号分隔，可选，如果没有配置则扫描所有页面或者扫描permissionInclude指定的页面

//可以是指定包含通配符*的页面地址，用来模糊匹配多个页面

  **permissionExclude**

//enablePermissionCheck属性可以屏蔽权限检测机制,true启用，false禁用，默认值为false

**enablePermissionCheck**


//failedback和failedbackurlpattern两个参数，用来记录请求页面地址，以便在登录后重新跳转到请求页面，  

**failedback**为true时，符合**failedbackurlpattern**中配置的请求页面时，会将请求地址作为参数传递到登陆页面，对应的参数名称为**accesscontrol_check_referpath**,如果failedbackurlpattern为空，则所有的需要认证的请求页面都会作为参数传递到登陆页面。重定向功能的使用可以参考文档：

[bboss安全认证过滤器认证后重定向到请求页面功能介绍](http://yin-bp.iteye.com/blog/2128726)

AuthenticateFilter提供了两个抽象方法：

Java代码

```java
protected boolean check(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta)  
```

在应用系统中，您可以继承org.frameworkset.web.interceptor.AuthenticateFilter并实现抽象方法check，从而实现具体的安全认证检测类来进行用户是否登录检测，如果已经登录则返回true，没有登录则返回false。

Java代码

```java
protected abstract boolean checkPermission(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta,String uri);  
```

应用系统可以继承实现checkPermission方法来实现url权限检测功能，返回true表示有url访问权限，false表示无url访问权限，无权限时将跳转到authorfailedurl属性对应的提示页面，enablePermissionCheck属性可以屏蔽权限检测机制。

以下是一个具体实现示例：

Java代码

```java
public class SYSAuthenticateFilter extends AuthenticateFilter  
{  
         /** 
     * 用户身份验证 
     */  
    protected boolean check(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta)  
    {  
        AccessControl control = AccessControl.getInstance();  
        boolean result = control.checkAccess(request, response, false);  
        if(result)  
        {  
            request.setAttribute(AccessControl.accesscontrol_request_attribute_key,control);  
        }  
        return result;  
          
          
    }  
  
    /** 
     * url权限判断 
     */  
    @Override  
    protected boolean checkPermission(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta, String uri) {  
        AccessControl control = AccessControl.getAccessControl();  
        return control.checkURLPermission(uri, "visible", "column");  
    }  
  
}  
```

安全过滤器在web.xml中的配置方法如下：

Xml代码

```xml
<filter>  
    <filter-name>securityFilter</filter-name>  
    <filter-class>com.frameworkset.platform.security.SYSAuthenticateFilter</filter-class>  
    <init-param>  
      <param-name>patternsExclude</param-name>  
      <param-value>  
            /sysmanager/logoutredirect.jsp,  
            /login.jsp,  
            /logout.jsp  
           </param-value>  
    </init-param>  
    <init-param>  
      <param-name>redirecturl</param-name>  
      <param-value>/sysmanager/logoutredirect.jsp</param-value>  
    </init-param>  
 <!--  
    安全认证过滤器AuthenticateFilter中包含checkPermission抽象方法，应用系统可以继承实现这个方法来实现url权限检测功能，返回true表示有url访问权限，false表示无url访问权限，无权限时将跳转到authorfailedurl属性对应的提示页面  
    enablePermissionCheck属性可以屏蔽权限检测机制  
    -->  
     <init-param>   
      <param-name>enablePermissionCheck</param-name>   
      <param-value>true</param-value>   
    </init-param>   
    
    <init-param>   
      <param-name>permissionExclude</param-name>   
      <param-value>   
        /login.jsp,/authorfailed.jsp  
       </param-value>   
    </init-param>   
<!--     <init-param>  -->  
<!--       <param-name>permissionInclude</param-name>  -->  
<!--       <param-value>  -->  
<!--         /login.jsp -->  
<!--        </param-value>  -->  
<!--     </init-param>  -->  
     
    <init-param>   
      <param-name>authorfailedurl</param-name>   
      <param-value>/authorfailed.jsp</param-value>   
    </init-param>  
    <init-param>   
      <param-name>preventDispatchLoop</param-name>   
      <param-value>false</param-value>   
    </init-param>       
    <init-param>  
            <param-name>failedback</param-name>  
            <param-value>true</param-value>  
        </init-param>  
        <init-param>  
            <param-name>failedbackurlpattern</param-name>  
            <param-value>/bbossdesktop/index.page,/bbossdesktop/indexcommon.page</param-value>  
        </init-param>  
  </filter>  
   
  <filter-mapping>  
    <filter-name>securityFilter</filter-name>  
    <url-pattern>*.jsp</url-pattern>  
  </filter-mapping>  
  <filter-mapping>  
    <filter-name>securityFilter</filter-name>  
    <url-pattern>*.page</url-pattern>  
  </filter-mapping>  
  <filter-mapping>  
    <filter-name>securityFilter</filter-name>  
    <url-pattern>*.frame</url-pattern>  
  </filter-mapping>  
```

当采用AuthenticateFilter过滤器后，原来在bboss-mvc.xml中的认证拦截器就可以去掉了，具体操作方法为，
定位到bboss-mvc.xml,注释或者去掉SYSAuthenticateInterceptor节点内容：

Java代码

```java
<!-- 配置全局控制器方法拦截器 -->  
     <property name="org.frameworkset.web.servlet.gloabel.HandlerInterceptors" >  
        <list componentType="bean">  
            <!--<property class="com.frameworkset.platform.security.SYSAuthenticateInterceptor"> -->  
                <!-- 配置认证检查拦截器拦截url模式规则 -->  
                <!-- <property name="patternsInclude">  
                    <list componentType="string">  
                        <property value="/**/*.page"/>  
                    </list>  
                </property>-->  
                <!-- 配置认证检查拦截器不拦截url模式规则 -->  
                <!--<property name="patternsExclude">  
                    <list componentType="string">  
                        <property value="/uddi/queryservice/main.page"/>  
                        <property value="/uddi/queryservice/verify.page"/>  
                        <property value="/uddi/queryservice/requesterVerify.page"/>  
                        <property value="/uddi/queryservice/detail.page"/>  
                        <property value="/uddi/servicemanage/basic.page"/>      
                        <property value="/uddi/servicemanage/servicedesc.page"/>  
                        <property value="/uddi/servicemanage/listmetadata.page"/>  
                        <property value="/uddi/queryservice/visitpermission.page"/>  
                          
                        <property value="/uddi/queryservice/logout.page"/>  
                          
                          
                    </list>  
                </property>  
                <property name="redirecturl" value="/sysmanager/logoutredirect.jsp"/>  
            </property>-->  
        </list>  
     </property>  
```

目前3.6版本还没有发布到sourceforge中，但是大家可以从github下载最新的源码构建jar包来进行升级：
github源码地址：
https://github.com/bbossgroups/bbossgroups-3.5

mvc依赖的jar说明请参考文章中的mvc部分（bboss框架的包需要全部重新从相应的子工程中构建新的包来替换和升级）：
http://yin-bp.iteye.com/blog/1143994