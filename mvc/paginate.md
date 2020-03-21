### 通过bboss mvc实现分页操作

通过bboss mvc实现分页操作

mvc demo下载和部署方法可以参考文档：

http://yin-bp.iteye.com/blog/1026245

这里介绍一下通过bboss mvc实现分页操作的实现步骤，呵呵

1.首先编写分页demo的配置文件为：

Xml代码 

```xml
<?xml version="1.0" encoding='gb2312'?>  
<!--   
bboss-demo.xml  
描述：分页处理控制器demo  
-->  
<properties>  
    <property name="/pager/*.html" class="org.frameworkset.spi.mvc.PaginController"/>  
</properties>  
```

2.编写控制器代码类

Java代码

```java
package org.frameworkset.spi.mvc;  
  
import java.io.IOException;  
import java.sql.SQLException;  
import java.util.List;  
  
import javax.servlet.http.HttpServletResponse;  
import javax.servlet.jsp.PageContext;  
  
import org.frameworkset.util.annotations.PagerParam;  
import org.frameworkset.util.annotations.RequestParam;  
import org.frameworkset.web.servlet.ModelAndView;  
import org.frameworkset.web.servlet.ModelMap;  
  
import test.pager.TableInfo;  
  
import com.frameworkset.common.poolman.PreparedDBUtil;  
import com.frameworkset.common.poolman.SQLExecutor;  
import com.frameworkset.util.ListInfo;  
  
/** 
 *  
 * @author Administrator 
 * 
 */  
public class PaginController {  
      
    /** 
     * http://localhost:8080/bboss-mvc/pager/firstpagerdemo.html 
     * @param sortKey 
     * @param desc 
     * @param offset 
     * @param pagesize 
     * @return 
     */  
    public ModelAndView firstpagerdemo(@PagerParam(name=PagerParam.SORT ) String sortKey,  
            @PagerParam(name=PagerParam.DESC,defaultvalue="true") boolean desc,  
            @PagerParam(name=PagerParam.OFFSET) long offset,  
            @PagerParam(name=PagerParam.PAGE_SIZE,defaultvalue="2") int pagesize,  
            @RequestParam(name="TABLE_NAME") String tablename  
              
            )  
    {             
        String sql = "select * from tableinfo";  
        boolean usecondition = tablename != null && !tablename.equals("");  
        if(usecondition)  
            sql += " where TABLE_NAME like ?";  
          
        ModelAndView view = new ModelAndView("/pager/pagerdemo");  
        try {  
            ListInfo datas = null;  
            if(usecondition)  
            {  
                datas = SQLExecutor.queryListInfo(TableInfo.class, sql, offset, pagesize, "%" + tablename + "%");  
            }  
            else  
            {  
                datas = SQLExecutor.queryListInfo(TableInfo.class, sql, offset, pagesize);  
            }  
            view.addObject("pagedata", datas);  
          
//          datas.setMaxPageItems(pagesize);  
              
              
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
      
          
        return view;  
    }  
      
    /** 
     * http://localhost:8080/bboss-mvc/pager/pagerdemo.html 
     * @param sortKey 
     * @param desc 
     * @param offset 
     * @param pagesize 
     * @return 
     */  
    public ModelAndView pagerdemo(@PagerParam(name=PagerParam.SORT ) String sortKey,  
            @PagerParam(name=PagerParam.DESC,defaultvalue="true") boolean desc,  
            @PagerParam(name=PagerParam.OFFSET) long offset,  
            @PagerParam(name=PagerParam.PAGE_SIZE,defaultvalue="2") int pagesize,  
            @RequestParam(name="TABLE_NAME") String tablename,  
            PageContext context,  
            ModelMap model  
            )  
    {             
        String sql = "select * from tableinfo";  
        boolean usecondition = tablename != null && !tablename.equals("");  
        if(usecondition)  
            sql += " where TABLE_NAME like ?";  
        ListInfo datas = new ListInfo();  
        PreparedDBUtil db = new PreparedDBUtil();  
        try {  
            db.preparedSelect(sql,offset,pagesize);  
            if(usecondition)  
                db.setString(1, "%" + tablename + "%");  
            List<TableInfo> tables = db.executePreparedForList(TableInfo.class);  
              
            datas.setTotalSize(db.getTotalSize());//设置总记录数  
            datas.setDatas(tables);//设置当页数据  
//          datas.setMaxPageItems(pagesize);  
              
              
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
      
        ModelAndView view = new ModelAndView("/pager/pagerdemo","pagedata", datas);  
        return view;  
    }  
      
    public void testcn(HttpServletResponse response)  
    {  
        try {  
            response.setContentType("text/html; charset=GBK");  
            response.getWriter().print("中文");  
              
        } catch (IOException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
  
}  
```

