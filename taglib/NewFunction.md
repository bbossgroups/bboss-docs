### bbossgroups标签库使用大全（续三）-新增功能特性详解

bbossgroups标签库使用大全（续三）-bboss最近新增三个功能特性，本文详细介绍之。

**1.部分逻辑标签（equal,notequal,upper,lower,upperequal,lowerequal,in,notin）增加length属性**

Html代码

```html
<!--   
        用于设置获取集合，字符串长度的变量名称，可对Collection,ListInfo,String,Map，Array类型对象求长度操作  
        
     length属性值带有前缀cell,request,session,pagecontext:  
     cell 从对象属性中获取属性值得长度  
     request 从request属性中获取属性值得长度  
     parameter 从request参数中获取属性值得长度  
     session 从session属性中获取属性值得长度  
     pagecontext 从pagecontext属性中获取属性值得长度  
     默认从cell中获取对象属性  
     length属性适用于equal,notequal,upper,lower,upperequal,lowerequal,  
     in,notin标签  
         -->  
        <attribute>  
            <name>length</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
```

**2.list标签增加position,start属性，用来直接展示对应位置的对象**

position属性标识只输出集合中postion位置对应的对象数据，如果postion超出集合大小，则抛出异常
start属性指示从start开始的位置输出集合中的后续元素，忽略start之前的数据,start小于0从第一个位置开始迭代数据，start大于集合大小则不输出任何数据。

上述两个改造使用实例：

Html代码 

```html
<pg:equal length="request:rejectList" value="1">  
          
        if(confirm("确实要驳回"))//驳回  
            {  
                <pg:list requestKey="rejectList" position="0">  
                    $("#approveForm #pass").val("<pg:cell colName='nodeCode'/>");  
                </pg:list>                                      
                $("#approveForm").submit();  
            }  
            return false;  
              
    </pg:equal>  
<pg:list requestKey="rejectList" start="2">  
                    $("#approveForm #pass").val("<pg:cell colName='nodeCode'/>");  
                </pg:list>  
```

**3.增加size标签，用来输出Collection,ListInfo,String,Map，Array类型对象求长度**

Java代码

```java
<pg:size requestKey="rejectList"/>  
```

Java代码 

```java
<pg:size requestKey="rejectList" increament="1"/>  
```

increament属性含义，输出size+increament的和，可以为负数，也可以为正数。