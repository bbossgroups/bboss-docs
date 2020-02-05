### bboss国际化标签小常识-基础数据国际化案例

一般所说的国际化都是指表示层页面显示信息的国际化，但是有些情况下，业务系统中的一些基础数据也需要做国际化，本文举一个简单的例子来说明bboss国际化标签如何实现基础数据的国际化。

bboss中使用message标签结合cell标签来实现基础数据的国际化，使用方法如下：

Java代码 

```java
<pg:list requestKey="appbom">  
<pg:cell colName="appname"/>  
  
<pg:message colName="devcountry"/>  
</pg:list> 
```

 devcountry属性代表了保存在数据记录字段devcountry中的一个国际化编码code，当系统语言环境发生变化时就从相应的国际化属性文件中获取对应语言的开发国家名称，例如

devcountry=zh时，中文语言环境<pg:message colName="devcountry"/>就会输出“中国”，英文语言环境<pg:message colName="devcountry"/>就会输出“china”，这样即可结合国际化属性文件和表字段code来实现业务基础数据的国际化。  