### bboss标签库list/map标签循环变量var属性使用说明

[bboss v4.10.2](https://github.com/bbossgroups/bboss/releases)及以上版本为list、map两个标签增加几个定义el变量名称的属性，分别为：

list标签新增以下属性，以便在el表达式中进行相关处理：

var：指定存放循环迭代中当前对象变量名称属性

loopvar：指定存放循环索引变量名称属性

rowcountvar：指定存放总记录数变量名称属性，分页时可以使用

pagesizevar：指定存放分页当前页面记录变量名称属性，如果是列表则变量中存放的是集合的记录数

offsetvar：指定存放分页偏移offset值变量名称属性，分页时可以使用，存放分页记录起点位置值

map标签新增以下属性，以便在el表达式中进行相关处理：

var：指定存放循环迭代中当前对象变量名称属性

loopvar：指定存放循环索引变量名称属性

rowcountvar：指定存放map中记录数变量名称属性

mapkeyvar：存放map集合中当前key值变量名称属性

使用示例：
list标签：

Html代码

```html
<pg:list requestKey="users" var="user">  
    输出用户姓名：${user.name}  
    输出用户性别：${user.sex}  
    输出用户年龄：${user.age}  
</pg:list> 
```

map标签：

Html代码

```html
<pg:map requestKey="users" var="user">  
    输出用户姓名：${user.name}  
    输出用户性别：${user.sex}  
    输出用户年龄：${user.age}  
</pg:map>  
```

稍微复杂一点的示例：

Html代码

```html
<pg:pager scope="request"  data="sessionList" desc="true" isList="false" containerid="sessionContainer">  
        <pg:param name="sessionid"/>  
        <pg:param name="appkey"/>  
        <pg:param name="referip"/>  
        <pg:param name="createtime_start"/>  
        <pg:param name="createtime_end"/>  
        <pg:param name="host"/>  
        <pg:param name="validate"/>  
        <pg:list requestKey="monitorAttributes" var="attribute">  
            <pg:param name="${attribute.name}"/>  
        </pg:list>  
.....  
  
</pg:pager>  
```

list标签综合示例：

Html代码

```html
<pg:list requestKey="boms"  loopvar="i" rowcountvar="total" pagesizevar="pagesize" offsetvar="offset" var="bom">  
    i:${i } ,total:${total },pagesize:${pagesize },offset:${offset },bom:${bom.bm }  
</pg:list>  
```

map标签综合示例：

Html代码

```html
<pg:map actual='${sessionInfo.attributes}' var="data" rowcountvar="total" mapkeyvar="key" loopvar="i">  
                                    <i:${i },key:${key },total:${total },data:${data }  
</pg:map>  
```

逻辑标签借助el表达式进行组合判断：

Html代码

```html
<pg:list requestKey="users" var="user">  
    判断用户状态为1或者2输出有效：  
<pg:true actual="${user.status == 1 or user.status == 2}">  
有效  
</pg:true>  
        判断用户状态为1或者2输出有效，否则出入无效：  
<pg:true actual="${user.status == 1 or user.status == 2}" evalbody="true">  
<pg:yes>有效</pg:yes><pg:no>有效</pg:no>  
</pg:true>  
       或者直接这么写：  
${user.status==1 or user.status==2?'有效':'无效'}  
</pg:list>  
```

上述功能类似于in标签的功能：

Html代码

```html
<pg:list requestKey="users" >  
    判断用户状态为1或者2输出有效：  
<pg:in colName="status" value="1,2">  
有效</pg:in>  
  
        判断用户状态为1或者2输出有效，否则出入无效：  
<pg:in colName="status" value="1,2" evalbody="true">   
<pg:yes>有效</pg:yes><pg:no>有效</pg:no>  
</pg:in>  
         
</pg:list>
```

更多标签库使用文档，请访问：http://yin-bp.iteye.com/category/69334
$$

$$