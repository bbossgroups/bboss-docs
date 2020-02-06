### bboss mvc接收和响应xml格式数据的方法

本文介绍bboss mvc接收和响应xml格式数据的方法

1.首先需要在bboss-mvc.xml文件中配置bboss mvc处理xml报文的插件XMLHttpMessageConverte：

property name="httpMessageConverters"
     list

  property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter"/
     property

class="**org.frameworkset.http.converter.XMLHttpMessageConverter**"/
     property class="org.frameworkset.http.converter.StringHttpMessageConverter"/

  /list      
     /property

org.frameworkset.http.converter.XMLHttpMessageConverter就是bboss mvc处理xml报文的插件实现类，可以通过在请求参数前加RequestBody 注解进行获取请求体重的xml报文，响应xml可以通过在返回值前面添加@ResponseBody(datatype="xml")注解 实现；xml处理插件可以将请求体中的xml报文转换为字符串、po对象类型的数据，也可以将返回的po对象或者list等集合对象转换为xml响应报文返回。

2.接收xml和响应xml的方法：

接收和响应字符串xml报文

Java代码

```java
public @ResponseBody(datatype="xml") String echo(@RequestBody String xml)  
    {  
        System.out.println(xml);  
        return xml;  
    }  
```

接收和响应字符串po对象报文

Java代码

```java
public @ResponseBody(datatype="xml") List<PO> echo(@RequestBody PO xml)  
    {  
                  List<PO> ret = new ArrayList<PO>();  
ret.add(xml);  
        return ret ;  
    }  
```

bboss mvc采用xstream和JAXB两种方式来实现xml和对象相互转，如果po对象类添加了注解javax.xml.bind.annotation.XmlRootElement，那么就采用JAXB来处理xml和对象间的转换，否则采用xstream来处理xml和对象间的转换。

XMLHttpMessageConverter插件接收application/xml,text/xml类型的请求报文，同时以application/xml类型响应xml报文。