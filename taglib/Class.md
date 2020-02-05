### bboss逻辑标签判断对象类型是否为给定的Class类型方法

在java代码中可以非常方便地判断对象类型是否是给定的类型,例如：

if(object instanceof java.util.Map)

  do something.

那么在jsp中也可能需要识别对象的class类型并做出相应的处理，本文介绍采用bboss逻辑标签来判断对象类型是否为特定的Class类型方法。

bboss逻辑标签来判断对象类型是否为给定的Class类型通过true和false两个逻辑标签来实现，通过两个标签的typeof属性来指定需要匹配的Class类型，用来检测相应数据类型是否是typeof给定的类型，typeof可以字符串类型的类路径，也可以直接是Class对象。

bboss逻辑标签的使用文档可以参考：

http://yin-bp.iteye.com/blog/1137674

具体使用方法如下:

typeof值为字符串类型的类路径

Html代码 

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<pg:true typeof="java.util.Map">  
   do something here.  
</pg:true>  
  
<pg:false typeof="java.util.Map">  
   do something here.  
</pg:false>  
```

typeof值直接是Class对象

Html代码 

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<pg:true typeof="<%=java.util.Map.class%>">  
   do something here.  
</pg:true>  
  
<pg:false typeof="<%=java.util.Map.class%>">  
   do something here.  
</pg:false>  
```

true标签只有在对应的数据类型匹配上typeof给定的类型才成立
false标签只有在对应的数据类型没有匹配上typeof给定的类型才成立

我们来看一个具体的实例：

这个列子中我们构建一个map容器，容器中放置两种类型的数据，一种数据的类型为test.TestBean,另一种数据的类型为java.util.Map,然后在jsp页面中用map标签输出这些数据，输出数据时需要用到true逻辑标签来识别相应的数据类型，然后来做出相应的输出操作。

Html代码 

```html
<%@ page contentType="text/html; charset=UTF-8" language="java" import="test.*,java.util.*"%>  
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<%  
    TestBean bean = null;  
    Map mapbeans = new HashMap();//定义一个map，值可能是TestBean类型也可能是另外一个map  
    bean = new TestBean();  
    bean.setId("uuid");  
    bean.setName("多多");  
    mapbeans.put(bean.getId(),bean);//添加一个类型为TestBean的元素  
      
    bean = new TestBean();  
    bean.setId("uuid1");  
    bean.setName("多多1");  
    mapbeans.put(bean.getId(),bean);//添加一个类型为TestBean的元素  
    bean = new TestBean();  
    bean.setId("uuid2");  
    bean.setName("多多2");  
    mapbeans.put(bean.getId(),bean);//添加一个类型为TestBean的元素  
  
        Map<String,String> mapstrings = new HashMap<String,String>();  
    mapstrings.put("id1","多多1");  
    mapstrings.put("id2","多多2");  
    mapstrings.put("id3","多多3");  
    mapstrings.put("id4","多多4");  
    mapbeans.put("inner", mapstrings);//添加一个类型为Map的元素  
  
    request.setAttribute("mapbeans",mapbeans);    
%>  
  
<html>  
<head>  
<title>测试获取map信息实例</title>  
</head>  
<body>  
    <table>  
        <h3>map<String,po>对象信息迭代功能,采用map标签输出map中的元素信息</h3>  
        <pg:map requestKey="mapbeans">  
            <pg:true typeof="<%=test.TestBean.class %>">  
            <tr >  
                <td>  
                    mapkey:<pg:mapkey/>  
                </td>   
                <td>  
                    id:<pg:cell colName="id" />  
                </td>   
                <td>  
                    name:<pg:cell colName="name" />  
                </td>   
            </tr>  
            </pg:true>  
            <pg:true typeof="java.util.Map">  
            <tr >  
                <td><table>  
                <pg:map>  
                    <tr>  
                        <td>outer mapkey use expression：<pg:cell expression="{0.mapkey}" /></td>   
                        <td>outer mapkey ：<pg:mapkey index="0"/> , inner mapkey:<pg:mapkey/></td>   
                        <td>  
                            inner value:<pg:cell/>  
                        </td>   
                    </tr>   
                      
                </pg:map>  
                </table></td>  
             </tr>      
            </pg:true>  
        </pg:map>       
    </table>  
</body>  
</html>  
```

这个例子中我们处理演示类型匹配操作功能外，还演示了嵌套的map标签中通过带索引号内置变量{0.mapkey}获取外围map标签中的mapkey的两种等价方法：

通过内置变量表达式：

Xml代码

```xml
<pg:cell expression="{0.mapkey}" />
```

通过mapkey标签带嵌套索引号index属性的方法：

Xml代码

```xml
<pg:mapkey index="0"/>  
```

 索引号的规则：最外层嵌套为0，次外层为1，依次类推，list，map，beaninfo可以混合使用，索引号规则可以同时对这些混合嵌套使用的标签起作用。

bboss标签库表达式及标签内置变量的使用文档请参考：

http://yin-bp.iteye.com/blog/2022430  