### bboss国际化功能简介

   借助bboss国际化功能，我们可以非常方便快捷地实现系统的国际化功能，本文简单介绍bboss国际化组件和相关标签在mvc框架、ioc框架、独立组件三种场景中的典型使用方法，bboss还提供了国际化配置文件热加载的机制。

bboss最新源码可以从github获取：

https://github.com/bbossgroups/bbossgroups-3.5



bboss国际化简单的web示例工程：

https://github.com/bbossgroups/bbossgroups-3.5/tree/master/bestpractice/i18n  

**1.国际化属性配置文件管理组件**

org.frameworkset.spi.support.HotDeployResourceBundleMessageSource

**2.独立组件使用模式**

**2.1 HotDeployResourceBundleMessageSource的独立使用方法**

Java代码 

```java
HotDeployResourceBundleMessageSource messagesource = new HotDeployResourceBundleMessageSource( );  
//如果有多个配置文件，可以在数组中追加，注意属性文件名不需要带语言信息和文件后缀  
//HotDeployResourceBundleMessageSource会自动扫描并加载对应语言的属性文件，后文同理。  
messagesource.setBasenames(new String[] {"org/frameworkset/spi/support/messages"});  
//如果没有找到代码对应的配置项，直接输出code  
messagesource.setUseCodeAsDefaultMessage(true);  
System.out.println(messagesource.getMessage("probe.jsp.generic.abbreviations",  Locale.US));  
System.out.println(messagesource.getMessage("probe.jsp.generic.abbreviations",  Locale.US));  
```

**2.2 是否需要对HotDeployResourceBundleMessageSource实例管理的资源文件启用热加载机制**

通过属性来控制：

private boolean changemonitor = true;

true 启用

false 关闭

默认启用

设置changemonitor属性值的方法为：

messagesource.setChangemonitor(false);

**2.3 通过ioc容器来管理独立的messagesource对象的方法**

Xml代码

```xml
<property name="messageSource" class="org.frameworkset.spi.support.HotDeployResourceBundleMessageSource" >  
        <property name="basename" value="/WEB-INF/messages"/>  
        <property name="useCodeAsDefaultMessage" value="true"/>  
 </property>  
```

Java代码

```java
DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/support/messages.xml");  
essageSource messagesource = context.getTBeanObject("messageSource",MessageSource.class); 
```

**3.ioc容器的国际化机制**

每个ioc容器默认自带一个MessageSource对象，对应的国际化资源配置文件和ioc组件配置必须在同一个包路径下：

Java代码

```java
//ioc组件配置文件  
/bbossaop/test/org/frameworkset/spi/support/messages.xml  
//ioc容器对应的国际化属性文件  
/bbossaop/test/org/frameworkset/spi/support/messages_zh_CN.properties  
/bbossaop/test/org/frameworkset/spi/support/messages_en_US.properties  
```

而且名称必须是messages，相应的语言信息部分为_en_US.properties

使用的实例为：

Java代码

```java
DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/support/messages.xml");  
        System.out.println(context.getMessage("Trans.Log.TransformationIsToAllocateStep", new Object[]{"a","b"}, (Locale)null));  
        System.out.println(context.getMessage("Trans.Log.TransformationIsToAllocateStep", new Object[]{"a","b"}, Locale.SIMPLIFIED_CHINESE));  
        System.out.println(context.getMessage("StepLoader.RuntimeError.UnableToInstantiateClass.TRANS0006",Locale.US));  
        Resource resource = context.getResource("org/frameworkset/spi/support/messages");  
        System.out.println(resource);  
```

这里我们看到：

Java代码

```java
context.getMessage("Trans.Log.TransformationIsToAllocateStep", new Object[]{"a","b"}, Locale.SIMPLIFIED_CHINESE);  
```

第二个参数是一个对象数组，含义是代码Trans.Log.TransformationIsToAllocateStep代表的真实输出信息中包含两个需要被替换的变量占位符{0}和{1},定义如下：

Java代码

```java
Trans.Log.TransformationIsToAllocateStep=\ 转换大约分配了 步骤 [{0}] 类型的 [{1}]  
```

国际化组件解析Trans.Log.TransformationIsToAllocateStep时，会将数组new Object[]{"a","b"}第一个元素替换{0},第二个元素替换{1},最终输出为：

Java代码

```java
转换大约分配了 步骤 [a] 类型的 [b]  
```

**4. mvc框架国际化功能**

mvc框架国际化功能涉及四个local解析器：

Java代码

