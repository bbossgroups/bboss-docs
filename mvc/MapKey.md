### bboss mvc参数绑定注解MapKey使用说明

bboss mvc参数绑定注解MapKey主要具备以下三个功能：
1.用于注解控制器方法map<key,po>类型参数，map<key,po>类型参数主要用来把表单中的多条记录转换为po对象，并以MapKey注解指定的value属性对应的参数值作为key将po对象put到map对象中，以下是一个具体的实例：

控制方法

Java代码

```java
public String sayHelloBeanMap(@RequestParam(name = "name") String yourname,  
            @MapKey(value="name") Map<String, ExampleBean> mapBeans, ModelMap model)  
    {  
  
        model.addAttribute("sayHelloBeanMap", mapBeans);  
        return "path:sayHello";  
    }  
```

@MapKey(value="name") Map<String, ExampleBean> mapBeans中mapkey注解的value="name"指定了将使用name参数作为map的key值来存放ExampleBean对象。前端表单维护了多条记录，这些记录将作为ExampleBean对象以name参数的值作为key放到mapBeans中。

2.用于注解控制器方法map类型参数，指定表单中要放入map中的参数的名称模式，只有符合这个模式的参数才会被放入map中，下面是一个实例：

Java代码

```java
public String sayHelloStringMapWithFilter(@MapKey(pattern="pre.cc.*") Map params,  
            ModelMap model)  
    {  
  
        model.addAttribute("sayHelloStringMapWithFilter", params);  
        return "path:sayHello";  
    }  
```

@MapKey(pattern="pre.cc.*") Map params前的MapKey注解中的属性pattern="pre.cc.*"指定了要放入参数params中的参数名称模式pre.cc.*，也就是指定了以pre.cc.开头的参数将会放入到map中。如果没有通过mapkey注解指定pattern那么前端提交的所有参数将被放入map中，例如：

Java代码

```java
public String sayHelloStringMap( Map params,  
            ModelMap model)  
    {  
  
        model.addAttribute("sayHelloStringMap", params);  
        return "path:sayHello";  
    }  
```

3.用于注解bean po对象中map类型参数，指定表单中要放入map中的参数的名称模式，只有符合这个模式的参数才会被放入map中，下面是一个实例：

Java代码

```java
public class ExampleBean  
{  
    @MapKey(pattern="pre.cc.*")   
        private Map params;  
        。。。。  
}  
```

@MapKey(pattern="pre.cc.*") private Map params前的MapKey注解中的属性pattern="pre.cc.*"指定了要放入参数params中的参数名称模式pre.cc.*，也就是指定了以pre.cc.开头的参数将会放入到map中。如果没有通过mapkey注解指定pattern那么前端提交的所有参数将被放入map中，例如：

Java代码

```java
public class ExampleBean  
{  
    private Map params;  
        。。。。  
}  
```

pattern模式的使用示例：

pre.cc.*

pre.*.cc

*.cc.*

其中的*号表示匹配任意多个字符。

更多详细信息请参考bboss mvc的参数绑定测试用例：

[HelloWord.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/3e4d674e1b2cc7b7c02782304a2fb4c4e2172794/bboss-mvc/test/org/frameworkset/mvc/HelloWord.java)

[hello.jsp](https://github.com/bbossgroups/bbossgroups-3.5/blob/3e4d674e1b2cc7b7c02782304a2fb4c4e2172794/bboss-mvc/WebRoot/jsp/examples/hello.jsp)