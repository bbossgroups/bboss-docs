### bboss mvc获取request,session,response,pageContext对象方法

本文介绍基于bboss mvc后台java程序如何获取request,session,response,pageContext对象。

**1.组件及方法**

组件：org.frameworkset.web.servlet.context.RequestContextHolder

基于bboss mvc的后台程序可以通过RequestContextHolder中一组静态方法获取request，response，pageContext，session对象，方法定义如下：

public static HttpServletResponse getResponse() //获取response对象

public static PageContext getPageContext() //获取pageContext对象

public static HttpServletRequest getRequest() //获取request对象

public static HttpSession getSession(boolean create) //获取session对象,create标识如果session不存在是否创建新的session，true-创建 false-不创建

public static HttpSession getSession() //获取session对象如果session不存在创建新的session

**2.使用实例**

Java代码 

```java
HttpServletResponse  response = RequestContextHolder.getResponse();  
PageContext pageContext = RequestContextHolder.getPageContext ();  
HttpServletRequest request = RequestContextHolder.getRequest();  
HttpSession session = RequestContextHolder.getSession(true);  
HttpSession session = RequestContextHolder.getSession();  
```

本文涉及到bboss mvc，如果在项目中的版本没有上述的api，可以自己从github[下载](http://yin-bp.iteye.com/blog/1080824)bboss，自行构建bboss mvc（执行bboss mvc工程下的build-jar.bat或者build.bat，前提是要配置好ant），然后拷贝bboss mvc工程下distrib目录下的bboss-mvc.jar更新到项目中即可

bboss版本下载地址，参考文档：
http://yin-bp.iteye.com/blog/1080824