```java
org.frameworkset.web.servlet.i18n.AcceptHeaderLocaleResolver  
org.frameworkset.web.servlet.i18n.SessionLocalResolver  
org.frameworkset.web.servlet.i18n.CookieLocaleResolver  
org.frameworkset.web.servlet.i18n.LanguageLocaleResolver  
```

org.frameworkset.web.servlet.i18n.AcceptHeaderLocaleResolver是默认的locale解析器，根据客户端浏览器request对象中的Locale来解析相应的code。

org.frameworkset.web.servlet.i18n.LanguageLocaleResolver是一个通过配置方式直接全局指定locale对象的解析器，使用方法如下：

Xml代码

```xml
<property name="localeResolver" class="org.frameworkset.web.servlet.i18n.LanguageLocaleResolver" f:language="en_US"/>  
```

**通过language属性指定全局语言代码，就无需在程序中通过代码指定本地语言了，LanguageLocaleResolver对不需要在界面中切换语言的系统特别有用**。   

 org.frameworkset.web.servlet.i18n.SessionLocalResolver类，从session中获取用户登录时存储的Locale对象，默认的key值为字符串：

Java代码 

```java
org.frameworkset.web.servlet.i18n.SESSION_LOCAL_KEY  
```

session中设置Locale对象的方法：

Java代码

```java
session.setAttribute(SessionLocalResolver.SESSION_LOCAL_KEY, java.util.Locale.US); 
```

或者

Java代码 

```java
session.setAttribute("org.frameworkset.web.servlet.i18n.SESSION_LOCAL_KEY", java.util.Locale.US);  
```

如果用户需要使用SessionLocalResolver必须在bboss-mvc.xml文件中增加以下配置：

Xml代码

```xml
<property name="localeResolver" class="org.frameworkset.web.servlet.i18n.SessionLocalResolver"/>  
```

以便覆盖默认的LocalResolver组件org.frameworkset.web.servlet.i18n.AcceptHeaderLocaleResolver(默认根据客户端所处的Locale来解析相应的code)

调整session中存放Locale对象的key值的方法：

Xml代码

```xml
<property name="localeResolver" class="org.frameworkset.web.servlet.i18n.SessionLocalResolver"  
f:sessionlocalkey="yoursessionlocalkey"  
/>  
```

session中设置Locale对象的方法变为：

Java代码

```java
session.setAttribute("yoursessionlocalkey", java.util.Locale.US);  
```

cookie中配置Local对象方法：

首先将bboss-mvc.xml中localeResolver组件做如下配置：

Xml代码

```xml
<property name="localeResolver" class="org.frameworkset.web.servlet.i18n.CookieLocaleResolver"  
     f:cookielocalkey="cookie.localkey"    
     />  
```

然后在jsp登录界面或者控制器方法中将语言代码设置到名称为cookie.localkey的cookie中即可：

Java代码

```java
com.frameworkset.util.StringUtil.addCookieValue(request, response, "cookie.localkey", language, 3600 * 24);  
```

  其中的language就是语言代码，例如：zh_CN，en_US等等，3600 * 24为cookie的有效期。

相应的从cookie中获取language的方法为：

language = com.frameworkset.util.StringUtil.StringUtil.getCookieValue(request, "cookie.localkey", "zh_CN");

"zh_CN"标识默认值，没有"cookie.localkey"的cookie时返回该值。  

上面说明的是每个localresolver组件单独设置locale语言环境的方法，比较繁琐，下面是bboss提供的通用设置和获取语言代码的方法：

获取设置到特定组件中的language代码：

Java代码

```java
String language = org.frameworkset.web.servlet.support.RequestContextUtils.getLocaleResolver(request).resolveLocaleCode(request);  

```

通用的设置语言代码：

Java代码

```java
try {  
    String language = "zh_CN";      org.frameworkset.web.servlet.support.RequestContextUtils.getLocaleResolver(request).setLocale(request, response, language);  
        } catch (Exception e) {  
            log.error("",e);  
        }  
```

另外包含一个message标签，通过message标签，可以在系统界面上方便实现国际化功能。

国际化标签的使用方式

Xml代码 

```xml
<pg:message  code="probe.jsp.wrongparams"/>  
```

使用国际化标签时，必须在jsp的头部导入标签定义文件：

Xml代码

```xml
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
```

mvc国际化组件messageSource的获取方法：

Java代码 

```java
org.frameworkset.spi.support.MessageSource messageSource = org.frameworkset.web.servlet.support.WebApplicationContextUtils.getWebApplicationContext();  
```

详情请参考测试jsp页面：[/bboss-mvc/WebRoot/jsp/i18n.jsp](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/jsp/i18n.jsp)或者[bestpractice\i18n\WebRoot\examples\i18n.jsp](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bestpractice/i18n/WebRoot/examples/i18n.jsp)

