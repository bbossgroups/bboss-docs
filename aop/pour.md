### bboss factory依赖注入模式使用方法

**1.bboss aop框架的工厂模式实现组件管理概述**

bboss aop框架的工厂模式是bbossgroups3.0新引入的一种组件创建机制，是对原有的属性注入模式和构造函数注入模式的很好补充。具体实现方式如下，在property元素上增加factory-bean、factory-class和factory-method三个属性，也就是对应property元素管理的组件实例通过factory-bean或者factory-class对应的组件中factory-method对应的方法来创建。factory-bean和factory-class的主要区别：

factory-bean 指定的值是一个组件id，对应aop组件配置文件中的其他组件的一个引用，同时factory-method指定的方法名称是这个组件的

一个实例方法，aop框架通过调用factory-bean对应的组件的factory-method对应的方法来创建组件实例。

factory-class 指定的是创建组件的工厂实现类的全路径，同时factory-method指定的方法名称是这个组件的一个静态或者实例方法，aop框架通过调用factory-class对应的组件的factory-method对应的方法来创建组件实例；当对应的方法是实例方法则首先创建factory-class类对应的工厂实例，如果配置了依赖注入属性，则会先注入这些属性到工厂实例中，然后在该工厂实例上调用factory-method对应的方法来创建组件实例。

如果factory-method指定的方法带有参数，可以通过construction节点包含property节点的方式来指定，多个property节点的排列顺序要和方法参数的顺序一致，类型要可以转换。

具体的实例可以参考测试用例：

/bbossaop/test/org/frameworkset/spi/beans/factory

我们通过一个简单的案例来介绍工厂模式的使用方法，需要创建的组件为：

org.frameworkset.spi.beans.factory.TestBean

我们通过分别：

通过工厂组件org.frameworkset.spi.beans.factory.TestFactoryBeanCreate中的相关方法来创建TestBean的实例

通过工厂类org.frameworkset.spi.beans.factory.StaticBeanFactory中的相关静态方法来创建TestBean的实例。  

下面介绍具体的程序和配置

**2.配置文件中如何配置工厂模式**

Xml代码

