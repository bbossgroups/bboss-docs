### bboss session自定义session id生成机制介绍

#### **1.bboss session自定义session id生成接口**

Java代码

```java
package org.frameworkset.security.session;  
  
public interface SessionIDGenerator {  
    String generateID();  
  
}  
```

#### **2.实现（以默认实现为示例）**

实现（以默认实现为示例）org.frameworkset.security.session.impl.UUIDSessionIDGenerator

Java代码

```java
package org.frameworkset.security.session.impl;  
  
import java.util.UUID;  
  
import org.frameworkset.security.session.SessionIDGenerator;  
  
public class UUIDSessionIDGenerator implements SessionIDGenerator {  
  
    @Override  
    public String generateID() {  
        String sessionid= UUID.randomUUID().toString();  
        return sessionid;  
    }  
  
}  
```

#### **3.在sessionconf.xml中配置SessionIDGenerator**

Xml代码

```xml
<property name="sessionManager" class="org.frameworkset.security.session.impl.SessionManager"  
        init-method="init" destroy-method="destroy">                   
            <property name="sessionIDGenerator" class="org.frameworkset.security.session.impl.UUIDSessionIDGenerator"/>   
            <property name="sessionTimeout" value="3600000"/>  
            <property name="sessionstore" refid="attr:sessionstore"/>   
            <property name="cookiename" value="JSESSIONID"/>            
            <property name="httpOnly" value="true"/>  
            <property name="secure" value="false"/>  
            <property name="lazystore" value="true"/>  
            <property name="monitorAttributes" ><![CDATA[ 
            [ 
                {"name":"userAccount","cname":"账号","type":"String","like":true,"enableEmptyValue":false},                
                {"name":"worknumber","cname":"工号","type":"String","like":false,"enableEmptyValue":true} 
            ]            
            ]]></property>  
      
    </property>  
      
    <property name="sessionStaticManager"  
        f:monitorScope="all" class="org.frameworkset.security.session.statics.MongoSessionStaticManagerImpl"  
          
        />     
      
    <property name="sessionstore" class="org.frameworkset.security.session.impl.MongDBSessionStore"/>  
</properties>  

```

