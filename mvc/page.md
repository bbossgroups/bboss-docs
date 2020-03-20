### 浅谈 bboss mvc 页面访问控制实现机制

声明：3.6以后的bboss中已经增加了安全过滤器，不再需要这个拦截器来进行安全认证检测

浅谈 bboss mvc 页面访问控制实现机制，本文介绍如何通过bboss mvc框架中的拦截器来实现页面访问控制功能，内容不多，很简单，但是很实用，呵呵。切入正题。

1.bboss mvc拦截器介绍

1.1 bboss mvc的拦截器接口为：

Java代码

```java
org.frameworkset.web.servlet.HandlerInterceptor  
```

1.2 bboss mvc提供了页面访问控制的基础抽象类，这个类实现了HandlerInterceptor接口：

Java代码 

```java
org.frameworkset.web.interceptor.AuthenticateInterceptor  
```

AuthenticateInterceptor提供了抽象方法：

Java代码

```java
protected abstract boolean check(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta);  
```

参数说明：前两个参数是url请求提供了jsp请求对象request和请求响应对象response，第三个参数是mvc请求控制器的元数据信息handlerMeta，通过这个对象用户可以获取到控制的相关定义信息。

返回值说明：返回boolean值，如果是true表示允许访问，为false表示不允许访问。如果验证通过时，还可以将用户会话信息以特定的key值对方式存储到request的attribute中，以便控制器方法中方便地获取用户会话信息；如果验证不通过那么则跳转到用户指定的页面，同时会将用户当前请求的页面信息（页面路径，页面参数）转交给失败跳转页（一般是登陆页面），当登陆通过后，任然允许用户获取这些信息转向到需要访问的页面。

1.3 通过继承AuthenticateInterceptor类并实现其中的抽象check方法用户可以非常方便地实现自己的访问控制拦截器

1.4 用户实现了自己的访问控制拦截器后，还需要在bboss-mvc.xml中配置访问控制拦截和器拦截页面规则（可以非常方便地配置需要拦截的url请求和不需要拦截的url请求，如果不指定这些规则，就拦截所有的url请求方法，这里的url请求指的是mvc控制url请求）。

下面举例说明

2.实现自己的访问控制拦截器

2.1 访问控制拦截器定义

Java代码    

```java
/** 
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
package org.frameworkset.web.interceptor;  
  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
  
import org.frameworkset.web.servlet.handler.HandlerMeta;  
  
  
  
/** 
 * <p> 
 * MyFirstInterceptor.java 
 * </p> 
 * <p> 
 * Description: 
 * </p> 
 * <p> 
 * bboss workgroup 
 * </p> 
 * <p> 
 * Copyright (c) 2009 
 * </p> 
 *  
 * @Date 2011-5-31 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public  class MyFirstInterceptor extends AuthenticateInterceptor{  
  
    @Override  
    protected boolean check(HttpServletRequest request,  
            HttpServletResponse response, HandlerMeta handlerMeta)  
    {  
        String name = request.getParameter("name");  
        if(name != null && name.equals("test"))  
            return false;  
        return true;  
    }  
      
  
}  
```

这个访问控制拦截器逻辑非常简单，直接通过name参数值来判断页面是否可以访问，如果name有值并且值为test那么就阻止用户继续访问，否则允许用户访问。

2.2 访问控制拦截器及拦截规则配置

bboss-mvc.xml文件中找到如下内容（如果没有就加进去，这里可以配置多个全局拦截器）：

Xml代码 

```xml
<!-- 配置全局控制器方法拦截器 -->  
     <property name="org.frameworkset.web.servlet.gloabel.HandlerInterceptors" >  
        <list componentType="bean">  
            ...............  
        </list>  
     </property>  
```

找到后将访问控制拦截器配置进去：

Xml代码

```xml
<!-- 配置全局控制器方法拦截器 -->  
     <property name="org.frameworkset.web.servlet.gloabel.HandlerInterceptors" >  
        <list componentType="bean">  
            <!-- 配置认证检查拦截器 -->  
            <property class="org.frameworkset.web.interceptor.MyFirstInterceptor">  
                <!-- 配置认证检查拦截器拦截url模式规则 -->  
                <property name="patternsInclude">  
                    <list componentType="string">  
                        <property value="/**/*.do"/>  
                    </list>  
                </property>  
                <!-- 配置认证检查拦截器不拦截url模式规则 -->  
                <property name="patternsExclude">  
                    <list componentType="string">  
                        <property value="/*.html"/>  
                    </list>  
                </property>  
                <property name="redirecturl" value="/login.jsp"/>  
            </property>  
        </list>  
     </property>  
```

  这里需要说明一下：

patternsInclude-用来指定需要拦截的url请求规则列表，可以是通配符，也可以是具体的地址

patternsExclude-用来指定不需要拦截的url请求规则列表，可以是通配符，也可以是具体的地址

redirecturl-用来指定验证失败时需要跳转的页面,例如/login.jsp，前面带/表示这个地址是相对于应用上下文的地址，当前请求的页面信息（页面路径，页面参数）转交给失败跳转页（一般是登陆页面），当登陆通过后，任然允许用户通过获取这些信息转向到需要访问的页面（跳不跳由用户自己决定）。

如果patternsInclude和patternsExclude都没有指定，那么默认拦截所有的控制方法请求。patternsExclude指定的规则优先级要高些，只要匹配上这个规则那么页面通通放行。

ok，就这么简单，呵呵，本文参考bbossgroups 3.2版本功能编写，适用3.2及后续版本。  