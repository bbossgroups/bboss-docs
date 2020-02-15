### bboss ioc容器之间组件引用方法简介

bboss ioc容器之间组件引用方法简介。我们可以在ioc容器直接获取其他容器中的组件，也可以将其他ioc容器中的组件注入到自己的组件中。本文简单说明如何在ioc容器中获取其他ioc容器中定义的组件，其他容器类型可以为：

   **org.frameworkset.spi.DefaultApplicationContext**

   **MVC容器**

   **org.frameworkset.spi.ApplicationContext**

bboss ioc容器参考文章：

   http://yin-bp.iteye.com/blog/1153798



本文涉及到的工厂模式参考文章：

   http://yin-bp.iteye.com/blog/1014361

**1.ioc配置文件示例**

Xml代码

```xml
<!--   
   本实例说明如何在ioc容器中获取其他ioc容器中定义的组件，其他容器类型包括：  
   org.frameworkset.spi.DefaultApplicationContext   
   MVC容器  
   org.frameworkset.spi.ApplicationContext   
     
   bboss ioc容器请参考文章：  
   http://yin-bp.iteye.com/blog/1153798  
     
        涉及到的工厂模式请参考文章：  
   http://yin-bp.iteye.com/blog/1014361  
 -->  
<properties>  
    <!--   
        使用工厂模式，定义一个外部org.frameworkset.spi.ApplicationContext类型的容器对象引用，以便引用容器中的组件  
                该容器对象通过加载配置文件 org/frameworkset/spi/beans/testapplicationcontext.xml  
               创建  
    -->  
    <property name="test.outnerapplicationcontext"   
              factory-class="org.frameworkset.spi.ApplicationContext"   
              factory-method="getApplicationContext">  
        <construction>  
            <property  value="org/frameworkset/spi/beans/testapplicationcontext.xml"/>      
        </construction>  
    </property>  
      
    <!--   
        使用工厂模式定义一个对外部组件的引用组件test_refbean_from_outnerapplicationcontext，  
        从外部容器test.outnerapplicationcontext中获取名称为rpc.restful.convertor的组件  
    -->  
    <property name="test_refbean_from_outnerapplicationcontext"   
              factory-bean="test.outnerapplicationcontext"   
              factory-method="getBeanObject">  
        <construction>  
            <property  value="rpc.restful.convertor"/>      
        </construction>  
    </property>  
      
      
    <!--   
        使用工厂模式，定义bboss MVC类型的容器对象引用，以便引用容器中的组件  
                该容器对象通过MVC框架自动创建  
    -->  
    <property name="test.mvcapplicationcontext"   
              factory-class="org.frameworkset.web.servlet.support.WebApplicationContextUtils"   
              factory-method="getWebApplicationContext"/>  
      
    <!--  
        使用工厂模式定义一个对外部组件的引用组件test_refbean_from_mvcapplicationcontext，从MVC容器中获取名称为deskTopMenuShorcutManager的组件 
    -->  
    <property name="test_refbean_from_mvcapplicationcontext"   
              factory-bean="test.mvcapplicationcontext"   
              factory-method="getBeanObject">  
        <construction>  
            <property  value="deskTopMenuShorcutManager"/>      
        </construction>  
    </property>  
      
      
    <!--   
        使用工厂模式，定义一个外部org.frameworkset.spi.DefaultApplicationContext 类型的容器对象，以便引用改容器中的组件  
                该容器对象通过加载配置文件 org/frameworkset/spi/beans/testapplicationcontext.xml  
               创建  
    -->  
    <property name="test.outnerdefaultapplicationcontext"   
              factory-class="org.frameworkset.spi.DefaultApplicationContext"   
              factory-method="getApplicationContext">  
        <construction>  
            <property  value="org/frameworkset/spi/beans/testapplicationcontext.xml"/>      
        </construction>  
    </property>  
      
    <!--   
        使用工厂模式定义一个对外部组件的引用组件test_refbean_from_outnerdefaultapplicationcontext，  
        从外部容器test.outnerdefaultapplicationcontext中获取名称为rpc.restful.convertor的组件  
    -->  
    <property name="test_refbean_from_outnerdefaultapplicationcontext"   
              factory-bean="test.outnerdefaultapplicationcontext"   
              factory-method="getBeanObject">  
        <construction>  
            <property  value="rpc.restful.convertor"/>      
        </construction>  
    </property>  
      
</properties>  
```

**2.ioc配置文件加载和跨ioc容器组件实例获取方法**

我们可以在ioc容器直接获取其他容器的组件，也可以将其他ioc容器的组件注入到自己的组件中，这里值说明第一种情况：

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
  
package org.frameworkset.spi.container;  
  
import org.frameworkset.spi.BaseApplicationContext;  
import org.frameworkset.spi.DefaultApplicationContext;  
import org.frameworkset.spi.remote.restful.RestfulServiceConvertor;  
  
/** 
 * <p>Title: IOCReference.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2012-8-4 下午2:17:18 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class IOCReference {  
  
    public void test()  
    {  
        //初始化ioc容器对象，在该容器对象中定义了三个外部ioc容器组件的引用组件  
        BaseApplicationContext context = DefaultApplicationContext  
                        .getApplicationContext("org/frameworkset/spi/container/iocreference.xml");  
        //跨ioc组件实例获取方法示例  
        RestfulServiceConvertor convertor = context.getTBeanObject("test_refbean_from_outnerapplicationcontext",RestfulServiceConvertor.class);  
        RestfulServiceConvertor convertor1 = context.getTBeanObject("test_refbean_from_mvcapplicationcontext",RestfulServiceConvertor.class);  
        RestfulServiceConvertor convertor2 = context.getTBeanObject("test_refbean_from_outnerdefaultapplicationcontext",RestfulServiceConvertor.class);  
    }  
}  
```

