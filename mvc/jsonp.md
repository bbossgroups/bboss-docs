### bboss mvc结合jsonp实现跨站跨域应用间通讯功能介绍

本文介绍bboss mvc结合jsonp实现跨站跨域应用间通讯功能的使用方法和实现机制，切入主题。

bboss最新版本下载：

https://github.com/bbossgroups/bbossgroups-3.5

bboss mvc最新版本通过mvc json插件MappingJacksonHttpMessageConverter支持jsonp数据协议来实现跨站跨域应用之间的交互通讯，使用方法如下。

**1.服务器端实现**

服务器端部署在实例工程：bestpractice\demoproject中，服务端程序为/demoproject/src/org/frameworkset/mvc/HelloWord.java

服务端控制器方法通过制定注解@ResponseBody的datatype属性为jsonp来指示mvc框架需要将响应值转换为jsonp函数+json参数数据的合法JavaScript脚本返回到客户端, 以便实现跨站跨域请求交互，HelloWord.java提供了两个方法一个是应用于使用jquery的请求，一个应用于JavaScript标记请求

Java代码

```java
public @ResponseBody(datatype="jsonp") JsonpBean jsonpwithjquery()  
    {  
        JsonpBean jsonpbean = new JsonpBean();  
        jsonpbean.setPrice("91.42");  
        jsonpbean.setSymbol("IBM jquery jsonp");  
        return jsonpbean;  
  
    }  
    public @ResponseBody(datatype="jsonp") JsonpBean jsonp()  
    {  
        JsonpBean jsonpbean = new JsonpBean();  
        jsonpbean.setPrice("91.42");  
        jsonpbean.setSymbol("IBM");  
        return jsonpbean;  
    }  
```

分别对应请求地址为：

http://localhost:8081/demoproject/examples/jsonpwithjquery.page

http://localhost:8081/demoproject/examples/jsonp.page

**2.客户端实现**

客户端应用和服务器应用属于两个不同站点或者不同域名，客户端部署在bboss-mvc的web应用中,对应的访问地址为：http://localhost:8081/bboss-mvc/jsp/jsonp/testjsonp.jsptestjsonp.jsp相关的代码如下：

Js代码

```js
<!-- 普通的jsonp调用示例开始，定义跨域回调函数 -->  
    <script type="text/javascript">  
            function jsonpCallback(result)  
            {  
                alert("aaa:" + result.symbol);//弹出跨站 请求返回的json数据对象的symbol属性的值  
            }  
        </script>  
    <!-- 普通的jsonp调用示例，向另一个应用demoproject发起mvc请求，并指参数callback（参数名字可任意指定）指定回调函数jsonpCallback-->  
    <script type="text/javascript" src="http://localhost:8081/demoproject/examples/jsonp.page?jsonp_callback=jsonpCallback"></script>  
    <!-- 普通的jsonp调用示例结束-->  
      
    <!-- 采用jquery实现jsonp调用示例开始-->  
    <script src="<%=request.getContextPath() %>/include/jquery-1.4.2.min.js" type="text/javascript"></script>     
    <!-- 采用jquery实现jsonp调用示例-->   
    <script type="text/javascript">  
        $(function() {  
            $.getJSON("http://localhost:8081/demoproject/examples/jsonpwithjquery.page?jsonp_callback=?", function(data) {  
                alert("bbb:" + data.symbol);//弹出跨站 请求返回的json数据对象的symbol属性的值  
            });  
            //jsonp1337140657188({"postalcodes":[{"adminName2":"Westchester","adminCode2":"119","postalcode":"10504","adminCode1":"NY","countryCode":"US","lng":-73.700942,"placeName":"Armonk","lat":41.136002,"adminName1":"New York"}]});  
            $.getJSON("http://www.geonames.org/postalCodeLookupJSON?postalcode=10504&country=US&callback=?", function(data) {  
                alert( data.postalcodes[0].adminName2);//这是一个互联网跨域调用的实例，确保能够上网，弹出跨站 请求返回的json数据对象数组属性的第一个元素的属性adminName2的值  
            });   
        });          
    </script>  
    <!-- 采用jquery实现jsonp调用示例结束-->  
```

bboss mvc对jsonp提供了默认的支持，服务端json数据请求分别带了参数jsonp_callback

http://localhost:8081/demoproject/examples/jsonpwithjquery.page?jsonp_callback=?
jsonpCallback=?，是因为使用了jquery的$.getJSON方法来发起该请求，回调函数是个匿名函数，jquery框架会为该匿名函数产生一个随机函数名称，然后将?替换为实际的函数名称

提交给服务器端

http://localhost:8081/demoproject/examples/jsonp.page?jsonp_callback=jsonpCallback

jsonp_callback=jsonpCallback，可以看出我们已经直接指定了回调函数的名称，就是之前定义的实名函数jsonpCallback()

需要说明的是，回调函数对应的参数名称jsonp_callback是MappingJacksonHttpMessageConverter内置的默认的回调函数参数名称

我们可以全局地改变这个参数的名称，在bboss-mvc.xml文件中在MappingJacksonHttpMessageConverter插件上修改f:jsonpCallback属性的值即可：

Xml代码 

```xml
<property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter"  
                    f:jsonpCallback="jsonp_callback"/>  
```

**3.执行实例**

我们只需要将bboss-mvc下的WebRoot和bestpractice\demoproject\WebRoot对应的应用部署到tomcat并启动tomcat，然后在浏览器中输入

http://localhost:8081/bboss-mvc/jsp/jsonp/testjsonp.jsp

既可以看到实际的效果