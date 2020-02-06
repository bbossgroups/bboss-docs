### bboss热部署应用资源销毁监听器ApplicationLifeListener使用说明

实现类org.frameworkset.web.listener.ApplicationLifeListener实现javax.servlet.ServletContextListener接口，当应用卸载时用来清除框架和应用系统的内存缓存资源，有效规避应用热部署时内存泄露和线程泄露风险。

在web.xml开头处配置ApplicationLifeListener即可，配置方法如下：

Xml代码

```xml
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
                            e.printStackTrace(); 
                    } 
                }});]]>  
          
    </description>  
    <listener-class>org.frameworkset.web.listener.ApplicationLifeListener</listener-class>  
    </listener>  
```

ApplicationLifeListener组件在应用销毁时主动销毁bboss框架占用的系统资源，应用程序也可以通过org.frameworkset.spi.BaseApplicationContext组件提供的addShutdownHook方法添加自己的资源销毁回调程序：

public static void addShutdownHook(Runnable destroyVMHook,int proir)

public static void addShutdownHook(Runnable destroyVMHook)

两个方法参数说明：

destroyVMHook-为java.lang.Runnable接口实现类，用来执行具体的资源销毁逻辑

int proir-指定Runnable 的执行优先级，数值越大越先执行。

方法使用示例：

按默认添加顺序执行方式

Java代码

```java
BaseApplicationContext.addShutdownHook(new Runnable(){  
  
                @Override  
                public void run() {  
                      
                    try {  
                        CacheUtil.destroy();  
                      
  
                    } catch (Throwable e) {  
                                                e.printStackTrace();  
                    }  
                }});  
```

指定执行优先级方式：

Java代码

```java
BaseApplicationContext.addShutdownHook(new Runnable(){  
  
                @Override  
                public void run() {  
                      
                    try {  
                        CacheUtil.destroy();  
                      
  
                    } catch (Throwable e) {  
                                                e.printStackTrace();  
                    }  
                }},100);  
```

ApplicationLifeListener可有效解决bboss托管的quartz、activiti之类的框架导致应用热部署失败的问题。