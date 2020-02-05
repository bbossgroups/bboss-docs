### bboss内容管理模板宏用法介绍

  本文介绍bboss内容管理模板宏的使用方法。

bboss内容管理模板宏是指将内容管理模板中的公共部分抽取出来放到一个独立的模板文件中，然后通过#include（模板宏路径）标记在首页模板、概览模板、细览模板以及一般页面中进行引用。下面先看一个子模板定义：

假设一个站点test，在站点模板目录/bestbride/_template下新建一个模板宏文件/head/test.html，内容为：

Xml代码

```xml
<li><a href="<cms:url site="jckj"/>">首页</a></li>  
              
              
            <cms:outline channel="0" datatype="channelhomepage" >  
                <cms:equal colName="name" value="${cur_channel.name}" evalbody="true">  
                    <cms:yes>  
                    <li><a href="#" ><cms:cell colName="name"/></a></li>  
                    </cms:yes>  
                    <cms:no>  
                    <li><a href="<cms:url/>" ><cms:cell colName="name"/></a></li>  
                    </cms:no>  
                </cms:equal>   
            </cms:outline>   
```

这个模板宏主要用来输出网站的一级导航菜单。

那么在首页模板index.html引用该模板宏的方法为，将以下语句放到模板的对应导航菜单位置即可：

#include(/head/test.html)

这里需要说明的是，如果在模板中需要引用css或者图片资源，那么引用必须借助cms:uri标签来达成，例如：

Xml代码

```xml
<img src="<cms:uri link="images/a.jpg"/>" />  
<link href="<cms:uri link="css/css.css"/>" rel="stylesheet" type="text/css" />  
```

