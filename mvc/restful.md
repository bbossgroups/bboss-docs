### 浅谈 BbossMVC restful使用技巧

浅谈 BbossMVC restful使用技巧。切入正题。

1.BbossMVC restful功能实现机制

BbossMVC restful功能使用起来非常简单，非常适用，主要是通过BbossMVC restful控制器来实现，这类控制器的特征如下：

1.1 在方法级指定@HandlerMapping注解，指定value属性值对应方法映射的特定url请求，在url中添加对应于方法参数的路径变量部分信息，例如：@HandlerMapping(value="/examples/namequery/{loginname}.page")；

1.2 在对应于变量部分的方法参数上添加@PathVariable注解，通过value属性指定相应的路径变量，同时还可以指定变量的字符编码集和日期转换格式等信息，

例如：@PathVariable(value="loginname",decodeCharset="UTF-8")。

2.BbossMVC restful实例-用户查询：

2.1 功能说明

本实例介绍怎么通过restful功能来实现一个用户查询的功能，非常简单，涉及两个控制器方法：
进入查询页面的控制器方法（非restful方法）

查询处理的控制方法（restful方法）

2.2 控制器方法实现代码-LoginNameQuery

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
package org.frameworkset.web.restful;  
  
import org.frameworkset.util.annotations.HandlerMapping;  
import org.frameworkset.util.annotations.PathVariable;  
import org.frameworkset.util.annotations.ResponseBody;  
  
/** 
 * <p>Title: LoginName.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2008</p> 
 * @Date 2011-5-11 
 * @author biaoping.yin 
 * @version 1.0 
 */  
  
public class LoginNameQuery {  
    /** 
     *  
     * @param loginname 
     * @return 
     */  
    @HandlerMapping(value="/examples/namequery/{loginname}.page")  
    public @ResponseBody   
        String loginnamequery(@PathVariable(value="loginname",decodeCharset="UTF-8") String loginname)  
    {             
        if(loginname == null || loginname.trim().equals(""))              
            return "查询中的用户名为空，请重新输入用户名";  
        if(loginname.equals("多多"))  
        {  
            return "用户名["+loginname+"]存在。";  
        }  
        else  
            return "用户名["+loginname+"]不存在。";  
    }  
      
    @HandlerMapping(value="/examples/namequery/loginName.page")  
    public String loginName()  
    {  
        return "path:loginName";  
    }  
  
}  
```

没什么需要特别说明的，restful方法的返回值@ResponseBody String定义了该方法的返回值为String类型，将作为对应的restful请求的响应数据发回给客服端。

参数(@PathVariable(value="loginname",decodeCharset="UTF-8") String loginname对应@HandlerMapping(value="/examples/namequery/{loginname}")中指定的restful地址中的{loginname}路径变量，指定decodeCharset解码字符集主要是loginname为中文时乱码问题。

2.3 restful视图jsp页面-loginName.jsp

Js代码 

```js
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<html>  
    <head>  
        <title>姓名查询</title>  
        <pg:config enablecontextmenu="false"/>  
        <script type="text/javascript">  
            function doquery(){  
               
                if($("#loginName3").val() == null || $("#loginName3").val() == "")  
                {  
                    alert("请输入要查询的姓名!")  
                    return false;  
                }  
                //这里之所以要编码，是因为中文不能出现在url组成部分中（参数可以由中文）  
                var resturl = "<%=request.getContextPath() %>/examples/namequery/" + encodeURIComponent(encodeURIComponent($("#loginName3").val())+".page");  
                $("#queryresult").load(resturl);  
                return false;  
            }  
        </script>  
    </head>  
  
    <body>  
            <table>   
                <tr>   
                    <td>查询登录名：<input type="text" name="loginName3" id="loginName3"/>   
                    </td>   
                      
                    <td><input type="button" value="查询" onclick="doquery()"/>   
                    </td>   
                </tr>   
                <tr>   
                    <td>查询结果：  
                    </td>   
                    <td id="queryresult"></td>   
                </tr>   
            </table>  
    </body>  
</html>  
```

该界面提供一个查询登录名输入框，用来输入要查询的用户名，一个查询按钮用来提交查询，在doquery()方法中提交了一个restful 风格的url请求，在url路径中直接包含了要查询的用户名，为了避免中文乱码，对用户名进行了编码处理：

var resturl = "<%=request.getContextPath() %>/examples/namequery/" + encodeURIComponent(encodeURIComponent($("#loginName3").val()))+".page";

将查询返回的信息在<**td id="queryresult">**<**/td>** 中展示。

2.3 restful控制器配置文件-bboss-loginnamequery.xml

Xml代码

```xml
<?xml version="1.0" encoding='gb2312'?>  
  
<properties>  
    <property name="loginNamequery"   
        class="org.frameworkset.web.restful.LoginNameQuery"  
        path:loginName="/examples/loginName.jsp"/>  
</properties>  
```

2.4 web.xml文件中和festful请求相关的配置：

Xml代码

```xml
<servlet>  
    <servlet-name>mvcdispather</servlet-name>  
    <servlet-class>org.frameworkset.web.servlet.DispatchServlet</servlet-class>  
    <init-param>  
        <param-name>contextConfigLocation</param-name>  
        <!--如果有多个目录需要加载，请用,号分隔-->  
        <param-value>/WEB-INF/conf/bboss-*.xml</param-value>  
    </init-param>  
    <load-on-startup>0</load-on-startup>  
</servlet>  
<servlet-mapping>  
    <servlet-name>mvcdispather</servlet-name>  
    <url-pattern>*.page</url-pattern>  
</servlet-mapping>  
```

这个映射配置<**url-pattern>***.page<**/url-pattern>**

就是对应于restful实例的请求映射。