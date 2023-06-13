### bboss mvc控制器方法响应报文注解ResponseBody使用说明

@ResponseBody注解被应用于控制器方法的响应值上，可以为应用程序丰富便捷的功能，下面分别说明。

**1.将字符串作为响应报文返回到客户端**

Java代码

```java
public @ResponseBody  String deleteRequester(  String id)  
```

可以在bboss-mvc.xml配置中全局指定响应String报文的字符编码：

<property class="org.frameworkset.http.converter.StringHttpMessageConverter" 

**f:responseCharset="UTF-8"**/>

**2.将对象作为json报文响应报文返回到客户端**

Java代码 

```java
public @ResponseBody(datatype = "json")  List<POBean> getRequesters(  String condition)  
```

bboss mvc可以将各种对象转换为json格式报文返回给客户端

**3.将对象作为jsonp报文响应报文返回到客户端**

Java代码

```java
public @ResponseBody(datatype = "jsonp")  List<POBean> getRequesters(  String condition)
```

bboss mvc可以将各种对象转换为jsonp格式报文返回给客户端，jsonp报文与json报文类似，都是响应json格式的报文，区别就是jsonp报文方法可以接受跨站请求，而json报文方法不可以。

**4.注解File对象提供文件下载功能**

Java代码

```java
public @ResponseBody File downFile( String fileid)  
```

**5.注解Resource类型提供资源下载功能**

包括以下类型：

ClassPathResource -- 适用于应classpath下面的各种资源

ServletContextResource --适用于web应用根目录及子目录下的各种资源

FileSystemResource --适用于文件系统中各种文件资源

UrlResource --适用于url连接对应各种资源

ByteArrayResource--适用于二进制资源

例如：

Java代码

```java
public @ResponseBody  
    Resource exportExeclTemplate() throws Exception {  
          
        String fileName = "com/s/mms/background/action/exceldata.xls";  
          
        ClassPathResource classpath = new ClassPathResource(fileName);  
        return classpath;  
    }  
```

**6.注解FileBlob对象，实现文件、url资源的下载和浏览功能**

**下载:**FileBlob对象主要是用来直接下载Blob对象和InputStream流对象，同时可以指定一个下载文件名，实例如下：

Java代码 

```java
public @ResponseBody  
    FileBlob exportExeclTemplate() throws Exception {  
          
        String fileName = "com/s/mms/background/action/exceldata.xls";  
        FileBlob fb = new FileBlob ("exceldata.xls",new FileInputstream(new File(fileName)))//下载文件流  
                FileBlob fb = new FileBlob ("exceldata.xls",Blob对象) //实现对数据库blob对象的下载功能  
                FileBlob fb = new FileBlob ("test.xml",new URL("http://localhost:8080/bboss/test.xml"));//下载url地址对应的资源  
        return fb;  
    }  
```

**浏览:**通过FileBlob对象实现资源的浏览功能,实现浏览功能非常简单，设置FileBlob的rendtype属性值为FileBlob.BROWSER即可，例如：

Java代码

```java
public @ResponseBody  
    FileBlob exportExeclTemplate() throws Exception {  
          
        String fileName = "com/s/mms/background/action/exceldata.xls";  
        FileBlob fb = new FileBlob ("exceldata.xls",new FileInputstream(new File(fileName)))//下载文件流  
                fb.setRendtype(FileBlob.BROWSER)  
        return fb;  
    }  
```

@ResponseBody注解主要就这么多功能了，以后有更好的特性再加上去。