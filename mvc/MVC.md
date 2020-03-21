### 浅谈 BbossMVC 数据绑定

浅谈 BbossMVC 数据绑定，本文介绍BbossMVC框架的数据绑定功能。包括数字绑定，字符串绑定，日期类型绑定，日期数组类型绑定，bean对象绑定，List绑定，Map绑定，枚举类型绑定，枚举类型数组绑定。

**相关的框架包请及时更新最新版本**

本文涉及的技术包括：mvc，aop/ioc,taglib,jsp

目 录 [ - ]

​    1.数据绑定实战策略-实例eclipse工程下载和部署以及浏览

​    2.数据绑定实例-jsp页面

​    3.数据绑定实例-控制器实现

​    4.数据绑定控制器配置文件-bboss-helloworld.xml  

1.数据绑定实战策略-实例eclipse工程下载和部署以及浏览

1.1.下载本文相关附件-mvc-databind.zip

http://dl.iteye.com/topics/download/6b4fc8b6-79dd-325f-b32a-4b69354b35a5

1.2.解压mvc-databind.zip ，将其中的工程导入eclipse即可查看文中相关的源代码

1.3.部署，准备好tomcat 6和jdk 15或以上的版本

1.4.在tomcat 6的conf\Catalina\localhost下增加mvc.xml文件，内容为：

Xml代码

```xml
<?xml version='1.0' encoding='utf-8'?>  
<Context docBase="D:\workspace\bbossgroups-3.2\bestpractice\mvc\WebRoot" path="/mvc" debug="0" reloadable="false">  
</Context>  
```

用户可以根据自己的实际情况设置docBase属性的值

1.5.启动tomcat，输入以下地址即可访问mvc的数据绑定实例了：

http://localhost:8080/mvc/examples/index.page

2.数据绑定实例-jsp页面   

Html代码    