mvc国际化配置文件路径默认为

Java代码

```java
/bboss-mvc/WebRoot/WEB-INF/messages.properties  
/bboss-mvc/WebRoot/WEB-INF/messages_zh_CN.properties  
/bboss-mvc/WebRoot/WEB-INF/messages_en_US.properties  
```

等等。

在web.xml文件的DispatchServlet中进行配置：

Xml代码

```xml
<init-param>  
            <param-name>messagesources</param-name>  
            <param-value>/WEB-INF/messages,/WEB-INF/messages1</param-value>  
        </init-param>  
        <init-param>  
            <param-name>useCodeAsDefaultMessage</param-name>  
            <param-value>true</param-value>  
        </init-param>  
```

messagesources用来指定国际化配置文件清单

useCodeAsDefaultMessage用来指定如果没有在相应的配置文件中找到相应的code对应的配置项是否直接输出code，默认值为true

为true时输出，false不输出

完整的配置如下：

Xml代码

```xml
<servlet>  
        <servlet-name>mvcdispather</servlet-name>  
        <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>/WEB-INF/bboss-*.xml,/WEB-INF/conf/bboss-*.xml</param-value>  
        </init-param>  
        <init-param>  
            <param-name>messagesources</param-name>  
            <param-value>/WEB-INF/messages,/WEB-INF/messages1</param-value>  
        </init-param>  
        <init-param>  
            <param-name>useCodeAsDefaultMessage</param-name>  
            <param-value>true</param-value>  
        </init-param>  
        <load-on-startup>0</load-on-startup>  
    </servlet> 
```

这里不需要带国家标识，系统会自动在/WEB-INF/目录下查找对应国家语言的配置文件，如果有多个配置文件可以用逗号分割，例如：

Java代码

```java
/WEB-INF/messages,/WEB-INF/messages1,/WEB-INF/messages2  
```

另外为了便于业务系统在java程序中获取mvc国际化信息，提供了一个工具类其中定义了一组静态方法：

Java代码

```java
public static   void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale)  
      
  
      /** 
       * Set the current locale to the given one. 
       * @param request the request to be used for locale modification 
       * @param response the response to be used for locale modification 
       * @param locale the new locale, or <code>null</code> to clear the locale 
         * @throws UnsupportedOperationException if the LocaleResolver implementation 
         * does not support dynamic changing of the theme 
       */  
public static void setLocale(HttpServletRequest request, HttpServletResponse response, String locale)  
public static Locale getRequestContextLocal(HttpServletRequest request)  
  
public static String getRequestContextLocalCode(HttpServletRequest request)  
  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值 
 * @param code 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code,HttpServletRequest request)  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值,如果代码值为空，则返回defaultMessage 
 * @param code 
 * @param defaultMessage 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code,String defaultMessage,HttpServletRequest request)  
  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值,如果代码值为空，则返回defaultMessage 
 * @param code 
 * @param defaultMessage 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code,String defaultMessage)  
  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值,如果代码值为空，则返回defaultMessage 
 * @param code 
 * @param defaultMessage 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code)  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值,并且将数组args中的每个元素替换到代码值中位置占位符，例如{0}会用数组的第一个元素替换 
 * @param code 
 * @param args 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code,Object[] args,HttpServletRequest request)  
public static String getI18nMessage(String code,Object[] args)  
public static String getI18nMessage(String code,Object[] args,String defaultMessage)  
/** 
 * 根据code从mvc的国际化配置文件中获取对应语言的代码值,如果代码值为空，则返回defaultMessage,并且将数组args中的每个元素替换到代码值中位置占位符，例如{0}会用数组的第一个元素替换 
 * @param code 
 * @param args 
 * @param defaultMessage 
 * @param request 
 * @return 
 */  
public static String getI18nMessage(String code,Object[] args,String defaultMessage,HttpServletRequest request)  
```

**5.国际化配置文件热加载机制配置**

bboss提供了简单实用的国际化属性文件热加载机制，可以方便地开启和关闭。

检测文件是否改动的时间间隔默认5秒，如果有改动mvc框架会重新加载有修改的文件，没有修改的文件不会重新加载

如果想屏蔽改机制请修改bboss-aop.jar/aop.properties文件的选项

#国际化属性文件变更检测时间间隔，单位为毫秒，默认为5秒间隔

Java代码

```java
resourcefile.refresh_interval=5000  
```

若果>0则启用热加载机制，<=0则屏蔽热加载机制，开发环境请开启，生产环境请关闭。