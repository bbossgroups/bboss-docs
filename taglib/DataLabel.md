### bboss标签使用大全-数据展示标签

本文介绍bboss中所有数据展示标签使用方法。

#### 1.bbossgroups中的标签

**1.1.数据展示标签**

主要是bboss taglib中的一系列标签，很好地和mvc框架、jquery、jquery easyui结合使用： pager, listdata,notify,list, beaninfo,cell, rowid,rowcount,querystring ，convert,contextmenu,map,mapkey，param,params,index，config,size

树标签：tree，treedata，radio，checkbox  

**1.2.逻辑标签（可以和页面数据展示标签结合使用，也可以单独使用）**

equal,notequal,null,notnull, equalandlower, equalandupper,
upper, lower, in, notin,match,contain,notmatch,notcontain，empty，notempty,true,false,startwith,notstartwith,endwith,notendwith

**1.3.国际化和主题标签**message

theme

**1.4.mvc数据绑定错误信息展示标签**errors

error

globalerrors

**1.5.request/session标签**request

session

**1.6.数据库操作标签，有效防止sql注入问题**

dbutil-执行数据库增、删、改操作（预编译和普通）

sqlparams-用于支持在pager标签，beaninfo标签，list标签上执行预编译操作的绑定变量集合，同时可以指定sql绑定变量的定义语法分界符。  

batchutil-执行预编译批处理、普通批处理操作

statement-指定batchutil要执行的批处理语句，可以是预编译sql语句，也可以是普通sql语句

batch-指定statement指定的预编译sql语句的一组绑定变量

sqlparam-用来指定预编译操作的sql绑定变量参数的值、数据类型、数据格式，只能内置在dbutil，sqlparams，statement,batch三个标签中。

数据库标签的介绍，请参考文章：
http://yin-bp.iteye.com/blog/648161  

#### **2.下面全面介绍每类标签的简单用法。**

**2.1.标签定义文件的导入**

Js代码

```js
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<%@ taglib uri="/WEB-INF/treetag.tld" prefix="tree" %>  
<%@ taglib uri="/WEB-INF/commontag.tld" prefix="common"%>   
```

**2.2.config 标签**

config 用来导入标签库用到的js文件，enablecontextmenu用来控制是否输出右键菜单相关的js函数，false不输出，反之输出，使用方法如下：

Js代码

```js
<pg:config enablecontextmenu="false"/>  
```

**2.3.list标签**

用来输出list，set，map[],list<**map>**,list<string,number>等中的数据，使用方法如下,可以和pager标签结合使用，也可以直接从request，session，pagecontext中获取数据，或者直接从数据库获取数据,或者和list嵌套使用,或者通过actual属性结合el表达式获取需要展示的数据。

从request，session，pagecontext中获取数据：

Java代码

```java
<pg:list requestKey="serverHelloListBean" >           <pg:cell colName="name"/>  
       <pg:cell colName="id"/>  
  
</pg:list>  
```

结合el表达式获取数据：

Java代码

```java
<pg:list actual="${serverHelloListBean}" >            <pg:cell colName="name"/>  
       <pg:cell colName="id"/>  
  
</pg:list>  
```

和pager标签结合使用：

Html代码

