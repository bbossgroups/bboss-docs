### bboss session listener实现和配置方法

bboss session listener类似于servlet规范中的session listener接口，本文介绍bboss session listener实现和配置方法
bboss session listener接口：

Java代码

```java
package org.frameworkset.security.session;  
  
public interface SessionListener {  
    public void createSession(SessionEvent event);  
    public void destroySession(SessionEvent event);  
    public void addAttribute(SessionEvent event);  
    public void removeAttribute(SessionEvent event);  
  
}  
```

##### 第一步，实现bboss session listener接口

Java代码

```java
package org.frameworkset.security.session.impl;  
  
import org.apache.log4j.Logger;  
import org.frameworkset.security.session.SessionEvent;  
import org.frameworkset.security.session.SessionListener;  
  
public class MySessionListener implements SessionListener {  
    private static Logger log = Logger.getLogger(MySessionListener.class);  
    @Override  
    public void createSession(SessionEvent event) {  
        log.debug("createSession session id:"+event.getSource().getId());  
    }  
  
    @Override  
    public void destroySession(SessionEvent event) {  
        log.debug("destroySession session id:"+event.getSource().getId());  
  
    }  
  
    @Override  
    public void addAttribute(SessionEvent event) {  
        log.debug("addAttribute session id:"+event.getSource().getId() + ",attirbute name is "+event.getAttributeName());  
  
  
    }  
  
    @Override  
    public void removeAttribute(SessionEvent event) {  
        log.debug("removeAttribute session id:"+event.getSource().getId() + ",attirbute name is "+event.getAttributeName());  
    }  
  
}  
```

#####   第二步，配置和加载自己bboss session listener

在/resources/sessionconf.xml文件的sessionManager组件的sessionlisteners属性上配置session listener，多个用逗号分隔：

property name="sessionManager" class="org.frameworkset.security.session.impl.SessionManager"
init-method="init" destroy-method="destroy"
。。。。。。
   **property name="sessionlisteners" value="org.frameworkset.security.session.impl.MySessionListener"/**
/property

配置好了后，bboss session框架会加载session listener器并拦截应用程序对session的创建、销毁和属性修改变更操作事件。

参考文档：
[bboss session共享使用方法介绍](http://yin-bp.iteye.com/blog/2064662)  