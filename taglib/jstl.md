### bboss标签库与jstl标签库功能对比

本文就bboss标签库与jstl标签库中的几个常用标签做个简单的对比：

逻辑标签和数据展示标签

1.导入的tld文件

使用bboss标签库，jsp头部需要导入：

Java代码 

```java
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
```

jstl标签库,jsp头部导入：

Java代码 

```java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core"  prefix="c"%>  
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn"%>  
```

2.逻辑判断标签对比

分别使用bboss和jstl来实现逻辑或条件判断功能:menuType的值为 'item'或者'module'或者'subSystem'时为真：

先看jstl

Java代码 

```java
<c:if test="${menuType eq 'item' or menuType eq 'module' or menuType eq 'subSystem'}">  
    do somthing.  
</c:if>  
```

再看bboss，可以通过in标签和true标签来实现上述功能(使用in标签是不是比if标签要更简单和直观一些呢？)：

Java代码 

```java
<pg:in actual="${menuType}" scope="item,module,subSystem">  
     do somthing.  
</pg:in>  
```

bboss的true标签实现上述功能(true比if的语义更加明确)：

Java代码 

```java
<pg:true actual="${menuType eq 'item' or menuType eq 'module' or menuType eq 'subSystem'}">  
    do somthing.  
</pg:true>  
```

if-else对比

先看jstl

Java代码 

```java
<c:choose>  
   <c:when test="${param.newFlag== '1' || param.newFlag== '2' ||param.newFlag== '3'}">    
    <th>作品名称<font color="Red">*</font>：</th>        
   </c:when>  
   <c:otherwise>   
   <th>班级<font color="Red">*</font>：</th>  
   </c:otherwise>  
</c:choose>  
```

再看bboss：

Java代码 

```java
<pg:true actual="${param.newFlag== '1' || param.newFlag== '2' ||param.newFlag== '3'}"  evalbody="true">  
    <pg:yes>  
    <th>作品名称<font color="Red">*</font>：</th>    
    </pg:yes>  
    <pg:no>  
    <th>班级<font color="Red">*</font>：</th>  
    </pg:no>  
 </pg:true>  
```

再看看bboss的if-elseif-else:

Java代码

```java
<pg:case actual="${count}">  
          
            <pg:equal value="1">  
                yes,1！  
            </pg:equal>  
            <pg:equal value="2">  
                yes,2！  
            </pg:equal>  
            <pg:other>  
                yes,other！！  
            </pg:other>  
        </pg:case>  
```

