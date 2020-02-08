### bboss中的map标签结合list标签/cell标签展示复杂数据结构案例

一.概述

首先介绍一下map、mapkey标签

map标签有两个作用：

1.用来迭代展示map中的所有对象详细信息，map标签展示的数据可以从request，session，pagecontext中获取，也可以嵌套在list，beaninfo，map标签中使用。

2.用来输出map中的某个值

mapkey标签在map标签中使用，用来输出map中的key值。

map中value可以为各种复杂的对象类型，value可以为普通的bean对象，基础数据类型，list/map/数组等容器对象。  

map包括以下主要属性：

requestKey:指定map对象存储在request中的key名称

colName：map对象来源于bean属性名称

keycell：只展示map中的一个数据，指定map所对应的外围容器中当前记录对象作为key值，map标签然后获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

key：只展示map中的一个数据，指定map数据key，从map中获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

keycolName：只展示map中的一个数据，指定map所对应的外围容器中对象中的属性名称，key的由该属性对应的值指定，map标签然后获取key对应的value，map标签中内置的cell标签、逻辑标签、list标签都可以展示value对象中包含的数据

二、案例描述如下

map的类型为Map<String,List**<Exampbean**>>,也就是key类型为String，我们会将所有的key按顺序放置到一个List**<String**>中,然后再list标签遍历key，然后用遍历到得key获取map中的List**<Exampbean**>集合，然后再用list标签遍历输出List**<Exampbean**>中对象属性；也会直接遍历输出map中的数据，可以看看有序的map和无序的map输出直接的区别。

三、案例实现  

1.首先在控制器方法中构造我们需要的数据结构：

Java代码

```java
public String listmap(ModelMap model)  
    {  
        //将所有的key放到nameList中  
        List<String> nameList = new ArrayList<String>();  
        nameList.add("handlerModel");  
        nameList.add("applyUnionModel");  
        nameList.add("billLoanModel");  
        nameList.add("loanPayModel");  
        nameList.add("budgetModel");  
        nameList.add("outgoModel");  
        nameList.add("billItemModel");  
        nameList.add("billAttachment");  
        nameList.add("billSapModel");  
        //构造每个key对应的List<ExampleBean>数据并放到Map<String,List<ExampleBean>> billDataMap变量中  
        Map<String,List<ExampleBean>> billDataMap = new HashMap<String,List<ExampleBean>>();  
        List<ExampleBean> datas = new ArrayList<ExampleBean>();//定义List<ExampleBean>集合，为了示例的简单，每个集合中只放一个ExampleBean类型对象  
        ExampleBean bean = new ExampleBean();  
        bean.setName("handlerModel");  
        bean.setSex("男");  
        datas.add(bean);  
        billDataMap.put("handlerModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("applyUnionModel");  
        bean.setSex("女");  
        datas.add(bean);  
        billDataMap.put("applyUnionModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("billLoanModel");  
        bean.setSex("男");  
        datas.add(bean);  
        billDataMap.put("billLoanModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("loanPayModel");  
        bean.setSex("女");  
        datas.add(bean);  
        billDataMap.put("loanPayModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("budgetModel");  
        bean.setSex("男");  
        datas.add(bean);  
        billDataMap.put("budgetModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("outgoModel");  
        bean.setSex("女");  
        datas.add(bean);  
        billDataMap.put("outgoModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("billItemModel");  
        bean.setSex("女");  
        datas.add(bean);  
        billDataMap.put("billItemModel",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("billAttachment");  
        bean.setSex("男");  
        datas.add(bean);  
        billDataMap.put("billAttachment",datas);//put数据到map中  
        datas = new ArrayList<ExampleBean>();  
        bean = new ExampleBean();  
        bean.setName("billSapModel");  
        bean.setSex("未知");  
        datas.add(bean);  
        billDataMap.put("billSapModel",datas);//put数据到map中    
        model.addAttribute("nameList", nameList);//将名称列表放到控制器数据容器中    
        model.addAttribute("billDataMap", billDataMap);//将map数据放到控制器数据容器中  
        return "path:listmap";//跳转到数据展示页面  
    }  
```

