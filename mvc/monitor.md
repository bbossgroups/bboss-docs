### bboss mvc启动事件监听器使用方法

在实际应用，往往需要在mvc容器启动后执行相应的操作，bboss提供了mvc容器启动监听器来达成这个目的，本文详细介绍bboss mvc启动事件监听器使用方法。

1.首先要实现一个ioc容器监听器，这个监听器只要实现接口即可：

Java代码

```java
org.frameworkset.spi.event.IocLifeCycleEventListener  
```

接口中提供了两个事件方法和一个初始化参数方法：

Java代码 

```java
public void init(Map<String,String> params);//监听器初始化参数方法  
public void beforestart()//mvc容器启动前事件触发的方法  
public void afterstart(BaseApplicationContext arg0) //mvc容器启动后事件触发的方法，将mvc对应的ioc容器对象作为after事件方法的参数 
```

以下是一个简单的接口实现实例：

Java代码

```java
package com.frameworkset.platform.sysmgrcore.manager;  
  
import org.frameworkset.spi.BaseApplicationContext;  
import org.frameworkset.spi.event.IocLifeCycleEventListener;  
import org.frameworkset.task.TaskService;  
  
public class QuartzIocLifeCycleEventListener implements IocLifeCycleEventListener {  
  
    public QuartzIocLifeCycleEventListener() {  
        // TODO Auto-generated constructor stub  
    }  
  
    @Override  
    public void afterstart(BaseApplicationContext arg0) {  
        // mvc容器启动后，初始化任务管理quartz服务  
        TaskService service = TaskService.getTaskService();  
        service.startService();  
  
    }  
  
    @Override  
    public void beforestart() {  
        //do something here.  
  
    }  
  
    @Override  
    public void init(Map<String, String> arg0) {  
        sqlitepath = arg0.get("sqlitepath");  
  
    }  
}  
```

2.实现事件监听器后，需要将监听器配置到mvc拦截器中，配置方法如下：

找到应用的web.xml文件，在DispatchServlet中增加iocLifeCycleEventListeners参数，多个事件监听器以逗号分隔。

Xml代码

```xml
<servlet-name>mvc</servlet-name>  
        <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>/WEB-INF/conf/appbom/bboss-*.xml,  
            /WEB-INF/conf/commons/bboss-*.xml,             
            /WEB-INF/conf/workflow/bboss-*.xml,  
            /WEB-INF/conf/application/bboss-*.xml,  
            /WEB-INF/conf/document/bboss-*.xml,  
            /WEB-INF/conf/params/bboss-*.xml,  
            /WEB-INF/conf/counter/bboss-*.xml,  
            /WEB-INF/conf/channel/bboss-*.xml  
            </param-value>  
        </init-param>  
        <init-param>  
            <param-name>messagesources</param-name>  
            <param-value>/WEB-INF/messages_pdp,/WEB-INF/messages_pdp_common,  
            /WEB-INF/conf/appbom/messages_appbom,  
            /WEB-INF/conf/sanyems/messages</param-value>  
        </init-param>  
        <init-param>  
            <param-name>useCodeAsDefaultMessage</param-name>  
            <param-value>true</param-value>  
        </init-param>  
        <init-param>  
            <param-name>iocLifeCycleEventListeners</param-name>  
            <param-value>com.frameworkset.platform.sysmgrcore.manager.QuartzIocLifeCycleEventListener</param-value>  
        </init-param>  
<init-param>  
            <param-name>iocLifeCycleEventListenerParams</param-name>  
            <param-value>sqlitepath=d:/gencodedb|sourcepath=d:/sourcecode</param-value>  
        </init-param>  
        <load-on-startup>0</load-on-startup>  
    </servlet>  
```

参数iocLifeCycleEventListeners中可以配置多个事件监听器，配置时用逗号分隔即可，例如：

Xml代码

```xml
<init-param>  
            <param-name>iocLifeCycleEventListeners</param-name>  
            <param-value>com.frameworkset.platform.sysmgrcore.manager.QuartzIocLifeCycleEventListener,com.frameworkset.platform.OtherIocLifeCycleEventListener</param-value>  
        </init-param>  
```

  iocLifeCycleEventListenerParams中可以配置监听器依赖的初始化参数，多个参数用|分隔，例如：
sqlitepath=d:/gencodedb|sourcepath=d:/sourcecode

mvc容器事件监听器主要用来保证其他服务和mvc ioc容器启动的先后顺序,以便解决其他服务和mvc容器启动顺序冲突问题。只有存在顺序冲突的情况下才需要用到mvc ioc容器事件监听器；如果没有启动顺序冲突，不需要使用事件监听器。  