bboss的逻辑标签比较丰富，功能丰富，语义明确，除了true和in，还有相对应的false和notin标签，他们可以嵌套组合实现if/else等各种各样的逻辑判断操作，更加详细的信息可参考文档[《bbossgroups标签库使用大全（续）》](http://yin-bp.iteye.com/blog/1137674)；jstl的if等逻辑标签功能也非常丰富，但是语义不明确，看起来比较费劲。

3.集合展示标签对比

主要是collection/数组/map的展示标签，先看看jstl如何来展示一个collection集合：

Java代码 

```java
<c:forEach var="subSys"  items="${subSystems}">  
                            <fieldset>  
                                <legend>${subSys.value.name}</legend>  
                                <table>  
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        id:  
                                    </td>  
                                    <td class="detailcontent">  
                                        ${subSys.value.id}  
                                    </td>  
                                </tr>  
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        name:  
                                    </td>  
                                    <td class="detailcontent">  
                                        <c:forEach var="name" items="${subSys.value.localeNames}" varStatus="status">  
                                            ${name.key}:${name.value}${status.index != fn:length(subSys.value.localeNames)-1 ? "," : ""}&nbsp;  
                                        </c:forEach>  
                                    </td>  
                                </tr>  
                              
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        baseuri:  
                                    </td>  
                                    <td class="detailcontent">  
                                        ${subSys.value.baseuri}  
                                    </td>  
                                </tr>  
                                </table>  
                            </fieldset>  
                        </c:forEach>  
```

再来看看bboss的list标签如何展示上述功能:

Java代码

```java
<pg:list actual="${subSystems}">  
                            <fieldset>  
                                <legend><pg:cell colName="name"/></legend>  
                                <table>  
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        id:  
                                    </td>  
                                    <td class="detailcontent">  
                                        <pg:cell colName="id"/>  
                                    </td>  
                                </tr>  
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        name:  
                                    </td>  
                                    <td class="detailcontent">  
                                        <pg:map colName="localeNames" >  
                                            <pg:mapkey />:<pg:cell /><pg:equal expression="{rowcount}-1" expressionValue="{rowid}"> ","</pg:equal>&nbsp;  
                                        </pg:map>  
                                    </td>  
                                </tr>  
                              
                                <tr>  
                                    <td width="124" height="28" class="detailtitle">  
                                        baseuri:  
                                    </td>  
                                    <td class="detailcontent">  
                                        <pg:cell colName="baseuri"/>  
                                    </td>  
                                </tr>  
                                </table>  
                            </fieldset>  
                        </pg:list>  
```

jstl的foreach和bboss的list标签都可以很好地展示collection、数组、set中的数据，也能够嵌套使用，bboss有专门的map标签来展示map数据。jstl为了展示数据会定义一个数据变量和状态变量，并且获取集合长度还要反复调用fn标签；而bboss无需定义这两个变量，不仅内置了rowcount标签和rowid标签，而且在equal等逻辑标签的expression和expressionValue表达式属性中还可以使用以下内置变量：

Java代码

```java
rowid：遍历集合的当前行号（从0开始）  
offset ：分页标签中当前页的第一条记录在总记录中所处的位置  
rowcount :总记录数  
pagesize :当前页或者当前列表获取到的记录数  
mapkey ：map中当前记录的key  
currentcell：map中当前记录的值  
```

如果上述的list展示内容较多，我们可以精简一下：

bboss

Java代码 

```java
<pg:list actual="${subSystems}">  
<pg:cell colName="name"/>//对象中的name属性  
<pg:cell colName="id"/>//对象中的id属性  
</pg:list>  
```

jstl实现相同的功能：

Java代码 

```java
<c:forEach var="subSys"  items="${subSystems}">  
${subSys.value.name}  
${subSys.value.id}  
</c:forEach>  
```

呵呵，再看看一个精简的map数据的展示功能：

bboss

Java代码

```java
<pg:map actual="${publicItem.workspacecontentExtendAttribute}">  
<pg:mapkey />  
<pg:cell/>  
</pg:map>  
```

jstl

Java代码

```java
<c:forEach var="attribute" items="${publicItem.workspacecontentExtendAttribute}">  
${attribute.key}                                ${attribute.value}  
</c:forEach>  
```

复杂一点的：

bboss

Java代码

```java
<pg:map actual="${publicItem.localeHeadimgs}" >                       <pg:notequal expression="{rowid}" value="0">,</pg:notequal><pg:mapkey/>:<pg:cell/>&nbsp;  
</pg:map>  
```

jstl

Java代码

```java
<c:forEach var="headimg" items="${publicItem.localeHeadimgs}" varStatus="status"> ${headimg.key}:${headimg.value}${status.index != fn:length(publicItem.localeHeadimgs)-1 ? "," : ""}&nbsp;           </c:forEach>  
```

bboss的数据展示标签的详细使用，请参考文档[《bbossgroups标签库使用大全 》](http://yin-bp.iteye.com/blog/1136924)。

4.总结

通过本文几个简单的功能对比，可以看出jstl和bboss的基本功能都非常丰富，从对比中可以看出bboss标签展示数据和实现逻辑判断等功能时语义更加明确，代码更加通俗易懂。（本文观点仅代表作者本人观点，欢迎大家讨论交流）