2.控制器配置文件：

Xml代码

```xml
<properties>  
    <property name="/examples/*.page"  
        path:listmap="/examples/listmap.jsp"  
        class="org.frameworkset.mvc.HelloWord">  
    </property>  
</properties>  
```

3.jsp实现代码,包含三部分内容：

1.直接通过list标签输出nameList中的所有key

2.根据list中key的顺序有序遍历并输出map中list中保持的bean的属性值

3.直接遍历并输出map中list中保持的bean的属性值

详细代码如下：

Java代码

```java
<%@ page language="java" contentType="text/html; charset=utf-8"  
    pageEncoding="utf-8"%>  
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>  
  
            <div class="detail">  
                <div class="tab">  
                1.直接通过list标签输出nameList中的所有key  
                    <ul id="tabul">  
                        <pg:list requestKey="nameList">  
                            <li class="current"><a href="javascript:void(0);"><pg:cell /></a></li>  
                        </pg:list>  
                    </ul>  
                    <div class="tabdiv">  
                        <span></span>  
                    </div>  
                </div>  
                <div class="pannle">  
                2.根据list中key的顺序有序遍历并输出map中list中保持的bean的属性值  
                <ol>  
                    <pg:list requestKey="nameList">  
                          
                            <pg:equal value="handlerModel"><!-- 经办列表，不同的值遍历输出的信息不一样 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>                                                
                                            </ul>  
                                    </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="applyUnionModel"><!-- 关联申请 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                    </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="billLoanModel"><!-- 借款列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="loanPayModel"><!-- 冲账列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="budgetModel"><!-- 预算列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="outgoModel"><!-- 分摊列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="billItemModel"><!-- 收款列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="billAttachment"><!-- 附件列表 -->  
                                <pg:map requestKey="billDataMap" keycell="true"><!-- 获取当前key对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                        </pg:list></li>  
                                </pg:map>  
                            </pg:equal>  
                            <pg:equal value="billSapModel"><!-- SAP列表信息 演示key属性，通过key属性直接获取map中的list值-->  
                                <pg:map requestKey="billDataMap" key="billSapModel"><!-- 直接指定key=billSapModel，获取对应的list进行展示 -->  
                                    <li class="pannelol"><span></span>   
                                    <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                            <ul>  
                                                <li><label>名称：</label><pg:cell colName="name" /></li>  
                                                <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                            </ul>  
                                    </pg:list></li>  
                                </pg:map>  
                                  
                                  
                                  
                            </pg:equal>  
                          
                    </pg:list>  
                    </ol>  
                </div>  
                <div class="pannle">  
                3.直接遍历并输出map中list中保持的bean的属性值  
                <ol>  
                    <pg:map requestKey="billDataMap">  
                        <li class="pannelol"><span></span>   
                        <pg:list><!-- 直接遍历输出map中存储的list,同理也可以直接遍历输出map中存储的map和数组 -->  
                                <ul>  
                                    <li><label>名称：</label><pg:cell colName="name" /></li>  
                                    <li><label>性别：</label><pg:cell colName="sex" /></li>    
                                </ul>  
                        </pg:list></li>  
                    </pg:map>  
                </ol>  
                </div>  
            </div>  
```

再举个keycolName属性使用的例子：

<pg:list requestKey="nameList">

<pg:map requestKey="billDataMap" keycolName="name">

对应的key：<pg:mapkey/>

对应的value：<pg:cell/>

**</pg:map**>

**</pg:list**>

这个例子的含义是，根据list中对象属性name获取map 对象billDataMap中和name对应的值并输出这个key和value

四、总结

bboss标签库提供了一系列的数据展示标签、逻辑标签、国际化标签，可以非常方便地在jsp页面中输出各种复杂的数据结构，本文着重介绍了map标签如何与list、cell标签组合实现map数据的有序输出，展示了bboss标签库的独特数据展示能力。

更加详细介绍bboss标签库参考文档如下：

[bbossgroups标签使用大全](http://yin-bp.iteye.com/blog/1136924) 

[bbossgroups标签库使用大全（续）](http://yin-bp.iteye.com/blog/1137674) 