3.编写jsp页面：

Js代码

```js
<%@ page contentType="text/html; charset=GBK" language="java" %>  
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<%@ page import="org.frameworkset.web.servlet.support.RequestContext" %>  
  
<!--   
    测试在通过控制器获取分页列表数据，并且提供查询功能  
-->  
<html>  
<head>  
<title>测试在通过控制器获取分页列表数据，并且提供查询功能</title>  
</head>  
<body>  
    <table>  
                <tr class="cms_report_tr">  
                        <!--设置分页表头-->  
                    <form action="<%=RequestContext.getPathWithinHandlerMappingPath(request)%>" method="post">  
                        <td  style="width:20%">请输入表名：</td>  
                        <td  style="width:5%" colspan="100"><input type="text" name="TABLE_NAME" value="<%=request.getParameter("TABLE_NAME") %>"><input type="submit" name="查询" value="查询"></td>  
                    </form>  
                </tr>  
                          
          
                <!--分页显示开始,分页标签初始化-->  
                <pg:pager scope="request" data="pagedata"   
                          isList="false">  
                <pg:param name="TABLE_NAME"/>  
                    <tr class="cms_report_tr">  
                        <!--设置分页表头-->  
  
                        <td width="2%" align=center style="width:5%">  
                        <input class="checkbox"   
                            type="checkBox" hidefocus=true   
                            name="checkBoxAll"   
                            onClick="checkAll('checkBoxAll','ID')">   
                        </td>  
                        <td width="8%">  
                            TABLE_NAME                  </td>  
                        <td width="8%">  
                            TABLE_ID_GENERATOR                  </td>  
                            <td width="8%">  
                            TABLE_ID_TYPE                   </td>  
                          
                    </tr>  
                  
                <pg:notify>  
                    <tr class="cms_report_tr">  
                          
  
                    <td width="2%" align=center style="width:5%">  
                        没有数据  
                    </td>  
                    </tr>               
                </pg:notify>  
                      
                <pg:list >  
                  
                    <tr class="cms_report_tr">  
                          
  
                        <td width="2%" align=center style="width:5%">  
                            <input class="checkbox" hideFocus onClick="checkOne('checkBoxAll','ID')"   
                            type="checkbox" name="ID"   
                            value="<pg:cell colName="TABLE_NAME" defaultValue=""/>">  
                            <img border="0" src="${pageContext.request.contextPath}<pg:theme code="exclamation.gif"/>"  
                                         alt="<pg:message code="probe.jsp.datasources.list.misconfigured.alt"/>"/>                                       
                        </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_NAME" defaultValue=""/>         
                            <pg:message var="messagecode" code="probe.jsp.wrongparams"/>  
                            ${messagecode}  
                                        </td>  
                              
                          
                        <td width="8%">  
                            <pg:cell colName="TABLE_ID_GENERATOR" defaultValue=""/>         
                            <pg:message var="messagecode" code="probe.jsp.wrongparams"/>  
                            ${messagecode}  
                                        </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_ID_TYPE" defaultValue=""/>          
                            <pg:message var="messagecode" code="probe.jsp.wrongparams"/>  
                            ${messagecode}  
                                        </td>  
                    </tr>  
                    </pg:list>  
                      
                      
                    <tr><pg:index/></tr>  
                </pg:pager>   
      
                  
    </table>  
</body>  
</html>  
```

到此整个分页的所有代码就做好了，实际效果可以自己下载demo应用操作体验一下，如果demo已经部署好的话，可以在浏览器中输入以下地址看分页的效果：

http://localhost:8080/bboss-mvc/pager/pagerdemo.html

如果要开始动手做自己的例子可以参考文档：

《搭建自己的bbossmvc eclipse开发工程，编写第一个实例》

http://yin-bp.iteye.com/blog/1026261