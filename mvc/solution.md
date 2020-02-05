### 解决tomcat stop报Illegal access: this web application instance has been stopped异常方法

运行shutdown.bat/shutdown.sh关闭tomcat的时候，控制台可能抛出以下异常:
Java代码

```java
Illegal access: this web application instance has been stopped already.  Could not load org.frameworkset.spi.BaseApplicationContext$2.  The eventual following stack trace is caused by an error thrown for debugging purposes as well as to attempt to terminate the thread which caused the illegal access, and has no functional impact.  
java.lang.IllegalStateException  
    at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1600)  
    at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1559)  
    at org.frameworkset.spi.BaseApplicationContext.shutdown(BaseApplicationContext.java:684)  
    at org.frameworkset.spi.BaseApplicationContext$1.run(BaseApplicationContext.java:94)  
    at java.lang.Thread.run(Thread.java:662)  
```

出现这种问题时，在web.xml文件中添加bboss mvc的应用启动监听器，问题一般就可以得到解决，添加方法如下：
Xml代码

```xml
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">  
    <display-name>BBOSS-MVC</display-name>  
    <listener>  
        <description><![CDATA[应用销毁监听器： 
        在应用销毁之前调用系统shutdown 回调函数，前提是所有的shutdown回调函数 
        是通过以下方法注册： 
        BaseApplicationContext.addShutdownHook(new Runnable(){ 
 
                @Override 
                public void run() { 
                     
                    try { 
                        stop(); 
                     
 
                    } catch (Throwable e) { 
                        // TODO Auto-generated catch block 
                        e.printStackTrace(); 
                    } 
                }});]]>  
          
    </description>  
        <listener-class>org.frameworkset.web.listener.ApplicationLifeListener</listener-class>  
    </listener>  
.........  
  
</web-app>  
```

