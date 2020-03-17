### bboss标签库使用大全-逻辑标签使用介绍

bbossgroups标签库使用大全（续），接上篇[《bbossgroups标签库使用大全》](http://yin-bp.iteye.com/blog/1136924)，本片重点介绍逻辑标签的使用。同样在使用的时候需要在jsp页头中倒入标签定义文件tld：

Html代码

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
<%@ taglib uri="/WEB-INF/treetag.tld" prefix="tree" %>  
<%@ taglib uri="/WEB-INF/commontag.tld" prefix="common"%> 
```

bbossgroups中主要包含以下逻辑标签

equal,notequal,null,notnull, equalandlower, equalandupper,
upper, lower, in, notin,match,contain,notmatch,notcontain，empty，notempty ，true,false,startwith,notstartwith,endwith,notendwith,case,other,yes,no

这些逻辑标签基本上都支持下述数据类型的比较操作：

1.int/Integer

2.double/Double

3.long/Long

4.String

5.Short

6.java.util.Date/Timestamp/java.sql.Date

7.float/Float

如何使用bboss逻辑标签实现if-else和if-elseif-else功能请参考文档：

[bboss逻辑标签实现if-else以及if-else if-else条件判断功能](http://yin-bp.iteye.com/blog/2160572)

**1.equal、notequal标签的使用**

在list，map，beaninfo标签中使用，对对象中的属性进行相等和不相等逻辑判断：

判断属性sex的值是否等于1，是则输出男，否则忽略标签体中的内容

<pg:equal colName="sex" value="1">男</pg:equal>

判断属性sex的值是否不等于1，不等于则输出女，否则忽略标签体中的内容

<pg:notequal colName="sex" value="1">女</pg:notequal>

直接将el表达式的值和value属性的值进行比较：

判断request参数sex的值是否等于1，是则输出男，否则忽略标签体中的内容

<pg:equal actual="${param.sex}" value="1">男</pg:equal>

判断request参数的值是否不等于1，不等于则输出女，否则忽略标签体中的内容

<pg:notequal actual="${paramsex}" value="1">女</pg:notequal>

直接判断同一两个属性是否相等：

<pg:equal expression="{sex}" expressionValue="{sexaaaa}">男</pg:equal>

判断行号是否是最后一行

<pg:equal expression="{rowid}" expressionValue="{rowcount} - 1">男</pg:equal>

判断行号是否是给定的行号

<pg:equal expression="{rowid}" value="1">男</pg:equal>

判断行号是否是偶数行

<pg:equal expression="{rowid}%2" value="0">男</pg:equal>  

equal和notequal标签可以嵌套在其他的逻辑标签中使用。

字符串忽略大小写比较-ignoreCase属性使用方法：

ignoreCase作用： 字符串比较是否忽略大小写：true 忽略，false不忽略，默认值值false

<pg:equal colName="dbname" value="mysql" ignoreCase="true">mysql</pg:equal>

<pg:notequal colName="dbname" value="mysql" ignoreCase="true">oracle</pg:notequal>

**2.null,notnull标签的使用**

在list，map，beaninfo标签中使用，对对象中的属性进行null和非null逻辑判断：

判断属性sex的值是否为null，是则输出男，否则忽略标签体中的内容

<pg:null colName="sex" >男</pg:null>

判断属性sex的值是否不为null，不为null则输出女，否则忽略标签体中的内容

<pg:notnull colName="sex" >女</pg:notnull>  

直接将el表达式的值进行判断：

判断request参数sex的值是否为null，是则输出男，否则忽略标签体中的内容

<pg:null actual="${param.sex}" >男</pg:null>

判断request参数的值是否不等于null，不等于null则输出女，否则忽略标签体中的内容

<pg:notnull actual="${paramsex}" >女</pg:notnull>

null和notnull标签可以嵌套在其他的逻辑标签中使用。  

**3.empty，notempty 标签的使用**

empty的含义为：字符串为null或者"",数组对象、容器对象、ListInfo（bboss分页数据封装类）对象为null或者size为0.notempty则相反。

在list，map，beaninfo标签中使用，对对象中的属性进行empty和非empty逻辑判断：

判断属性sex的值是否为empty，是则输出男，否则忽略标签体中的内容

<pg:empty colName="sex" >男</pg:empty>

判断属性sex的值是否不为empty，不为empty则输出女，否则忽略标签体中的内容

<pg:notempty colName="sex" >女</pg:notempty>

直接将el表达式的值进行判断：

判断request参数sex的值是否为empty，是则输出男，否则忽略标签体中的内容

<pg:empty actual="${param.sex}" >男</pg:empty>

判断request参数的值是否不等于null，不等于null则输出女，否则忽略标签体中的内容

<pg:notempty actual="${paramsex}" >女</pg:notempty>

empty和notempty标签可以嵌套在其他的逻辑标签中使用。

**4.in, notin标签的使用**

in标签判定指定的值是否包含在几个值中间，notin的意义相反。

在list，map，beaninfo标签中使用，判定对象中的属性是否包含在几个值中间，notin的意义相反：  

判断属性id的值是否为1,2,3,4,5中的一个数字，是则输出ddddd，否则忽略标签体中的内容

<pg:in colName="id" scope="1,2,3,4,5">ddddd</pg:in>

判断属性id的值是否不包含在1,2,3,4,5，不则输出ddddd，否则忽略标签体中的内容

<pg:notin colName="id" scope="1,2,3,4,5">ddddd</pg:notin>

直接将el表达式的值进行判断：

判断request参数sex的值是否1,2,3,4,5中的一个数字，是则输出ddddd，否则忽略标签体中的内容

<pg:in actual="${param.sex}" scope="1,2,3,4,5">男</pg:in>

判断request参数的值是否不包含在1,2,3,4,5，不则输出ddddd，否则忽略标签体中的内容

<pg:notin actual="${paramsex}" scope="1,2,3,4,5">女</pg:notin>

in和notin标签可以嵌套在其他的逻辑标签中使用。  

**5.contain, notcontain，match ，notmatch 标签的使用**

contain, notcontain标签可以实现以下功能：

  用来判断给定属性的值包含/不包含正则表达式pattern对应的字符串

  用来判断给定属性的值包含/不包含value对应的字符串

  用来判断给定属性的值包含/不包含expressionValue表达式对应的字符串

match ，notmatch 标签分别用来判断给定属性的值匹配/不匹配给定的正则表达式模式。

下面是具体的用法：

<pg:contain colName="table_id_name" pattern="[1-2]+">ddddd</pg:contain>

<pg:notcontain colName="table_id_name" pattern="[1-2]+">ddddd</pg:notcontain>

<pg:contain colName="table_id_name" value="table">ddddd</pg:contain>

<pg:notcontain colName="table_id_name" value="table">ddddd</pg:notcontain>

<pg:contain colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:contain>

<pg:notcontain colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:notcontain>

<pg:contain expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:contain>

<pg:notcontain expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:notcontain>

<pg:match colName="table_id_name" pattern="[1-2]+">ddddd</pg:match>

<pg:notmatch colName="table_id_name" pattern="[1-2]+">ddddd</pg:notmatch>

同样支持el表达式：

<pg:contain actual="${param.sex}" pattern="[1-2]+">ddddd</pg:contain>

<pg:notcontain actual="${param.sex}" pattern="[1-2]+">ddddd</pg:notcontain>

<pg:match actual="${param.sex}" pattern="[1-2]+">ddddd</pg:match>

<pg:notmatch actual="${param.sex}" pattern="[1-2]+">ddddd</pg:notmatch>

**6.一组大于/小于/大于等于/小于等于逻辑标签**

equalandlower, equalandupper,

upper, lower

**7.true/false逻辑标签**

判断变量fromwebseal是否为false，如果为false或者null则执行标签体中的代码

<pg:false actual="${fromwebseal}">

<a href="#" class="zhuxiao" onclick="logout()"><pg:message code="sany.pdp.module.logout"/></a>

</pg:false>

判断变量fromwebseal是否为true，如果为true或者非null或者不为字符串false则执行标签体中的代码

<pg:true actual="${fromwebseal}">

<a href="#" class="zhuxiao" onclick="logout()"><pg:message code="sany.pdp.module.logout"/></a>

</pg:true>

采用true/false标签的typeof属性可以方便地检测对应的数据类型，参考文档：

[bboss逻辑标签判断对象类型是否为给定的Class类型方法](http://yin-bp.iteye.com/blog/2128736)

这组标签的使用方法基本上和其他逻辑标签使用方法一致，这里不做过多的介绍了,更多的标签属性可以参考标签定义tld文件：[pager-taglib.tld](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/pager-taglib.tld)。

**8.startwith,notstartwith,endwith,notendwith**

startwith,notstartwith,endwith,notendwith是一组字符串操作，分别对应于String.startWith方法和String.endWith方法功能。具体使用方法如下：

<pg:startwith colName="table_id_name" value="table">ddddd</pg:startwith>

<pg:notstartwith colName="table_id_name" value="table">ddddd</pg:notstartwith>

<pg:startwith colName="table_id_name" value="table" offset="2">ddddd</pg:startwith>

<pg:notstartwith colName="table_id_name" value="table" offset="2">ddddd</pg:notstartwith>

<pg:startwith colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:startwith>

<pg:notstartwith colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:notstartwith>

<pg:startwith colName="table_id_name" expressionValue="{otherfiled}" offset="2">ddddd</pg:startwith>

<pg:notstartwith colName="table_id_name" expressionValue="{otherfiled}" offset="2">ddddd</pg:notstartwith>

<pg:startwith expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:startwith>

<pg:notstartwith expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:notstartwith>

<pg:endwith colName="table_id_name" value="table">ddddd</pg:endwith>

<pg:notendwith colName="table_id_name" value="table">ddddd</pg:notendwith>

<pg:endwith colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:endwith>

<pg:notendwith colName="table_id_name" expressionValue="{otherfiled}">ddddd</pg:notendwith>

<pg:endwith expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:endwith>

<pg:notendwith expression="{table_id_name}" expressionValue="{otherfiled}">ddddd</pg:notendwith>  