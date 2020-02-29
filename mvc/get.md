### bboss mvc处理ajax get方法中文乱码方式纪实

bboss mvc处理ajax get方法中文乱码方式纪实：

尹标平(122054810)  

关于胡雅辉同学所提ajax get方式提交中文参数乱码问题解决办法：

Java代码 

```java
$.ajax({  
                    url:'${pageContext.request.contextPath}/utf8/generalAjaxGet.page',  
                    contentType : "application/x-www-form-urlencoded",      
                    type:'get',  
                    dataType:'json',  
                    data:{  
                            id:id,  
                            name:encodeURIComponent(name),  
                            remark:encodeURIComponent(remark)  
                        },  
                    success:function(json){  
                        alert(json.data);  
                    }     
                });  
```

在jsp页面的js函数中，对包含中文的name，remark参数采用encodeURIComponent函数编码，例如：remark:encodeURIComponent(remark)

服务器端SimpleEntity对象中的属性name和remark分别添加@RequestParam注解，并指定decodeCharset属性为UTF-8：

Java代码 

```java
@RequestParam(decodeCharset="UTF-8")  
    private String name;  
    @RequestParam(decodeCharset="UTF-8")  
    private String remark;  
```

问题即可解决，目前只想到这个办法，至于其他方法暂时没有想到

尹标平(122054810) 

同时服务器端控制器方法要改为,这样数据到客户端后就不会有乱码：

Java代码

```java
public @ResponseBody AjaxResponseBean generalAjaxGet(SimplEntity entity, HttpServletRequest request,HttpServletResponse response){  
        AjaxResponseBean ajaxResponseBean=new AjaxResponseBean();  
        ajaxResponseBean.setStatus("success");  
        try {  
            ajaxResponseBean.setData(entity);  
  
          
        } catch (Exception e) {  
            ajaxResponseBean.setStatus("error");  
            ajaxResponseBean.setData(e.getMessage());  
        }  
        return ajaxResponseBean;  
    }  
```

这样MVC框架的Json转换插件自动会把数据转换为json格式响应到客户端，客户端获取json结果数据的最终方式为：

Java代码

```java
$.ajax({  
                    url:'${pageContext.request.contextPath}/utf8/generalAjaxGet.page',  
                    contentType : "application/x-www-form-urlencoded",      
                    type:'get',  
                    dataType:'json',  
                    data:{  
                            id:id,  
                            name:encodeURIComponent(name),  
                            remark:encodeURIComponent(remark)  
                        },  
                    success:function(json){  
                        alert(json.data.name);  
                        alert(json.data.remark);  
                    }     
                });  
```

尹标平(122054810)  

同时要确保bboss-mvc的httpMessageConverters中有以下配置项：

Java代码 

```java
<property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter"/>  
```

尹标平 共享文件 1 个 

  utf8.zip

下载 | 查看全部

尹标平(122054810)  

改造后的程序在共享文件中的uft8.zip,可以在共享区下载

尹标平(122054810)  

或者到以下地址下载：
http://www.bbossgroups.com/file/download.htm?fileName=utf8.zip