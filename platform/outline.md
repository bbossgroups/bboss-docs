### bboss内容管理outline标签嵌套使用方法示例

bboss内容管理outline标签可以用来循环输出文档列表和频道导航列表，本文介绍在outline标签在生成一级导航以及二级导航列表的在内容模板中的用法（嵌套层级数不限）。

嵌套二级导航用法如下：

Xml代码 

```xml
<cms:outline channel="0" datatype="channelhomepage">  
            <td width="240" align="center" valign="middle"><a  
                href="<cms:url/>" target="_blank"> <cms:cell colName="name" /></a>  
                <table>  
<!--二级导航开始，通过colName属性指定了二级导航的父亲导航名称-->  
                    <cms:outline colName="displayName" datatype="channelhomepage">  
                        <tr>  
                            <td width="240" align="center" valign="middle"><a  
                                href="<cms:url/>" target="_blank"> <cms:cell colName="name"  
/></a></td>  
                        </tr>  
                    </cms:outline>  
<!--二级导航结束-->  
                </table></td>  
        </cms:outline>  

```

同样还可以使用三级嵌套导航，样式如下：

Xml代码 

```xml
<cms:outline channel="0" datatype="channelhomepage">  
    <a href="<cms:url/>" target="_blank"> <cms:cell colName="name" /></a>  
                  
<!--二级导航开始，通过colName属性指定了二级导航的父亲导航名称-->  
                    <cms:outline colName="displayName" datatype="channelhomepage">  
            <a href="<cms:url/>" target="_blank"> <cms:cell colName="name"  
/></a>  
        <!--三级导航开始，通过colName属性指定了二级导航的父亲导航名称-->  
                    <cms:outline colName="displayName" datatype="channelhomepage">  
            <a href="<cms:url/>" target="_blank"> <cms:cell colName="name"  
/></a>  
                    </cms:outline>  
<!--三级级导航结束-->              
</cms:outline>  
<!--二级导航结束-->  
                  
        </cms:outline>  
```

