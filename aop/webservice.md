### 解决cxf+bboss发布的webservice缺少<wsdl:types>和<wsdl:message>标签的问题

cxf+bboss发布webservice服务（cxf+bboss发布webservice服务方法请参考文档：

[bbossgroups webservice引擎使用方法](http://bbossgroups.group.iteye.com/group/wiki/3091-webservice-bboss-aop)），服务发布成功，查看其wsdl文件的时候却缺少<wsdl:types>和<wsdl:message>标签,例如：

Xml代码

```xml
 <?xml version="1.0" encoding="UTF-8" ?>   
- <wsdl:definitions name="MaterialWServiceImplService" targetNamespace="http://impl.webservice.material.mms.test.com/" xmlns:ns1="http://webservice.material.mms.test.com/" xmlns:ns2="http://schemas.xmlsoap.org/wsdl/soap/http" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:tns="http://impl.webservice.material.mms.s.com/" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">  
  <wsdl:import location="http://10.8.135.224:8081/testMMS/cxfservices/queryTaskList?wsdl=MaterialWService.wsdl" namespace="http://webservice.material.mms.test.com/" />   
- <wsdl:binding name="MaterialWServiceImplServiceSoapBinding" type="ns1:MaterialWService">  
  <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http" />   
- <wsdl:operation name="queryAwaitTaskByUserNum">  
  <soap:operation soapAction="" style="document" />   
- <wsdl:input name="queryAwaitTaskByUserNum">  
  <soap:body use="literal" />   
  </wsdl:input>  
- <wsdl:output name="queryAwaitTaskByUserNumResponse">  
  <soap:body use="literal" />   
  </wsdl:output>  
  </wsdl:operation>  
  </wsdl:binding>  
- <wsdl:service name="MaterialWServiceImplService">  
- <wsdl:port binding="tns:MaterialWServiceImplServiceSoapBinding" name="MaterialWServiceImplPort">  
  <soap:address location="http://10.8.135.224:8081/sMMS/cxfservices/queryTaskList" />   
  </wsdl:port>  
  </wsdl:service>  
  </wsdl:definitions>  
```

认真看发布后的wsdl文件，发现多了<wsdl:import>标签：

Xml代码

```xml
<wsdl:import location="http://10.8.135.224:8081/sMMS/cxfservices/queryTaskList?wsdl=MaterialWService.wsdl" namespace="http://webservice.material.mms.test.com/" />   
```

将<wsdl:import>标签中的location拿出去在浏览器中打开，里面是“丢失”的两个标签，这时候就发现其实并不是丢失了，而是包含在了<wsdl:import>标签内

为什么会包含在了<wsdl:import>标签内？仔细查看生成的wsdl，发现<wsdl:definitions>标签内的targetNamespace属性和<wsdl:import>中namespace属性的值不同，这就是原因所在，发布服务时，接口类和服务实现类的@Webservice注解中没有指定targetNamespace为一个名称或者没有指定时（cxf发布服务时会默认将类和接口的包路径反转，然后作为targetNamespace的值），就会导致上述现象，最终解决方案：  

1，将接口类和实现类放在同一个包下，问题即可解决

2，将接口类和实现类中的注解中加入命名空间属性配置，@WebService(targetNamespace="XXXXX")，两个配置值保持一致即可

实现类：

Java代码

```java
package org.frameworkset.web.ws;  
  
import javax.jws.WebService;  
  
/** 
 * <p>Title: WSServiceImpl.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2008</p> 
 * @Date 2011-4-24 
 * @author biaoping.yin 
 * @version 1.0 
 */  
@WebService(targetNamespace="org.frameworkset.web.ws")  
public class WSServiceImpl implements WSService{  
  
    public String sayHello(String duoduo) {  
  
        if(duoduo == null)  
            return "";  
        else  
            return duoduo;  
    }  
  
}  
```

接口定义：

Java代码

```java
package org.frameworkset.web.ws;  
  
import javax.jws.WebService;  
  
/** 
 * <p>Title: WSService.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2008</p> 
 * @Date 2011-4-24 
 * @author biaoping.yin 
 * @version 1.0 
 */  
@WebService(targetNamespace="org.frameworkset.web.ws")  
public interface WSService {  
      
    public String sayHello(String duoduo)  
    ;  
  
}  
```