```xml
<!--  
Warning: Factory pattern can not against loop ioc,while loop ioc occured , unknown exception will be throwed. 
 -->  
<properties>  
    <!--  
    This is a webservice client example.  
    ApplicationContext context = ApplicationContext.getApplicationContext("org/frameworkset/spi/beans/factory/factorybean.xml");  
    ETLWebService etlWebService = (ETLWebService)context.getBeanObject("ETLWebService");  
    -->  
    <property name="ETLWebService" factory-bean="ETLWebServiceFactory" factory-method="create"/>  
      
    <property name="ETLWebServiceFactory" class="org.apache.cxf.jaxws.JaxWsProxyFactoryBean">  
        <property name="serviceClass" value="org.frameworkset.monitor.etl.webservice.ETLWebService"/>  
        <property name="address" value="http://localhost/kettle/webservice/ETLWebServicePort"/>           
    </property>  
  
    <!--   
        bean [testbean] will be created used facctory method [createNoArgs] of factory bean [testfactorybean].  
        testfactorybean is defined by another composinent.  
     -->  
    <property name="testbeanCreateNoArgs" factory-bean="testfactorybean" factory-method="createNoArgs"/>  
    <!--   
        bean [testbean] will be created used facctory method [createWithArgs] of factory bean [testfactorybean].  
        testfactorybean is defined by another component.  
        constructions parameters will be used as the parameters of createWithArgs method.  
     -->  
    <property name="testbeanCreateByArgs" factory-bean="testfactorybean" factory-method="createWithArgs">  
        <construction>  
            <property name="name" value="duoduo"/>  
            <property name="id" value="12"/>    
        </construction>  
    </property>  
    <!--   
        bean [testbean] will be created used facctory method [createNoArgsThrowException] of factory bean [testfactorybean].  
        testfactorybean is defined by another component.  
     -->  
    <property name="testbeanCreateNoArgsThrowException" factory-bean="testfactorybean" factory-method="createNoArgsThrowException"/>  
    <!--   
        bean [testbean] will be created used factory method [createWithArgsThrowException] of factory bean [testfactorybean].  
        testfactorybean is defined by another component.  
        constructions parameters will be used as the parameters of createWithArgsThrowException method.  
        A Exception occur when call createWithArgsThrowException method and component created failed.  
     -->  
    <property name="testbeanCreateByArgsThrowException" factory-bean="testfactorybean" factory-method="createWithArgsThrowException">  
        <construction>  
            <property name="name" value="duoduo"/>  
            <property name="id" value="12"/>    
        </construction>  
    </property>  
      
    <property name="testfactorybean" class="org.frameworkset.spi.beans.factory.TestFactoryBeanCreate">  
        <property name="factorydata1" value="duoduo"/>  
        <property name="factorydata2" value="12"/>            
    </property>  
      
    <!-- createWithArgs method must be a static method of factory class org.frameworkset.spi.beans.factory.TestFactoryBeanCreate,   
         constructions parameters will be used as the parameters of createWithArgs method.-->  
    <property name="staticTestbeanCreateByArgs" factory-class="org.frameworkset.spi.beans.factory.StaticBeanFactory" factory-method="createWithArgs">  
        <construction>  
            <property name="name" value="duoduo"/>  
            <property name="id" value="12"/>    
        </construction>  
    </property>  
      
    <!-- createNoArgs method must be a static method of factory class org.frameworkset.spi.beans.factory.TestFactoryBeanCreate 
         
         -->  
    <property name="staticTestbeanCreateNoArgs" factory-class="org.frameworkset.spi.beans.factory.StaticBeanFactory" factory-method="createNoArgs"/>  
      
      
    <!-- createWithArgsThrowException method must be a static method of factory class org.frameworkset.spi.beans.factory.TestFactoryBeanCreate,   
         constructions parameters will be used as the parameters of createWithArgsThrowException method.  
         A Exception occur when call createWithArgsThrowException method and component created failed.  
         -->  
    <property name="staticTestbeanCreateByArgsThrowException" factory-class="org.frameworkset.spi.beans.factory.StaticBeanFactory" factory-method="createWithArgsThrowException">  
        <construction>  
            <property name="name" value="duoduo"/>  
            <property name="id" value="12"/>    
        </construction>  
    </property>  
      
    <!-- createNoArgsThrowException method must be a static method of factory class org.frameworkset.spi.beans.factory.TestFactoryBeanCreate  
    A Exception occur when call createNoArgsThrowException method and component created failed.  
         -->  
    <property name="staticTestbeanCreateNoArgsThrowException" factory-class="org.frameworkset.spi.beans.factory.StaticBeanFactory" factory-method="createNoArgsThrowException"/>    
  
  
</properties>  
```

对应的配置文件存放的地址：org/frameworkset/spi/beans/factory/factorybean.xml

**3.需要创建的组件**

Java代码

```java
package org.frameworkset.spi.beans.factory;  
  
/** 
 * <p>Title: TestBean.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-1-14 上午10:13:42 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestBean {  
    private String name;  
    private int id;  
  
    public TestBean(String name, int id) {  
          
        this.name = name;  
        this.id = id;  
    }  
      
    public TestBean() {       
    }  
  
    protected String getName() {  
        return name;  
    }  
  
    protected void setName(String name) {  
        this.name = name;  
    }  
  
    protected int getId() {  
        return id;  
    }  
  
    protected void setId(int id) {  
        this.id = id;  
    }  
}  
```

**4.管理组件的工厂**

静态工厂

Java代码

```java
package org.frameworkset.spi.beans.factory;  
  
/** 
 * <p>Title: StaticBeanFactory.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-1-14 上午10:55:07 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class StaticBeanFactory {  
      
    public static TestBean createWithArgs(String name,int id)  
    {  
        return new TestBean(name,id);  
    }  
      
      
    public static TestBean createNoArgs()  
    {  
        return new TestBean();  
    }  
      
      
    public static TestBean createWithArgsThrowException(String name,int id) throws Exception  
    {  
        throw new Exception("createWithArgsThrowException name:"+name+",id：" + id );  
    }  
      
      
    public static TestBean createNoArgsThrowException() throws Exception  
    {  
        throw new Exception("createWithArgsThrowException()");  
    }  
}  
```

