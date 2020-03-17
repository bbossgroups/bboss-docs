### bboss mvc基础配置介绍

bboss mvc基础配置介绍，本文重点介绍bboss-mvc.xml文件中的一些有意义的配置以及其什么时候被加载。

**1.bboss-mvc.xml加载**

首先介绍bboss-mvc.xml文件什么时候会被加载，先谈一下web.xml中bboss mvc的请求处理servlet的配置：

Xml代码

```xml
<servlet>  
        <servlet-name>mvcdispather</servlet-name>  
        <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <!--如果有多个目录需要加载，请用,号分隔-->  
            <param-value/WEB-INF/conf/bboss-*.xml</param-value>  
        </init-param>  
        <load-on-startup>0</load-on-startup>  
    </servlet>  
    <servlet-mapping>  
        <servlet-name>mvcdispather</servlet-name>  
        <url-pattern>*.page</url-pattern>  
    </servlet-mapping>  
```

其中的/WEB-INF/conf/bboss-*.xml很关键，他会扫描/WEB-INF/conf下所有以bboss-开头的mvc xml配置文件并加载之；这里可以配置多个目录，用逗号分隔，只要bboss-mvc.xml放在其中的一个目录下就会在应用启动的时候被加载。

**2.bboss-mvc.xml中经常会用到的配置**

**2.1 文件上传插件配置**

Xml代码

```xml
<property name="multipartResolver"   f:encoding="UTF-8"  
        class="org.frameworkset.web.multipart.commons.CommonsMultipartResolver"/>  
```

常用的配置属性有：

encoding，一般被配置为utf-8,用来对附件上传表单中的请求参数进行单独的编码转换，避免中文乱码问题。

maxUploadSize：允许上传的最大文件大小，单位为byte，为-1时不限制大小。

maxInMemorySize：允许在内存中存放的最大文件块大小，以byte为单位，默认为10K

uploadTempDir:指定上传文件存放的临时目录，默认为应用临时目录

**2.2 文件下载、json响应、字符串响应插件配置**

Xml代码

```xml
<property name="httpMessageConverters">  
        <list>  
            <property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter"/>  
            <property class="org.frameworkset.http.converter.StringHttpMessageConverter"/>  
            <property class="org.frameworkset.http.converter.FileMessageConvertor"/>  
        </list>          
     </property>  
```

如果不指定"org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter，那么控制方法中以下写法将不能正常工作：

Java代码

```java
public @ResponseBody(datatype="json") GouWuChe datagrid_data()  
```

也就是说不能正确地将对象GouWuChe 转换为json对象返回给请求客户端。

如果不指定"org.frameworkset.http.converter.FileMessageConvertor，那么控制器方法的以下写法将不能正常工作：

Java代码

```java
public @ResponseBody File downloadFileFromFile(@RequestParam(name = "fileid") String fileid)  
            throws Exception  
```

也就是说不能正常地下载返回的File对应的文件。

如果不指定"org.frameworkset.http.converter.StringHttpMessageConverter，那么控制器方法的以下写法将不能正常工作：

Java代码

```java
public @ResponseBody(charset = "GBK")  
    String sayHelloEnum(@RequestParam(name = "sex") SexType type)  
```

也就是说不能正常地将返回值String响应给客户端。

对于ajax请求响应出现的中文乱码问题，解决办法有两个，一个是直接在StringHttpMessageConverter上通过responseCharset属性全局指定响应字符编码集，例如UTF-8或者GBK：

Xml代码 

```xml
<property class="org.frameworkset.http.converter.StringHttpMessageConverter" f:responseCharset="UTF-8"/>  
```

Xml代码

```xml
<property class="org.frameworkset.http.converter.StringHttpMessageConverter" f:responseCharset="GBK"/>  
```

具体使用何种字符集取决于项目中采用的字符集。

另外一种解决办法就是通过返回参数注解ResponseBody的charset 属性单独对请求方法的响应字符串进行编码,例如：

Java代码

```java
public @ResponseBody(charset = "UTF-8")  
    String sayHelloEnum(@RequestParam(name = "sex") SexType type)  
```

**2.3 全局拦截器配置（最常见的就是页面保护机制配置）**

拦截器配置在bboss-mvc.xml的org.frameworkset.web.servlet.gloabel.HandlerInterceptors节点中，所有的拦截器只需要实现接口

org.frameworkset.web.servlet.HandlerInterceptor

接口提供了一下方法：

Java代码