```html
<pg:listdata dataInfo="test.pager.TableInfoListData" keyName="TableInfoListData" />  
                <!--分页显示开始,分页标签初始化-->  
                <pg:pager maxPageItems="15" scope="request" data="TableInfoListData"   
                          isList="false">  
                    <tr class="cms_report_tr">  
                        <!--设置分页表头-->  
                    <pg:header>                                     
                                          
                        <td width="2%" align=center style="width:5%">  
                        <input class="checkbox"   
                            type="checkBox" hidefocus=true   
                            name="checkBoxAll"   
                            onClick="checkAll('checkBoxAll','ID')">   
                        </td>  
                        <pg:title nowrap="true" width="5%" title="TABLE_NAME"  
                                            sort="false" colName="" className="report_header"/>  
                        <pg:title nowrap="true" width="5%" title="TABLE_ID_NAME"  
                                            sort="true" colName="TABLE_ID_NAME" className="report_header"/>  
                          
                        <td width="28%">  
                            TABLE_ID_INCREMENT</td>  
                          
                        <td width="6%">  
                            TABLE_ID_VALUE                      </td>  
                        <td width="9%">  
                            TABLE_ID_GENERATOR                      </td>  
                          
                        <td width="10%" height='30'>TABLE_ID_TYPE</td>  
                        <td width="10%" height='30'>TABLE_ID_PREFIX</td>  
                    </pg:header>                                    
                    </tr>  
                <pg:notify>  
                        <tr  class="labeltable_middle_tr_01">  
                            <td colspan=100 align='center' height="18px">  
                                没有数据  
                            </td>  
                        </tr>  
                </pg:notify>  
  
                      
                <pg:list  autosort="false">  
                    <tr class="cms_report_tr">  
                          
  
                        <td width="2%" align=center style="width:5%">  
                            <input class="checkbox" hideFocus onClick="checkOne('checkBoxAll','ID')"   
                            type="checkbox" name="ID"   
                            value="<pg:cell colName="TABLE_NAME" defaultValue=""/>">                                         
                        </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_NAME" defaultValue=""/>                   </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_ID_NAME" defaultValue=""/>                        </td>  
                        <td width="28%">  
                            <pg:cell colName="TABLE_ID_INCREMENT" defaultValue=""/></td>  
                          
                        <td width="6%">  
                            <pg:cell colName="TABLE_ID_VALUE" defaultValue=""/>                       </td>  
                        <td width="9%">  
                            <pg:cell colName="TABLE_ID_GENERATOR" defaultValue=""/>                       </td>  
                          
                        <td width="10%" height='30'><pg:cell colName="TABLE_ID_TYPE" defaultValue=""/></td>  
                        <td width="10%" height='30'><pg:cell colName="TABLE_ID_PREFIX" defaultValue=""/></td>  
                    </tr>  
                    </pg:list>  
                    <tr class="labeltable_middle_tr_01">  
                        <td colspan=11 ><div class="Data_List_Table_Bottom">   
                            共  
                            <pg:rowcount />  
                            条记录  
                            每页显示15条  
                            <pg:index />                  </div>  </td>  
                    </tr>  
                    <input id="queryString" name="queryString" value="<pg:querystring/>" type="hidden">  
                    <tr></tr>  
                </pg:pager>  
```

直接从数据库获取数据:

Html代码

```html
<pg:list statement="select * from tableinfo order by table_id_value desc"   
                  dbname="bspf">  
          
            <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">  
                <td>  
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
```

和list嵌套使用：

Java代码

```java
<pg:list requestKey="serverHelloListBean" >           <pg:cell colName="name"/>  
       <pg:list colName="innerdatas">  
        <pg:cell colName="innername"/>  
  <!--获取外围list的属性字段值，index是外层list索引，最外层为0-->  
        <pg:cell index="0" colName="name"/>  
  
       </pg:list>  
  
</pg:list>  
```

list标签还可嵌套在beaninfo、map标签中使用。list标签还可以输出数组的元素值：

Java代码

```java
<pg:list requestKey="serverHelloArray" >  
                                <pg:rowid increament="1"/> <pg:cell />  
                            </pg:list>  
```

最后我们看一个在列表中如何嵌套其它列表实现select元素中项默认选中的例子：

<pg:list requestKey="applistData">

​      <**select name="acctasscat">**

<**option value="">**<**/option>**

<pg:list requestKey="acctasscatList" >

​    <option value="<pg:cell colName='dict_item_code'/>" <pg:equal expression="{0.acctasscat}"  

expressionValue="{dict_item_code}">selected</

pg:equal>>

<pg:cell colName='dict_item_name'/>

<**/option>**

</

pg:list>

<**/select>**

</

pg:list>

