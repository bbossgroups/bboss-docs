### bboss mvc框架中使用注解指定控制器方法日期类型参数日期格式的例子

直入正题：

1.控制器方法定义-DateConvertController

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
package org.frameworkset.web.date;  
  
import org.frameworkset.util.annotations.RequestParam;  
import org.frameworkset.web.servlet.ModelMap;  
  
/** 
 * <p>Title: DateConvertController.java</p>  
 * <p>Description: 日期转换实例</p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2008</p> 
 * @Date 2011-4-30 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class DateConvertController {  
      
    public String converStringToDate(@RequestParam(name="d12",dateformat="yyyy-MM-dd") java.util.Date d12,  
                                     @RequestParam(name="stringdate",dateformat="yyyy-MM-dd") java.sql.Date stringdate,  
                                     @RequestParam(name="stringdatetimestamp",dateformat="yyyy-MM-dd HH/mm/ss") java.sql.Timestamp stringdatetimestamp,  
                                     @RequestParam(name="stringdatetimestamp") String stringdatetimestamp_,  
            ModelMap model)  
    {  
        model.put("java.util.Date", d12);  
        model.put("java.sql.Date", stringdate);  
        model.put("java.sql.Timestamp", stringdatetimestamp);  
        return "path:convertok";  
          
    }  
    public String dateconvert()  
    {  
        return "path:convertin";  
    }  
  
}  
```

2.选择日期的jsp页面

Js代码

```js
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
    <head>  
  
        <title>DatePicker</title>  
        <meta http-equiv="pragma" content="no-cache">  
        <meta http-equiv="cache-control" content="no-cache">  
        <meta http-equiv="expires" content="0">  
        <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">  
        <meta http-equiv="description" content="This is my page">  
      
        <script type="text/javascript"  
            src="${pageContext.request.contextPath}/jsp/datepicker/My97DatePicker/WdatePicker.js"></script>  
         <link rel="shortcut icon"  
        href="${pageContext.request.contextPath}/css/favicon.gif">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/tables.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/main.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/mainnav.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/messages.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/tooltip.css"  
        type="text/css">  
    </head>  
   
    <body>  
    <h1>日期格式转换demo</h1>  
    <form action="converStringToDate.html" method="post">  
    <table class="genericTbl">  
       <tr>  
       <th class="order1 sorted" >demo描述  
       </th>  
       <th class="order1 sorted">演示区  
       </th>  
       </tr>  
     <tr class="even" >  
         
        <td align="right">  
       普通触发：  
        </td>  
        <td>  
       <input id="d12" name="d12" type="text"  
        onclick="WdatePicker({el:'d12'})" src="${pageContext.request.contextPath}/jsp/datepicker/My97DatePicker/skin/datePicker.gif" width="16" height="22" align="absmiddle"/>  
       </td>  
       </tr>  
        
      
          
        <tr class="even">  
        <td align="right">  
        精确到日期：  
        </td>  
        <td>  
        <input class="Wdate" type="text" name="stringdate" onClick="WdatePicker()">  
        </td>  
       </tr>  
       <tr class="even">  
        <td align="right">  
        精确具体时间：  
        </td>  
        <td>  
        <input class="Wdate" type="text" name="stringdatetimestamp" onClick="WdatePicker({dateFmt:'yyyy-MM-dd HH/mm/ss'})">  
        </td>  
        </tr>  
          
         <tr class="even">  
        <td align="right">  
        提交：  
        </td>  
        <td>  
        <input type="submit" value="提交转换"/>  
        </td>  
        </tr>  
          
        </table>  
        </form>  
    </body>  
</html>  
```

3.转换结果查看页面-ok.jsp

Js代码

```js
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
  
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
<html>  
    <head>  
  
        <title>DatePicker</title>  
        <meta http-equiv="pragma" content="no-cache">  
        <meta http-equiv="cache-control" content="no-cache">  
        <meta http-equiv="expires" content="0">  
        <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">  
        <meta http-equiv="description" content="This is my page">  
        <!--  
    <link rel="stylesheet" type="text/css" href="styles.css">  
    -->  
        <script type="text/javascript"  
            src="${pageContext.request.contextPath}/jsp/datepicker/My97DatePicker/WdatePicker.js"></script>  
         <link rel="shortcut icon"  
        href="${pageContext.request.contextPath}/css/favicon.gif">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/tables.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/main.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/mainnav.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/messages.css"  
        type="text/css">  
    <link rel="stylesheet"  
        href="${pageContext.request.contextPath}/css/classic/tooltip.css"  
        type="text/css">  
    </head>  
   
    <body>  
    <h1>日期格式转换demo转换结果</h1>  
    <form action="dateconvert.html" method="post">  
    <table class="genericTbl">  
       <tr>  
       <th class="order1 sorted" >demo描述  
       </th>  
       <th class="order1 sorted">演示区  
       </th>  
       </tr>  
     <tr class="even" >  
         
        <td align="right">  
       普通触发：  
        </td>  
        <td>  
        <%=request.getAttribute("java.util.Date") %>  
       </td>  
       </tr>  
        
      
          
        <tr class="even">  
        <td align="right">  
        精确到日期：  
        </td>  
        <td>  
        <%=request.getAttribute("java.sql.Date") %>  
        </td>  
       </tr>  
       <tr class="even">  
        <td align="right">  
        精确具体时间：  
        </td>  
        <td>  
        <%=request.getAttribute("java.sql.Timestamp") %>  
        </td>  
        </tr>  
          
         <tr class="even">  
        <td align="right">  
        返回：  
        </td>  
        <td>  
        <input type="submit" value="返回"/>  
        </td>  
        </tr>  
          
        </table>  
        </form>  
    </body>  
</html>  
```

4.mvc框架配置文件-bboss-dateconvert.xml：

Xml代码

```xml
<?xml version="1.0" encoding='gb2312'?>  
<!--   
bboss-dateconvert.xml  
描述：日期类型转换  
-->  
<properties>  
    <property   
     name="/dateconvert/*.html"   
        path:convertok="/dateconvert/ok"  
        path:convertin="/dateconvert/in"  
     class="org.frameworkset.web.date.DateConvertController"/>  
</properties>  
```

补充说明：如果不指定dateformat属性，那么将用yyyy-MM-dd HH:mm:ss作为默认的日期转换格式。

更详细的情况请参考bbossgroups 项目的mvcdemo应用相关文档《bbossgroups 3.1 mvc demo部署方法》：

http://yin-bp.iteye.com/blog/1026245

demo部署好后可以通过以下地址访问日期格式转换的例子：

http://localhost:8080/bboss-mvc/dateconvert/dateconvert.html  