```java
/** 
     * Intercept the execution of a handler. Called after HandlerMapping determined 
     * an appropriate handler object, but before HandlerAdapter invokes the handler. 
     * <p>DispatcherServlet processes a handler in an execution chain, consisting 
     * of any number of interceptors, with the handler itself at the end. 
     * With this method, each interceptor can decide to abort the execution chain, 
     * typically sending a HTTP error or writing a custom response. 
     * @param request current HTTP request 
     * @param response current HTTP response 
     * @param handler chosen handler to execute, for type and/or instance evaluation 
     * @return <code>true</code> if the execution chain should proceed with the 
     * next interceptor or the handler itself. Else, DispatcherServlet assumes 
     * that this interceptor has already dealt with the response itself. 
     * @throws Exception in case of errors 
     */  
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, HandlerMeta handlerMeta)  
        throws Exception;  
  
    /** 
     * Intercept the execution of a handler. Called after HandlerAdapter actually 
     * invoked the handler, but before the DispatcherServlet renders the view. 
     * Can expose additional model objects to the view via the given ModelAndView. 
     * <p>DispatcherServlet processes a handler in an execution chain, consisting 
     * of any number of interceptors, with the handler itself at the end. 
     * With this method, each interceptor can post-process an execution, 
     * getting applied in inverse order of the execution chain. 
     * @param request current HTTP request 
     * @param response current HTTP response 
     * @param handler chosen handler to execute, for type and/or instance examination 
     * @param modelAndView the <code>ModelAndView</code> that the handler returned 
     * (can also be <code>null</code>) 
     * @throws Exception in case of errors 
     */  
    void postHandle(  
            HttpServletRequest request, HttpServletResponse response, HandlerMeta handlerMeta, ModelAndView modelAndView)  
            throws Exception;  
  
    /** 
     * Callback after completion of request processing, that is, after rendering 
     * the view. Will be called on any outcome of handler execution, thus allows 
     * for proper resource cleanup. 
     * <p>Note: Will only be called if this interceptor's <code>preHandle</code> 
     * method has successfully completed and returned <code>true</code>! 
     * @param request current HTTP request 
     * @param response current HTTP response 
     * @param handler chosen handler to execute, for type and/or instance examination 
     * @param ex exception thrown on handler execution, if any 
     * @throws Exception in case of errors 
     */  
    void afterCompletion(  
            HttpServletRequest request, HttpServletResponse response, HandlerMeta handlerMeta, Exception ex)  
            throws Exception;  
```

写好监听器，就可以进行配置了：

Xml代码

```xml
<!-- 配置全局控制器方法拦截器 -->  
     <property name="org.frameworkset.web.servlet.gloabel.HandlerInterceptors" >  
        <list componentType="bean">  
            <property class="com.bboss.security.XXXXInterceptor"/>  
            <property class="com.bboss.security.SYSAuthenticateInterceptor">  
                <!-- 配置认证检查拦截器拦截url模式规则 -->  
                <!-- <property name="patternsInclude">  
                    <list componentType="string">  
                        <property value="/**/*.page"/>  
                    </list>  
                </property>-->  
                <!-- 配置认证检查拦截器不拦截url模式规则 -->  
                <property name="patternsExclude">  
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
                <property name="redirecturl" value="/login.jsp"/>  
            </property>  
        </list>  
```

**2.4 url重写规则配置**

这个是用来配置所有控制器方法跳转地址是否需要进行url重写以及怎么重写的规则进行配置，以下就是最常用的配置：

Xml代码

```xml
<property name="viewResolver" class="org.frameworkset.web.servlet.view.InternalResourceViewResolver" singlable="true">  
        <property name="viewClass" value="org.frameworkset.web.servlet.view.JstlView"/>  
        <property name="prefix" value=""/>  
        <property name="suffix" value=""/>   </property>  
```

以上配置其实并没有设置重写规则，因为url重写前置prefix被配置为"",重写后缀suffix也被配置为"",这样控制跳转地址配置或者直接返回的地址串就是实际的物理url地址，mvc框架不做任何处理。

除非有特殊要求的项目才会开启url重写规则，也就是配置url重写前缀prefix和重写后缀suffix：

Xml代码

```xml
<property name="viewResolver" class="org.frameworkset.web.servlet.view.InternalResourceViewResolver" singlable="true">  
        <property name="viewClass" value="org.frameworkset.web.servlet.view.JstlView"/>  
        <property name="prefix" value="/jsp/"/>  
        <property name="suffix" value=".jsp"/>   </property> 
```

url重写前置prefix被配置为"/jsp/",重写后缀suffix也被配置为".jsp",这样控制跳转地址配置或者直接返回的地址串就是:"/jsp/"+returl+".jsp"。有人可能会担心url重写规则是否会影响性能，其实不会的，因为url只会被计算一次，后面就从缓冲区中取已经重写好的地址了。

以上就是bboss-mvc.xml中比较重要的一些应用可能会用到并修改的配置，其他的配置内容基本不用开发人员修改，保持默认配置即可。

  