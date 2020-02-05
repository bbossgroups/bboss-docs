### bboss数据库标签系列一 分页列表详细信息标签预编译sql查询数据

**本系列文章详细介绍bboss标签库的数据库标签具体使用方法，涉及到的功能包括：db查询（普通查询、预编译查询，分页查询），db新增、修改、删除、批处理操作（预编译）。**

bboss数据库标签系列一 分页列表详细信息标签预编译sql查询数据

beaninfo标签，pager标签，list标签预编译sql获取数据功能相关属性和标签：

sqlparamskey-指定将绑定变量参数存储在request 属性集中的变量名称，以便pager，beaninfo，list标签获取sql的绑定变量参数值。本节详细介绍如何直接通过预编译sql语句来实现数据展示。

#### **1   详细信息Beaninfo标签**

Html代码 

```html
<%@ page contentType="text/html; charset=UTF-8" language="java" import="java.sql.*,java.util.List" errorPage=""%> 
```

##### **1.1 导入标签定义文件**

Html代码

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
```

<!--
    测试在beaninfo标签上直接执行预编译查询操作，获取详细信息实例    statement:数据库查询语句    dbname:查询的相应数据库名称，在poolman.xml文件中进行配置

-->

##### 1.2 定义预编译sql语句

Html代码

```html
<%  
    String table_name="td_sm_user";  
    String statement="select * from tableinfo where table_name=${table_name} " ;  
      
%>  
```

其中${table_name}为绑定变量，其值将由<pg:sqlparam标签来设定

Html代码

```html
<html>  
<head>  
<title>测试在beaninfo标签上直接执行数据库，获取详细信息实例</title>  
</head>  
<body>  
   
  
    <table>  
```

##### **1.3 通过sqlparams和sqlparam标签指定预编译绑定变量信息**

Html代码

```html
<pg:sqlparams sqlparamskey="key" pretoken="//$//{" endtoken="//}">  
  
       <pg:sqlparam name="table_name" value="<%= table_name %>" type="string"/>  
  
             
</pg:sqlparams>   
```

Sqlparams标签： sqlparamskey属性用来在request中存放这些绑定变量信息，pretoken="//$//{" endtoken="//}"分别指定了变量的定义前界符和后分界符。

Sqlparam指定了变量table_name的值以及类型为string，table_name必须和sql语句：

select * from tableinfo where table_name=${table_name}中的${table_name}一致。

##### **1.4 Beaninfo标签上执行预编译查询Statement指定预编译sql语句，sqlparamskey指定通过key属性从request中获取预编译参数绑定变量集**

Html代码

```html
<pg:beaninfo statement="<%=statement %>"   
  
              dbname="bspf"  
  
               sqlparamskey="key">  
  
         
           <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">  
  
              <td>  
                  table_name:  
              </td>   
              <td>  
                  <pg:cell colName="table_name" defaultValue=""/>  
  
              </td>   
              <td>  
                  table_id_name：  
              </td>  
              <td>  
                  <pg:cell colName="table_id_name" defaultValue="" />  
  
              </td>  
              <td class="tablecells" align=center height='30' width="5%">  
  
                  table_id_value:  
              </td>    
              <td class="tablecells" align=center height='30' width="5%">  
  
                  <pg:cell colName="table_id_value" defaultValue=""/>  
  
              </td>    
           </tr>  
       </pg:beaninfo>  
```

#### **2 列表展示list标签**

Html代码

```html
<%@ page contentType="text/html; charset=UTF-8" language="java" import="java.sql.*,java.util.List" errorPage=""%>  
```

##### **2.1 导入标签定义文件**

Html代码

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
```

<!--
    测试在list标签上直接执行 预编译数据库操作，获取列表信息实例    statement:数据库预编译查询语句    dbname:查询的相应数据库名称，在poolman.xml文件中进行配置

-->

##### 2.2 定义预编译sql语句

Html代码

```html
<%  
    String TABLE_ID_TYPE="int";  
    String statement="select * from tableinfo where TABLE_ID_TYPE=${TABLE_ID_TYPE} order by table_id_value desc" ;  
      
%>  
```

其中${TABLE_ID_TYPE}为绑定变量，其值将由<pg:sqlparam标签来设定

Html代码

```html
<html>  
<head>  
<title>测试在list标签上直接执行预编译数据库操作，获取分页列表信息实例</title>  
</head>  
<body>  
   
  
    <table>  
```

##### **2.3 通过sqlparams和sqlparam标签指定预编译绑定变量信息**

Html代码

```html
<pg:sqlparams sqlparamskey="key" pretoken="//$//{" endtoken="//}">  
  
             <pg:sqlparam name="TABLE_ID_TYPE" value="<%=TABLE_ID_TYPE %>" type="string"/>  
  
                   
      </pg:sqlparams>   
```

Sqlparams标签： sqlparamskey属性用来在request中存放这些绑定变量信息，pretoken="//$//{" endtoken="//}"分别指定了变量的定义前界符和后分界符。

