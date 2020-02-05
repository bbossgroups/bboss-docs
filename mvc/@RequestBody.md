### bboss mvc @RequestBody注解使用说明

@RequestBody可以将客户端请求报文体通过数据类型转换后绑定为mvc控制器方法参数，@RequestBody有一个datatype属性，用来指定请求报文的数据格式，目前支持json，xml，String三种类型。

@RequestBody的使用方法如下：

Java代码

```java
public void test(@RequestBody String data)//不指定dataype属性时，如果参数类型为String，则按String来处理  
public void test(@RequestBody PO data)//不指定dataype属性时，如果参数类型为普通java bean，则按json来序列化处理来处理  
public void test(@RequestBody(datatype="String") String data)  
public void test(@RequestBody(datatype="xml") PO data)//默认采用XSteam对请求报文绑定为po对象  
public void test(@RequestBody(datatype="json") PO data)//json请求报文绑定为po对象
```