第一个<pg:list requestKey="applistData">是一个对象列表，是需要展示的数据列表，对象中包含属性acctasscat，该属性值对应select下拉选择框的value，如果相应的下拉选择项的值和acctasscat属性的值相等则默认选中。

第二个<pg:list requestKey="acctasscatList" >是一个字典对象列表，我们用它来生成下拉选择框，dict_item_name属性是字典项名称，dict_item_code属性是字典项值，dict_item_code和第一个list中当前记录对象的acctasscat属性对应。

我们用逻辑标签equal来确定select的项是否被选中，equal标签上指定了两个表达式：expression="{0.acctasscat}"  expressionValue="{dict_item_code}"
expression和expressionValue分别代表要比较相等的两个操作数，
expression="{0.acctasscat}" 中的{0.acctasscat}的含义：{}中的名称是一个属性变量，.号前面的0代表最外层list的位置号（在这里表示第一个list，次外层为1，依次类推） ，.号后面的acctasscat表示取第一个list的当前记录的acctasscat属性值最为第一个比较操作数。

expressionValue="{dict_item_code}"中的{dict_item_code}的含义：{}中的名称是一个属性变量，dict_item_code表示取第二个list的当前记录的dict_item_code属性值最为第二个比较操作数。

**2.4.cell标签**

cell标签典型用法如下：

<pg:cell colName="id" />//默认输出值为""串

<pg:cell colName="id" defaultValue="mm"/>//默认输出值为"mm"串

<pg:cell colName="datea" dateformat="yyyy-MM-dd"/>

<pg:cell colName="money" numerformat="###.##"/>

<pg:cell colName="moudleCNName" htmlEncode="true"/>  //htmlEncode属性控制是否对输出进行html转义编码,true编码，false不编码，默认值为false

<pg:cell colName="id" maxlength="10"/>//超过最大长度10将被截断

<pg:cell colName="id" maxlength="10" replace="..."/>//超过最大长度10将被截断,截断的串将被replace指定的值替换掉

<pg:cell/> //直接输出对象或者当前集合中的记录值（list，数组，map，set中放置的是基本数据类型String,number等）    

如果cell标签展示对对象属性时一个对象的话，可以有两种方式来展示colName对应的属性对象中的属性值：

son属性是一个对象类型，结构为：

son{name,class,sex}

第一种 直接用property属性来获取son属性中的name属性：

<pg:cell colName="son" property="name" />

第二种 通过嵌套beaninfo标签来展示son对象中的所有属性：

<pg:beaninfo colName="son">

   <pg:cell colName="name"/>

   <pg:cell colName="class"/>

   <pg:cell colName="sex"/>

</pg:beaninfo>

cell标签可以嵌套在beaninfo、list、map标签中使用也可以单独使用，单独使用的方法如下：

<pg:cell actual="${param.name}" />//直接输出el表达式${param.name}代表的值。