```html
<%--    
        
     *  Copyright 2008-2011 biaoping.yin    
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
        
     --%>    
    <%@ page contentType="text/html;charset=UTF-8" language="java"    
        session="false"%>    
    <%@ taglib uri="/WEB-INF/commontag.tld" prefix="common"%>     
    <%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>    
        
    <head>    
        <title>bboss-mvc - hello world,data bind!</title>    
        <script type="text/javascript"    
                src="../include/My97DatePicker/WdatePicker.js"></script>    
        <pg:config enablecontextmenu="false"/>    
            <script type="text/javascript">    
                
                function doquery(){    
                    $("#queryresult").load("sayHelloEnum.page",{sex:$("#sex").val()});    
                }    
                    
                function domutiquery(){    
                    $("#queryresult").load("sayHelloEnums.page",{sex:$("#sex").val()});    
                }    
            </script>     
    </head>    
    <body>    
                <h3>    
                    Hello World number bind Examples.    
                </h3>    
                <form action="sayHelloNumber.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr><td>    
                                请输入您的幸运数字：    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <common:request name="serverHelloNumber"/>    
                                <pg:error colName="name"/>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                </form>    
                <h3>    
                    Hello World String bind Examples.    
                </h3>    
                <form action="sayHelloString.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                            <td>    
                                请输入您的名字：    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <pg:empty requestKey="serverHello">    
                                    没有名字，不问候。    
                                </pg:empty>    
                                <pg:notempty requestKey="serverHello">    
                                    <common:request name="serverHello"/>    
                                </pg:notempty>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                </form>    
                <h3>    
                    Hello World Time bind Examples.    
                </h3>    
                <form action="sayHelloTime.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                            <td colspan="2">    
                                请输入您的幸运日期：    
                            </td>    
                        </tr>    
                        <tr >    
                            <td align="right">    
                           普通日期：    
                            </td>    
                            <td>    
                           <input id="d12" name="d12" type="text"    
                            onclick="WdatePicker({el:'d12'})" src="../include/My97DatePicker/skin/datePicker.gif" width="16" height="22" align="absmiddle"/>    
                           </td>    
                           </tr>    
                            <tr >    
                            <td align="right">    
                            sql日期：    
                            </td>    
                            <td>    
                            <input class="Wdate" type="text" name="stringdate" onClick="WdatePicker()">    
                            </td>    
                           </tr>    
                           <tr >    
                            <td align="right">    
                            timestamp精确具体时间：    
                            </td>    
                            <td>    
                            <input class="Wdate" type="text" name="stringdatetimestamp" onClick="WdatePicker({dateFmt:'yyyy-MM-dd HH/mm/ss'})">    
                            </td>    
                            </tr>    
                            
                        <tr>    
                            <td colspan="2">    
                                来自服务器的日期问候：    
                                <common:request name="utilDate" dateformat="yyyy-MM-dd"/>    
                                <common:request name="sqlDate" dateformat="yyyy-MM-dd"/>    
                                <common:request name="sqlTimestamp" dateformat="yyyy-MM-dd HH/mm/ss"/>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                </form>    
                <h3>    
                    Hello World Time Array bind Examples.    
                </h3>    
                <form action="sayHelloTimes.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                                                    <td colspan="2">    
                                请输入您的幸运日期：    
                                
                            </td>    
                        </tr>    
                        <tr >    
                            <td align="right">    
                           普通日期：    
                            </td>    
                            <td>    
                           <input id="d12s" name="d12s" type="text"    
                            onclick="WdatePicker({el:'d12s'})" src="../include/My97DatePicker/skin/datePicker.gif" width="16" height="22" align="absmiddle"/>    
                           </td>    
                           </tr>    
                            <tr >    
                            <td align="right">    
                            sql日期：    
                            </td>    
                            <td>    
                            <input class="Wdate" type="text" name="stringdates" onClick="WdatePicker()">    
                            </td>    
                           </tr>    
                           <tr >    
                            <td align="right">    
                            timestamp精确具体时间：    
                            </td>    
                            <td>    
                            <input class="Wdate" type="text" name="stringdatetimestamps" onClick="WdatePicker({dateFmt:'yyyy-MM-dd HH/mm/ss'})">    
                            </td>    
                            </tr>    
                            
                        <tr>    
                            <td colspan="2">    
                                来自服务器的日期问候：    
                                <common:request name="utilDates" dateformat="yyyy-MM-dd"/>    
                                <common:request name="sqlDates" dateformat="yyyy-MM-dd"/>    
                                <common:request name="sqlTimestamps" dateformat="yyyy-MM-dd HH/mm/ss"/>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                        
                </form>    
                <h3>    
                    Hello World Bean bind Examples.    
                </h3>    
                <form action="sayHelloBean.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                            <td>    
                                请输入您的名字：    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <pg:null actual="${serverHelloBean}">    
                                    没有名字，不问候。    
                                </pg:null>    
                                <pg:notnull actual="${serverHelloBean}">    
                                    <common:request name="serverHelloBean" property="name"/>    
                                </pg:notnull>    
                                    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                        
                </form>    
                    
                <h3>    
                    Hello World List<PO> bind  Examples.    
                </h3>    
                <form action="sayHelloBeanList.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                            <td>    
                                请输入您的名字：    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <pg:list requestKey="serverHelloListBean" >    
                                    <pg:cell colName="name"/>    
                                </pg:list>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定">/td>                           
                        </tr>    
                    </tbody>    
                </table>    
                        
                </form>    
                    
                <h3>    
                    Hello World Map<String,PO> bind  Examples.    
                </h3>    
                <form action="sayHelloBeanMap.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr><td>    
                                请输入您的名字：    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <pg:beaninfo requestKey="serverHelloMapBean" >    
                                    <pg:cell colName="name"/>    
                                </pg:beaninfo>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定"></td>                           
                        </tr>    
                    </tbody>    
                </table>    
                        
                </form>    
                    
                <h3>    
                    Hello World Array bind  Examples.    
                </h3>    
                <form action="sayHelloArray.page" method="post">    
                <table cellspacing="0" >    
                    <tbody>    
                        <tr>    
                            <td>    
                                请输入您的名字两次（一定要两次哦）：    
                            <input name="name" type="text">    
                            <input name="name" type="text">    
                            </td>    
                        </tr>    
                        <tr>    
                            <td>    
                                来自服务器的问候：    
                                <pg:list requestKey="serverHelloArray" >    
                                    <pg:rowid increament="1"/> <pg:cell />    
                                </pg:list>    
                            </td>    
                        </tr>    
                        <tr>    
                            <td><input type="submit" name="确定" value="确定"></td>                           
                        </tr>    
                    </tbody>    
                </table>    
                        
                </form>    
                <h3>    
                    Hello World Enum bind  Examples.    
                </h3>    
                    
                <table >    
                        
                    <!--分页显示开始,分页标签初始化-->    
                        <tr >    
                            <th align="center">    
                                NAME:<select id="sex">    
                                <option value="F">F</option>    
                                <option value="M">M</option>    
                                <option value="UN">UN</option>    
                                </select>    
                            </th>    
                            <th>    
                                <input type="button" value="性别查询" onclick="doquery()"/>    
                                <input type="button" value="多性别查询" onclick="domutiquery()"/>    
                            </th>    
                        </tr>    
                        <tr >    
                            <td align="center">    
                                结果    
                            </td>    
                                 
                            <td id="queryresult"></td>    
                        </tr>    
                </table>      
    </body>  
```

