### bboss mvc ajax响应输出中文乱码解决方法

对于bboss mvc ajax请求响应出现的中文乱码问题，怎么解决？解决办法有两个，一个是直接在bboss-mvc.xml中的字符串转换插件StringHttpMessageConverter上通过responseCharset属性全局指定响应字符编码集，例如UTF-8或者GBK：

Xml代码

```xml
<property class="org.frameworkset.http.converter.StringHttpMessageConverter" f:responseCharset="UTF-8"/>  
```

Xml代码

```xml
<property class="org.frameworkset.http.converter.StringHttpMessageConverter" f:responseCharset="GBK"/> 
```

具体使用何种字符集取决于项目中采用的字符集。

另外一种解决办法就是通过返回参数注解ResponseBody的charset 属性单独对请求方法的响应字符串进行编码,例如：

Java代码

```java
public @ResponseBody(charset = "UTF-8")  
    String sayHelloEnum(@RequestParam(name = "sex") SexType type)  
```

bboss mvc基础配置请参考文档：
http://yin-bp.iteye.com/blog/1139608