另外cell标签中提供了usecurrentCellValuetoCellName和currentcelltoColName两个属性，具体使用方法可参考文档：
[bboss标签实现列表中的动态列数据展示方法](http://yin-bp.iteye.com/blog/2148013)  

**2.5.beaninfo标签**

beaninfo标签用来展示po详细信息的标签，具体用法有从request，session，pageContext中获取要展示的对象，或者从db中获取要展示的数据，或者嵌套在list，map，beaninfo标签中展示属性对应的对象。

从request获取要展示的对象：

Java代码

```java
<pg:beaninfo requestKey="serverHelloMapBean" >  
                                <pg:cell colName="name"/>  
                            </pg:beaninfo>  
```

从db中获取要展示的数据：

Java代码

```java
<pg:beaninfo requestKey="serverHelloMapBean" >  
                                <pg:cell colName="name"/>  
                            </pg:beaninfo>  
```

从db中获取要展示的数据：

Java代码

```java
<pg:beaninfo statement="select * from tableinfo where lower(table_name)='td_sm_user' order by table_id_value desc"   
                  dbname="bspf">  
          
            <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">  
                <td>  
                    <pg:cell colName="table_name" defaultValue=""/>  
                </td>   
                <td>  
                    <pg:cell colName="table_id_name" defaultValue="" />  
                </td>  
                <td class="tablecells" align=center height='30' width="5%">  
                    <pg:cell colName="table_id_value" defaultValue=""/>  
                </td>   
            </tr>  
        </pg:beaninfo>  
```

嵌套在list中使用：

Java代码

```java
<pg:list requestKey="serverHelloListBean" >           <pg:cell colName="name"/>  
       <pg:beaninfo colName="innerdatas">  
        <pg:cell colName="innername"/>  
  <!--获取外围list的属性字段值，index是外层list索引，最外层为0-->  
        <pg:cell index="0" colName="name"/>  
  
       </pg:beaninfo>  
  
</pg:list>  
```

**2.6.map、mapkey标签**

map标签有两个作用：

1.用来迭代展示map中的所有对象详细信息，map标签展示的数据可以从request，session，pagecontext中获取，也可以嵌套在list，beaninfo，map标签中使用,也可以通过actual属性结合el表达式获取展示数据。

2.用来输出map中的某个值

mapkey标签在map标签中使用，用来输出map中的key值。

map中value可以为各种复杂的对象类型，value可以为普通的bean对象，基础数据类型，list/map/数组等容器对象。

map包括以下主要属性：

requestKey:指定map对象存储在request中的key名称

colName：map对象来源于bean属性名称

keycell：只展示map中的一个数据，指定map所对应的外围容器中当前记录对象作为key值，map标签然后获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

key：只展示map中的一个数据，指定map数据key，从map中获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

keycolName：只展示map中的一个数据，指定map所对应的外围容器中对象中的属性名称，key的由该属性对应的值指定，map标签然后获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

从request获取要展示的对象：  

Java代码

```java
<pg:map requestKey="serverHelloMapBean" >  
      
         <pg:mapkey/>  
<pg:cell colName="name"/>  
                            </pg:map>  
```

通过actual属性结合el表达式获取要展示的对象：

Java代码

```java
<pg:map actual="${serverHelloMapBean}" >  
      
         <pg:mapkey/>  
<pg:cell colName="name"/>  
                            </pg:map>  
```

指定key获取map中的一个值：

Java代码

```java
<pg:map requestKey="serverHelloMapBean" key="testkey">  
      
         <pg:cell />  
                            </pg:map>  
```

如果testkey对应的是一个对象，则可以结合cell标签输出每个属性的值：

Java代码

```java
<pg:map requestKey="serverHelloMapBean" key="testkey">  
      
         <pg:cell colName="pro1"/>  
    <pg:cell colName="pro2"/>  
</pg:map>  
```

在list或者beaninfo中根据对象属性值获取map中的一个值：

Java代码

```java
<pg:list requestKey="serverHelloListBean" >     
<pg:cell colName="name"/>  
  
<pg:map requestKey="serverHelloMapBean" keycolName="testkey">  
      
         <pg:cell/>  
</pg:map>  
</pg:list>  
```

嵌套在list中使用：

Java代码

```java
<pg:list requestKey="serverHelloListBean" >     
        <pg:cell colName="name"/>  
       <pg:map colName="innerdatas">  
<pg:mapkey/>  
        <pg:cell colName="innername"/>  
  <!--获取外围list的属性字段值，index是外层list索引，最外层为0-->  
        <pg:cell index="0" colName="name"/>  
  
       </pg:map>  
  
</pg:list>  
```

map标签还可以展示Map<Stirng,String>等基础数据类型value的迭代：

Java代码 

```java
<table>  
        <h3>map<String,String>字符串信息迭代功能</h3>  
        <pg:map requestKey="mapstrings">  
          
            <tr class="cms_data_tr">  
                <td>  
                    mapkey:<pg:mapkey/>  
                </td>   
                <td>  
                    value:<pg:cell/>  
                </td>   
                  
            </tr>  
        </pg:map>  
          
          
    </table>  
```

map标签嵌套在beaninfo中使用：

<pg:beaninfo requestKey="portalApplication">

   <select id="module_id" name="module_id" class="select1"style="width: 150px;">

<pg:map requestKey="moduleMap">

​     <option value="<pg:mapkey/>" <pg:equal expression="{mapkey}" expressionValue="{0.module_id}">selected</pg:equal>> <pg:cell/> <**/option>** </

pg:map>

<**/select>**

​     </

pg:beaninfo>

其中<pg:equal expression="{mapkey}" expressionValue="{0.module_id} ">selected</pg:equal>的含义：

如果map迭代中当前的key和beaninfo标签表示的对象的module_id属性相等则输出selected（0.表示最外层容器的索引编号，次外层为1.,其他依次类推）

map标签展示map中某个key对应值以及更加复杂的案例参考文章：[bboss中的map标签结合list标签/cell标签展示复杂数据结构案例](http://yin-bp.iteye.com/blog/1668729)

map中嵌套beaninfo标签：

Xml代码

```xml
<pg:map requestKey="moduleMap">  
    <pg:mapkey/>    
       <pg:cell colName="name"/>  
       <pg:beaninfo colName="classInfo">  
           <pg:cell colName="className"/>  
       </pg:beaninfo>  
           
</pg:map> 
```

**2.7.pager、listdata、querystring、rowcount、param、params、index、title、notify标签**

pager标签主要用来和index、listdata、list等标签或者mvc框架控制器方法结合实现分页功能，分页数据可以从数据加载器中获取，也可以从db中直接或取，还可以从mvc控制器方法中获取。另外还可以和ajax结合在一个页面中加载多个分页模块。

从数据加载器中获取数据：

Java代码

```java
<pg:listdata dataInfo="test.pager.TableInfoListData" keyName="TableInfoListData" />  
                <!--分页显示开始,分页标签初始化-->  
                <pg:pager maxPageItems="15" scope="request" data="TableInfoListData"   
                          isList="false">  
<pg:param name="table_name"/>   
<pg:params name="ids"/>   
                    <tr >  
                        <!--设置分页表头-->  
                    <pg:header>                                     
                                          
                        <td width="2%" align=center style="width:5%">  
                        <input class="checkbox"   
                            type="checkBox" hidefocus=true   
                            name="checkBoxAll"   
                            onClick="checkAll('checkBoxAll','ID')">   
                        </td>  
                        <pg:title nowrap="true" width="5%" title="TABLE_NAME"  
                                            sort="false" colName="" className="report_header"/>  
                        <pg:title nowrap="true" width="5%" title="TABLE_ID_NAME"  
                                            sort="true" colName="TABLE_ID_NAME" className="report_header"/>  
                          
                                            </pg:header>                                    
                    </tr>  
                <pg:notify>  
                        <tr  class="labeltable_middle_tr_01">  
                            <td colspan=100 align='center' height="18px">  
                                没有数据  
                            </td>  
                        </tr>  
                </pg:notify>  
  
                      
                <pg:list  autosort="false">  
                    <tr class="cms_report_tr">  
                          
  
                        <td width="2%" align=center style="width:5%">  
                            <input class="checkbox" hideFocus onClick="checkOne('checkBoxAll','ID')"   
                            type="checkbox" name="ID"   
                            value="<pg:cell colName="TABLE_NAME" defaultValue=""/>">                                         
                        </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_NAME" defaultValue=""/>                   </td>  
                        <td width="8%">  
                            <pg:cell colName="TABLE_ID_NAME" defaultValue=""/>                        </td>  
                                            </tr>  
                    </pg:list>  
                    <tr class="labeltable_middle_tr_01">  
                        <td colspan=11 ><div class="Data_List_Table_Bottom">   
                            共  
                            <pg:rowcount />  
                            条记录  
                            每页显示15条  
                            <pg:index />                  </div>  </td>  
                    </tr>  
                    <input id="queryString" name="queryString" value="<pg:querystring/>" type="hidden">  
                    <tr></tr>  
                </pg:pager>  
```

其中listdata标签指定了分页标签的数据加载器，并将加载器存放在request的attribute属性TableInfoListData中，pager标签通过data属性引用该加载器，并且指定了每页最多显示15条记录；param 标签用来记录页面请求参数以便上下翻页时不丢失请求或者查询参数(单个参数)；params 标签用来记录页面请求参数数组以便上下翻页时不丢失请求或者查询参数(参数数组)；notify标签用来输出没有数据的提示信息；list标签用来迭代输出当页数据；rowcount标签输出总记录数；index标签输出以下内容：

上下分页导航按钮

共几页，每页多少条记录，调整到第几页，设置每页记录数等等

querystring标签用来输出页面所有参数串，用来方便进行处理和操作后任然跳回到当前页面。

从数据库中直接获取数据，这个和上面的用法的唯一区别就是：

不需要listdata标签，只需要将pager标签如下使用即可：

Java代码

```java
<pg:pager statement="select * from sqltest where rownum < 10 order by object_name "   
                  dbname="portal" isList="false" >  
```

和ajax结合在一个页面中加载多个分页模块的使用方法：

主页面内容：

Java代码

```java
 <pg:config/>  
<body>  
        <div id="pagecontainer">  
            <script type="text/javascript">  
            $(document).ready(function(){  
                  $("#pagecontainer").load("pagerqueryuser.htm #pagecontent");  
                });  
            </script>  
        </div>  
          
        <div id="pagecontainer1">  
            <script type="text/javascript">  
            $(document).ready(function(){  
                  $("#pagecontainer1").load("pagerqueryuser1.htm #pagecontent");  
                });  
            </script>  
        </div>  
</body>  
```

两个从页面内容基本上差不多：

Java代码

```java
<div id="pagecontent">  
                
                <pg:pager scope="request" data="users"   
                          isList="false" containerid="pagecontainer" selector="pagecontent">  
                <pg:param name="userName" encode="true"/>  
            <table class="genericTbl">  
                    <tr >  
                        <pg:title nowrap="true" width="8%" title="用户ID:"  
                                            sort="true" colName="userId" className="order1 sorted"/>   
                          
                        <pg:title nowrap="true" width="8%" title="用户NAME:"  
                                            sort="true" colName="userName" className="order1 sorted"/>     
                    </tr>  
                    <pg:notify>  
                    <tr class="cms_report_tr">  
                    <td width="10%" align=center colspan="100" style="width:5%">  
                        没有数据  
                    </td>  
                    </tr>               
                </pg:notify>  
                <pg:list autosort="false">  
                    <tr class="even">  
                          
                         <td ><pg:cell colName="userId" defaultValue=""/>    
                         </td>    
                         <td width="8%" >    
                          <pg:cell colName="userName" defaultValue=""/></td>    
                    </tr>  
                </pg:list>  
            </table>  
            <div><pg:index/></div>  
            </pg:pager>   
</div>  
```

需要说明的就是pager标签上新加的两个属性

containerid="pagecontainer" //代表main页面的div容器id，这个分页list的内容将在该div中展示

selector="pagecontent" //表示分页页面内容的选择器

data="users"对应的ListInfo对象直接从mvc控制器方法设置到request的attribute属性中。例如：

Java代码

```java
public String pagerqueryuser1(@PagerParam(name=PagerParam.SORT ) String sortKey,  
        @PagerParam(name=PagerParam.DESC,defaultvalue="true") boolean desc,  
        @PagerParam(name=PagerParam.OFFSET) long offset,  
        @PagerParam(name=PagerParam.PAGE_SIZE,defaultvalue="2") int pagesize,  
        @RequestParam(name = "userName") String username ,  
        ModelMap model) throws UserManagerException {  
    ListInfo userTemp = userService.getUsersNullRowHandler(username,offset, pagesize);  
    model.addAttribute("users", userTemp);  
    return "jquerypagine/page1";  
}  
```

**特别说明：分页跳转时如何记录页面的查询参数或者其他请求参数**

通过param标签和params标签来记录页面的查询参数或者其他参数，放置的位置一般放置在pager标签下面，例如：

<pg:pager maxPageItems="15" scope="request" data="TableInfoListData"
  isList="false">

**<pg:param name="table_name"/>**

**<pg:params name="ids"/>**

**param**标签用于记录单个值的参数，参数名称通过name指定

**params**标签用于记录多个值的参数，参数名称通过name指定

**2.8.index标签**

index标签作为导航标签具有以下实用的属性：

export-控制导航键是否出现得属性：000000000（9位） 分别对应于"第几页"、“共几页”、“首页”、“下一页”、“上一页”、“尾页”、“跳转到”，“每页显示几条记录”,"共多少条记录"
对应得位置上的值为0时标示显示该按钮，为1时不显示。

useimage-导航按钮是否使用图片,默认值为false 只有useimage=true时，imagedir和imageextend才起作用
如果useimage=true时，没有指定imagedir和imageextend属性，那么采用默认属性 使用方法 <pg:index
useimage="true" imagedir="/include/images" imageextend=" border=0 "/>

useimage-如果useimage=true时，没有指定imagedir和imageextend属性，那么采用默认属性 使用方法 <pg:index
useimage="true" imagedir="/include/images" imageextend=" border=0 "/>

imagedir-导航按钮图片存放目录，存放的图片名称为： first.gif-首 页 next.gif-下一页 pre.gif-上一页

last.gif-尾 页 默认值为：/include/images  

imageextend-导航图片的扩展属性串，默认值为：" border=0 "

classname-中间页面样式名称，类似于google的页码1页，2页的样式

tagnumber-设置展示的中间页面数，默认为-1,即不展示中间页

centerextend-中间页扩展属性，用来设置每个页面元素的额外css属性和其他扩展属性

sizescope-可选择页面显示记录数，默认为:

"5","10","20","30","40","50","60","70","80","90","100"

用户可以自定义这个范围，以逗号分隔即可  

如果在pager标签和list标签上指定的maxPageItems属性对应的页面记录条数不在sizescope范围中，那么
将把maxPageItems作为第一个选项加入到sizescope中

每页默认显示多少条记录有两个地方可以指定,一个地方是pager标签上指定：

Xml代码

```xml
<pg:pager maxPageItems="15" ...  
```

一个地方是bboss mvc控制器分页方法参数pagesize的注解中指定defaultvalue="2"：

Java代码

```java
 public String pagerqueryuser1(......  
            @PagerParam(name=PagerParam.PAGE_SIZE,defaultvalue="2") int pagesize,  
......  
    }  
```

  一旦手动切换了每页显示记录条数，则会将这个最新的记录条数存到cookie中，不再使用默认设置的最大记录条数，除非清除cookie。

**2.9.rowid、rowcount、pagesize标签**

rowid标签用来输出list和map循环中的行号，具体使用方法如下：

<pg:rowid /> //默认从0开始

<pg:rowid increament="1"/>  //从1开始

<pg:rowid  offset="true"/>  offset为用来表示每页是否都从0开始重新计数

rowcount标签用来输出总记录数，用法如下：

<pg:rowcount />

pagesize用来输出当前页面记录，用法如下：

<pg:pagesize />  

**2.10 convert标签**

convert标签主要用来实现属性值的转码，也就是说经属性值和名称的映射关系放到map中，然后通过convert标签结合colName和code属性结合来实现代码转换：

<%

Map aaaaa = new HashMap();

aaaaa.put("1","男");

aaaaa.put("2","女");

request.setAttribute("codes",aaaaa);

%>

<pg:convert convertData="codes" colName="sex"/>  

**2.11.标签内置变量**

list、map、beaninfo标签都有一些内置java变量，可以直接在标签中使用：

list、map标签：dataSet对象，可以使用java代码访问该对象获取数据

beaninfo标签：beanifo对象，可以使用java代码访问该对象获取数据

更多的标签属性可以参考标签定义tld文件：[pager-taglib.tld](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/pager-taglib.tld)

未完待续。


  