Sqlparam指定了变量TABLE_ID_TYPE的值以及类型为int，TABLE_ID_TYPE必须和sql语句：

select * from tableinfo where TABLE_ID_TYPE=${TABLE_ID_TYPE} order by table_id_value desc
中的${TABLE_ID_TYPE}一致。

##### **2.4 list标签上执行预编译分页查询Statement指定预编译sql语句，sqlparamskey指定通过key属性从request中获取预编译参数绑定变量集**

Html代码

```html
 <pg:list  id="testid" statement="<%=statement%>" sqlparamskey="key"  
  
              dbname="bspf" isList="false" maxPageItems="5">  
  
           <pg:header>  
              <pg:title type="td" width="15%" className="headercolor" title="表名" sort="true" colName="table_name"/>  
  
              <pg:title type="td" width="15%" className="headercolor"  sort="true" colName="table_id_name" title="表id名"/>  
              <pg:title type="td" width="15%" className="headercolor"  sort="true" colName="table_id_value" title="表id值"/>              
           </pg:header>  
           <pg:param name="table_name"/>  
  
           <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">  
  
              <td> <pg:rowid offset="false" increament="1"/>  
  
                  <pg:cell colName="table_name" defaultValue=""/>  
  
              </td>   
              <td>  
                  <pg:cell colName="table_id_name" defaultValue="" />  
  
              </td>  
              <td class="tablecells" align=center height='30' width="5%">  
  
                  <pg:cell colName="table_id_value" defaultValue=""/>  
  
              </td>    
           </tr>  
       </pg:list>  
       <tr><td><pg:rowcount id="testid"/></td><td colspan="2">yi  
  
       <pg:index custom="true" id="testid"/>  
  
       </td></tr>      
         
    </table>  
</body>  
</html>  
```

#### **3 分页展示pager标签**

Html代码

```html
<%@ page contentType="text/html; charset=UTF-8" language="java" %>  
```

##### **3.1 导入标签定义文件**

Html代码 

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
```

<!--
    测试在pager标签上直接执行预编译数据库操作，获取列表/分页信息实例
    statement:数据库查询语句
    dbname:查询的相应数据库名称，在poolman.xml文件中进行配置

-->

##### **3.2 定义预编译sql语句**

Html代码

```html
<%  
    String object_id = "20";   
    String sql = "select * from sqltest  where object_id=#[object_id] order by object_name";  
 %>  
```

其中#[object_id]为绑定变量，其值将由<pg:sqlparam标签来设定

Html代码

```html
<html>  
<head>  
<title>测试在pager标签上直接执行数据库操作，获取分页列表信息实例</title>  
</head>  
<body>  
    <table>  
```

##### **3.3 通过sqlparams和sqlparam标签指定预编译绑定变量信息**          

Xml代码

```xml
<pg:sqlparams sqlparamskey="key" pretoken="#//[" endtoken="//]">  
  
              <pg:sqlparam name="object_id" value="<%=object_id %>" type="int"/>  
  
           </pg:sqlparams>  
```

 Sqlparams标签： sqlparamskey属性用来在request中存放这些绑定变量信息，pretoken="#//[" endtoken="//]"分别指定了变量的定义前界符和后分界符。

Sqlparam指定了变量object_id的值以及类型为int，object_id必须和sql语句：

select * from sqltest  where object_id=#[object_id] order by object_name

中的#[object_id]一致。  

##### **3.4  Pager标签上执行预编译分页查询Statement指定预编译sql语句，sqlparamskey指定通过key属性从request中获取预编译参数绑定变量集**          

Html代码

```html
<pg:pager statement="<%=sql %>"   
  
                 dbname="bspf"   
  
                 isList="false"   
  
                 pager_infoName="pager_info11"   
  
                 sqlparamskey="key" >            
  
                 <%  
                 System.out.println("pager_info11.getDataSize():" + pager_info11.getDataSize());  
                 %>  
                 <tr><td colspan="3"><pg:index/></td></tr>  
  
           <pg:list >  
  
           <%  
                 System.out.println("pager_info11.getDataSize()2:" + pager_info11.getDataSize());  
                 %>  
              <tr class="cms_data_tr" id="<pg:cell colName="object_id" defaultValue=""/>">  
  
                  <td>  
                     <pg:cell colName="owner" defaultValue=""/>  
  
                  </td>   
                  <td>  
                     <pg:cell colName="object_id" defaultValue="" />  
  
                  </td>  
                  <td class="tablecells" align=center height='30' width="5%">  
  
                     <pg:cell colName="object_name" defaultValue=""/>  
  
                  </td>    
              </tr>  
           </pg:list>  
           <tr><td colspan="3"><br><br></td></tr>  
  
           <%  
                 System.out.println("pager_info11.getDataSize()1:" + pager_info11.getDataSize());  
                 %>  
           </pg:pager>  
         
    </table>  
</body>  
</html>  
```

