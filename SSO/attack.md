### bboss跨站攻击白名单和脚本攻击防火墙配置

本文详细介绍bboss跨站攻击白名单和跨站脚本攻击防火墙配置

**首先看一个完整的过滤器配置：**

Xml代码

```xml
<filter>  
   <filter-name>CharsetEncoding</filter-name>  
   <filter-class>com.frameworkset.common.filter.SessionCharsetEncodingFilter</filter-class>  
   <init-param>  
     <param-name>RequestEncoding</param-name>  
     <param-value>UTF-8</param-value>  
   </init-param>  
   <init-param>  
     <param-name>ResponseEncoding</param-name>  
     <param-value>UTF-8</param-value>  
   </init-param>  
   <init-param>  
     <param-name>mode</param-name>  
     <param-value>0</param-value>  
   </init-param>    
   <init-param>  
     <param-name>checkiemodeldialog</param-name>  
     <param-value>true</param-value>  
       
   </init-param>  
   <init-param>  
     <param-name>refererDefender</param-name>  
     <param-value>true</param-value>  
       
   </init-param>  
  <init-param>  
     <param-name>refererwallwhilelist</param-name>  
     <param-value>*.bboss.com.cn,http://*.referer.ibm.com</param-value>  
   </init-param>  
   <init-param>  
     <param-name>wallfilterrules</param-name>  
     <param-value><![CDATA[><,%3E%3C,<iframe,%3Ciframe,<script,%3Cscript,<img,%3Cimg,alert(,alert%28,eval(,eval%28,style=,style%3D,[window['location'],{valueOf:alert},{toString:alert},[window["location"],new Function(]]>  
     </param-value>  
       
   </init-param>  
     
   <init-param>  
     <param-name>wallwhilelist</param-name>  
     <param-value><![CDATA[content,fileContent,extfieldvalues,questionString]]>  
     </param-value>  
       
   </init-param>  
     
  </filter>  
  
  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>*.jsp</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>*.do</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>*.frame</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>*.page</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>*.freepage</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>/cxfservices/*</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>/jasperreport/*</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>/druid/*</url-pattern>  
</filter-mapping>  
<filter-mapping>  
  <filter-name>CharsetEncoding</filter-name>  
  <url-pattern>/Kaptcha.jpg</url-pattern>  
</filter-mapping>  
```

**过滤器中涉及到的跨站攻击配置参数有：**

Xml代码

```xml
<init-param>  
          <param-name>refererDefender</param-name>  
          <param-value>true</param-value>  
            
        </init-param>  
       <init-param>  
          <param-name>refererwallwhilelist</param-name>  
          <param-value>*.bboss.com.cn,http://*.referer.ibm.com</param-value>  
        </init-param>  
```

**参数含义说明：**

refererDefender 是否启用跨站攻击防御功能，true启用，false关闭，默认关闭
refererwallwhilelist 配置跨站访问白名单， 开启跨站攻击防御功能后，必须将允许跨站访问的可信域名配置到白名单中，否则不允许被不可信的域名访问。白名单配置规则：多个域名用逗号分隔，域名中可以并只能包含一个*号通配符，多于的*号直接被忽略。

**过滤器中涉及到的脚本攻击配置参数有：**

Xml代码 

```xml
<init-param>  
          <param-name>wallfilterrules</param-name>  
          <param-value><![CDATA[><,%3E%3C,<iframe,%3Ciframe,<script,%3Cscript,<img,%3Cimg,alert(,alert%28,eval(,eval%28,style=,style%3D,[window['location'],{valueOf:alert},{toString:alert},[window["location"],new Function(]]>  
          </param-value>  
            
        </init-param>  
          
        <init-param>  
          <param-name>wallwhilelist</param-name>  
          <param-value><![CDATA[content,fileContent,extfieldvalues,questionString]]>  
          </param-value>  
            
        </init-param>  
```

**参数含义说明：**

wallfilterrules 配置参数中非法的过滤词，多个用逗号分隔，只要请求参数包含其中的任意一个过滤词，即为非法请求参数，参数值将被清空。

wallwhilelist 配置不需要检测过滤词的参数名称列表，也就是白名单列表，多个用逗号分隔，出现在这个清单中的参数名称不接收wallfilterrules规则扫描。