3.数据绑定实例-控制器实现

3.1.控制器实现类

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
    package org.frameworkset.mvc;    
        
    import java.io.IOException;    
    import java.util.List;    
    import java.util.Map;    
        
    import javax.servlet.http.HttpServletResponse;    
        
    import org.frameworkset.util.annotations.MapKey;    
    import org.frameworkset.util.annotations.RequestParam;    
    import org.frameworkset.web.servlet.ModelMap;    
        
    /**  
     * <p>  
     * HelloWord.java  
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
     * @Date 2011-6-4  
     * @author biaoping.yin  
     * @version 1.0  
     */    
    public class HelloWord    
    {    
        
        public String sayHelloNumber(@RequestParam(name = "name") int ynum,    
                ModelMap model)    
        {    
        
            if (ynum != 0)    
            {    
                model.addAttribute("serverHelloNumber", "幸运数字为[" + ynum + "]！");    
            }    
            else    
                model.addAttribute("serverHelloNumber", "幸运数字为[" + ynum    
                        + "]！，好像有点不对哦。");    
        
            return "path:sayHello";    
        }    
        
        public String sayHelloString(@RequestParam(name = "name") String yourname,    
                ModelMap model)    
        {    
        
            if (yourname != null && !"".equals(yourname))    
                model.addAttribute("serverHello", "服务器向您[" + yourname + "]问好！");    
            else    
                model.addAttribute("serverHello", "请输入您的名字！");    
            return "path:sayHello";    
        }    
        
        public String sayHelloTime(    
                @RequestParam(name = "d12", dateformat = "yyyy-MM-dd") java.util.Date d12,    
                @RequestParam(name = "stringdate", dateformat = "yyyy-MM-dd") java.sql.Date stringdate,    
                @RequestParam(name = "stringdatetimestamp", dateformat = "yyyy-MM-dd HH/mm/ss") java.sql.Timestamp stringdatetimestamp,    
                @RequestParam(name = "stringdatetimestamp") String stringdatetimestamp_,    
                ModelMap model)    
        {    
        
            model.put("utilDate", d12);    
            model.put("sqlDate", stringdate);    
            model.put("sqlTimestamp", stringdatetimestamp);    
            return "path:sayHello";    
        
        }    
            
        public String sayHelloTimes(    
                @RequestParam(name = "d12s", dateformat = "yyyy-MM-dd") java.util.Date[] d12,    
                @RequestParam(name = "stringdates", dateformat = "yyyy-MM-dd") java.sql.Date[] stringdate,    
                @RequestParam(name = "stringdatetimestamps", dateformat = "yyyy-MM-dd HH/mm/ss") java.sql.Timestamp[] stringdatetimestamp,    
                @RequestParam(name = "stringdatetimestamps") String[] stringdatetimestamp_,    
                ModelMap model)    
        {    
        
            model.put("utilDates", d12[0]);    
            model.put("sqlDates", stringdate[0]);    
            model.put("sqlTimestamps", stringdatetimestamp[0]);    
            return "path:sayHello";    
        
        }    
        
        public String sayHelloBean(ExampleBean yourname, ModelMap model)    
        {    
        
            if (yourname.getName() != null && !"".equals(yourname.getName()))    
                model.addAttribute("serverHelloBean", yourname);    
            else    
                ;    
        
            return "path:sayHello";    
        }    
        
        public String sayHelloBeanList(List<ExampleBean> yourname, ModelMap model)    
        {    
        
            model.addAttribute("serverHelloListBean", yourname);    
        
            return "path:sayHello";    
        }    
        
        public String sayHelloBeanMap(@RequestParam(name = "name") String yourname,    
                @MapKey("name") Map<String, ExampleBean> mapBeans, ModelMap model)    
        {    
        
            model.addAttribute("serverHelloMapBean", mapBeans.get(yourname));    
            return "path:sayHello";    
        }    
            
        public String sayHelloArray(@RequestParam(name = "name") String[] yournames,ModelMap model)    
        {    
            model.addAttribute("serverHelloArray",yournames);    
            return "path:sayHello";    
        }    
            
        /**  
         * 测试单个字符串向枚举类型值转换  
         * @param type  
         * @param response  
         * @throws IOException  
         */    
        public  void sayHelloEnum(@RequestParam(name="sex") SexType type,HttpServletResponse response) throws IOException    
        {    
            if(type != null)    
            {    
                if(type == SexType.F)    
                {    
                    response.setContentType("text/html; charset=GBK");    
                    response.getWriter().print("女");    
                }    
                else if(type == SexType.M)    
                {    
                    response.setContentType("text/html; charset=GBK");    
                    response.getWriter().print("男");    
                }    
                else if(type == SexType.UN)    
                {    
                    response.setContentType("text/html; charset=GBK");    
                    response.getWriter().print("未知");    
                }    
                        
            }    
                
                
        }    
        /**  
         * 测试单个字符串向枚举类型值转换  
         * @param type  
         * @param response  
         * @throws IOException  
         */    
        public  void sayHelloEnums(@RequestParam(name="sex") SexType[] types,HttpServletResponse response) throws IOException    
        {    
            response.setContentType("text/html; charset=GBK");    
            if(types != null)    
            {    
                if(types[0] == SexType.F)    
                {    
                        
                    response.getWriter().print("女");    
                }    
                else if(types[0] == SexType.M)    
                {    
                        
                    response.getWriter().print("男");    
                }    
                else if(types[0] == SexType.UN)    
                {    
                    response.getWriter().print("未知");    
                }                   
            }           
        }    
            
        public String index()    
        {           
            return "path:sayHello";    
        }    
    }    
