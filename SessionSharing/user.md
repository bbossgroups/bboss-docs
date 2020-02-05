## bboss session手动移除用户会话方法介绍

在一些特定的情况下只允许用户同时在一个地方登陆，那么每次登录的时候会记录用户和对应的sessionid的关系，同时在登录之前会检查用户是否已经已经在别的地方登录过了（查找之前当前用户账号和sessionid的记录），如果已经登录则会剔除之前的用户。
**org.frameworkset.security.session.SessionUti**
bboss session的l组件提供了以下两个api来支持剔除用户会话功能：

Java代码 

```java
//appcode对应sessionconf.xml文件中的appcode，  
//如果没有配置则对应应用的上下文request.getContextpath(),如果上下文为/则必须指定appcode为ROOT  
//在明确知道appcode的情况下可以调用以下方法  
public static void removeSession(String sessionId,String appcode)  
  
//在不知道appcode的情况下可以调用以下方法，通过传入request和对应的sessionid来剔除用户会话，根据request对象来推算出appcode  
public static void removeSession(String sessionId,HttpServletRequest request)  
```

通过以上方法中的任意一个，都可以剔除sessionId对应的用户会话。使用参考代码：
Java代码

```java
<%@page import="org.frameworkset.security.session.SessionUtil"%>  
<%@ page contentType="text/html; charset=UTF-8"%>  
<%@page import="test.*"%>  
<%  
String sessionId = request.getParameter("sessionId");  
if(sessionId != null)  
{  
    SessionUtil.removeSession(sessionId, request);  
}  
   
 %> 
```