组件工厂

Java代码

```java
package org.frameworkset.spi.beans.factory;  
  
/** 
 * <p>Title: TestFactoryBeanCreate.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-1-14 上午10:13:31 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestFactoryBeanCreate {  
    private String factorydata1;  
    private String factorydata2;  
    public TestBean createNoArgs()  
    {  
        return new TestBean();  
    }  
      
    public TestBean createWithArgs(String name,int id)  
    {  
        return new TestBean( name,id);  
    }  
      
      
    public TestBean createNoArgsThrowException() throws Exception  
    {  
        throw new Exception("createNoArgsThrowException " );  
    }  
      
    public TestBean createWithArgsThrowException(String name,int id) throws Exception  
    {  
        throw new Exception("createWithArgsThrowException name:"+name+",id：" + id );  
    }  
      
  
    public String getFactorydata1() {  
        return factorydata1;  
    }  
  
    public void setFactorydata1(String factorydata1) {  
        this.factorydata1 = factorydata1;  
    }  
  
    public String getFactorydata2() {  
        return factorydata2;  
    }  
  
    public void setFactorydata2(String factorydata2) {  
        this.factorydata2 = factorydata2;  
    }  
}  
```

**5.工厂类实例方法模式**

以hessian客户端作为示例来说明这种模式的使用方法，先看例子：

通过bboss-ioc 工厂类实例方法模式配置和获取客户端

Xml代码

```xml
<property name="clientservice" factory-class="com.caucho.hessian.client.HessianProxyFactory"   
                    f:connectionTimeout="360000"  
                    f:readTimeout="36000"  
                    factory-method="create">  
                    <construction>  
                        <property value="org.frameworkset.spi.remote.hession.server.ServiceInf"/>       
                        <property value="http://localhost:8080/context/hessian?service=basicservice"/>      
                    </construction>  
                </property>  
```

获取客户端:

Java代码

```java
DefaultApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/remote/hession/client/hessian-client.xml");  
                //获取客户端组件实例  
                ServiceInf basic = context.getTBeanObject("clientservice", ServiceInf.class);  
```

说明：bboss-ioc 首先创建HessianProxyFactory的实例，然后设置这实例的connectionTimeout和readTimeout两个属性的值，最后调用工厂实例的create方法（传入serviceInterface和serviceAdress两个参数）获取组件实例（hessian客户端代理实例）。

**6.测试用例**

Java代码

```java
package org.frameworkset.spi.beans.factory;  
  
import org.frameworkset.spi.ApplicationContext;  
import org.junit.Before;  
import org.junit.Test;  
  
/** 
 * <p>Title: TestCase.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-1-14 上午11:16:36 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestCase {  
    ApplicationContext context ;  
    @Before  
    public void initContext()  
    {  
        context = ApplicationContext.getApplicationContext("org/frameworkset/spi/beans/factory/factorybean.xml");  
          
    }  
    @Test  
    public void runCase()  
    {  
        TestBean bean =  (TestBean) context.getBeanObject("testbeanCreateNoArgs");  
        TestBean bean1 =  (TestBean) context.getBeanObject("testbeanCreateByArgs");  
        TestBean bean2 =  (TestBean) context.getBeanObject("staticTestbeanCreateByArgs");  
        TestBean bean3 =  (TestBean) context.getBeanObject("staticTestbeanCreateNoArgs");  
    }  
      
    @Test  
    public void runExceptionCase()  
    {  
        try {  
            TestBean bean = (TestBean) context  
                    .getBeanObject("testbeanCreateNoArgsThrowException");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        try {  
            TestBean bean1 = (TestBean) context  
                    .getBeanObject("testbeanCreateByArgsThrowException");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        try {  
            TestBean bean2 = (TestBean) context  
                    .getBeanObject("staticTestbeanCreateByArgsThrowException");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        try {  
            TestBean bean3 = (TestBean) context  
                    .getBeanObject("staticTestbeanCreateNoArgsThrowException");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
}  
```