```

3.2.依赖的值对象-ExampleBean

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
   package org.frameworkset.mvc;    
       
       
   /**  
    * <p>ExampleBean.java</p>  
    * <p> Description: </p>  
    * <p> bboss workgroup </p>  
    * <p> Copyright (c) 2009 </p>  
    *   
    * @Date 2011-6-4  
    * @author biaoping.yin  
    * @version 1.0  
    */    
   public class ExampleBean    
   {    
       private String name = null;    
       
           
       public String getName()    
       {    
           
           return name;    
       }    
       
           
       public void setName(String name)    
       {    
           
           this.name = name;    
       }    
       
   }    
```

3.3.依赖的枚举类型对象-SexType

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
    package org.frameworkset.mvc;    
        
    /**  
     * <p>Title: SexType.java</p>   
     * <p>Description: </p>  
     * <p>bboss workgroup</p>  
     * <p>Copyright (c) 2008</p>  
     * @Date 2011-4-5  
     * @author biaoping.yin  
     * @version 1.0  
     */    
    public enum SexType {    
        F,M,UN    
        
    }    
```

4.数据绑定控制器配置文件-bboss-helloworld.xml

Xml代码

```xml
<properties>    
      <property name="/examples/*.page"    
          path:sayHello="/examples/hello.jsp"    
          class="org.frameworkset.mvc.HelloWord">    
      </property>    
  </properties>   
```

附带说明一下，本文是参考bbossgroups 3.2版本功能编写