### bboss逻辑标签实现if-else以及if-else if-else条件判断功能介绍

采用bboss逻辑标签可以非常容易地实现if-else以及if-else if-else条件判断功能，相关的标签为case，other，yes，no以及其它[bboss逻辑标签](http://yin-bp.iteye.com/blog/1137674)。本文以两个简单的示例来说明上述功能。

**一、if-else功能**

相等的if-else条件判断：

Xml代码

```xml
<pg:equal actual="false" value="true" evalbody="true" >  
          
            <pg:yes>  
                yes,很好！  
            </pg:yes>  
            <pg:no>  
                no,很坏！  
            </pg:no>  
        </pg:equal>  
```

上述代码与下面代码段功能等价,区别是采用yes/no相结合的模式性能更好：

Xml代码

```xml
<pg:equal actual="false" value="true"  >  
                yes,很好！  
        </pg:equal>  
<pg:notequal actual="false" value="true"  >  
                no,很坏！  
        </pg:notequal>  
```

对比简单的相等匹配代码：

Xml代码 

```xml
<pg:equal actual="false" value="true"  >  
                yes,很好！  
        </pg:equal>  
```

这段简单的代码说明equal标签比较结果为true时直接执行equal标签体中的内容，否则不执行；而if-else判断功能时，equal标签指定了evalbody="true"属性，指示equal标签强制执行标签体语句，然后通过yes和no标签组合实现if-else功能，当比较结果为true时，执行yes标签体中内容，否则执行no标签中的内容。

上面的功能java中实现的写法：

Java代码 

```java
if(false == true)  
{  
   System.out.println("yes,很好！");  
}  
else  
{  
    System.out.println("yes,很坏！");  
}  
```

不相等的if-else条件判断：

Xml代码

```xml
<pg:notequal actual="false" value="true" evalbody="true" >  
          
            <pg:yes>  
                yes,很好！  
            </pg:yes>  
            <pg:no>  
                no,很坏！  
            </pg:no>  
        </pg:notequal>  
```

bboss所有的逻辑标签都可以使用evalbody属性，从而实现相应的if-else功能。

colName属性的使用方法如下：

Xml代码

```xml
<pg:notequal colName="name" value="duoduo" evalbody="true" >  
          
            <pg:yes>  
                yes,很好！  
            </pg:yes>  
            <pg:no>  
                no,很坏！  
            </pg:no>  
        </pg:notequal>  

```

上面的功能java中实现的写法：

Java代码

```java
if(name !=null && !name.equals("duoduo"))  
{  
   System.out.println("yes,很好！");  
}  
else  
{  
    System.out.println("yes,很坏！");  
}  
```

**二、if-elseif-else功能**

3个简单的相等判断实例-actual直接指定需要判断的值：

Xml代码

```xml
<pg:case actual="1">  
          
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
          
        <pg:case actual="2">  
          
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
          
        <pg:case actual="3">  
          
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

在case标签中可以内置其他所有逻辑标签，other标签放置在case的内嵌标签的最后面，当前面的标签都没有执行时，最终会执行other标签体重的内容。

看一个简单的cell colName的使用方法：

Xml代码

```xml
<pg:case colName="name">  
          
            <pg:equal value="1">  
                <pg:cell colName="firstName"/>  
            </pg:equal>  
            <pg:equal value="2">  
                <pg:cell colName="secondName"/>  
            </pg:equal>  
            <pg:other>  
                <pg:cell colName="otherName"/>  
            </pg:other>  
        </pg:case>      
```

上面的功能java中实现的写法：

Java代码

```java
if(name !=null && name.equals("1"))  
{  
      
}  
else if(name !=null && name.equals("2"))  
{  
       
}  
else   
{  
      
}  
```

在case标签中other标签是可选的，就好比if-elseif-else中最后的else是可选的一样,例如：

Xml代码

```xml
<pg:case colName="name">  
          
            <pg:equal value="1">  
                <pg:cell colName="firstName"/>  
            </pg:equal>  
            <pg:equal value="2">  
                <pg:cell colName="secondName"/>  
            </pg:equal>  
              
        </pg:case>          
```

