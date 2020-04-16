### bboss aop 实践 (5-1) 拦截器（Interceptor）

文件内容如下：

< ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​    <manager id="interceptor.a"

​       singlable="true">

​       < provider type="A" class="com.chinacreator.spi.interceptor.A" />

​       < interceptor class="com.chinacreator.spi.interceptor.Insterceptor"/>

​       <!—-其它拦截器

< interceptor class="com.chinacreator.spi.interceptor.Insterceptor1"/>-->    

​    </ manager >

</ manager-config > 

将simplemanager-interceptor.xml文件配置在主文件manager-provider.xml文件中:

< managerimport file="com/chinacreator/spi/interceptor/manager-interceptor.xml" />

这样我们就配置完毕了。 

##### 使用业务组件，拦截器作用于业务方法 

**package** com.chinacreator.spi.interceptor; 

**import** com.chinacreator.spi.BaseSPIManager;

**import** com.chinacreator.spi.SPIException; 

**public** **class** TestInterceptor {

​    **public** **static** **void** testInterceptor()

​    {

​       **try** {

​           AI a = (AI)BaseSPIManager.getProvider("interceptor.a");

​           **try** {

​              a.testInterceptorsBeforeAfter();

​           } **catch** (Exception e) {

​              // **TODO** Auto-generated catch block

​              e.printStackTrace();

​           }

​           **try** {

​              a.testInterceptorsBeforeThrowing();

​           } **catch** (Exception e) {

​              // **TODO** Auto-generated catch block

​              e.printStackTrace();

​           }

​       } **catch** (SPIException e) {

​           // **TODO** Auto-generated catch block

​           e.printStackTrace();

​       }

​    }

​    **public** **static** **void** main(String[] args)

​    {

​       testInterceptor();

​    } 

} 

综上所述，bboss aop框架提供了使用非常简单但功能强大的拦截器组件，不妨一试。