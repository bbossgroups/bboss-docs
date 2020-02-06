### bboss防止跨站攻击策略

此前博客中撰文介绍了[bboss 动态令牌机制轻松搞定表单重复提交](http://yin-bp.iteye.com/blog/1662594)的方法，本文介绍bboss防止跨站攻击的方法。

通过增强bboss字符编码转换器的功能实现防止跨站攻击功能：

com.frameworkset.common.filter.CharsetEncodingFilter

单纯（不具备防止跨站攻击能力）的字符编码转换过滤器的使用方法如下：

Xml代码

```xml
<filter>  
        <filter-name>CharsetEncoding</filter-name>  
        <filter-class>com.frameworkset.common.filter.CharsetEncodingFilter</filter-class>  
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
```

  这种情况下CharsetEncodingFilter是不具备防止跨站攻击功能的，但是为其增加两个init-param参数后就可以了：

​    **wallfilterrules** 指定黑名单单词表，以逗号分隔多个单词，只要在参数值中出现其中的一个单词，参数值就会被置为null（即参数被过滤掉）  

   **wallwhilelist**指定不会被黑名单检测的参数的名称清单，多个参数以逗号分隔,白名单中的参数安全性需要应用程序自己控制，对值中出现的非法字符需要进行相应的处理后再输出到客服端（比如，针对浏览器的转义处理等措施）

下面看一个具体的配置示例：

Xml代码 

```xml
<filter>  
        <filter-name>CharsetEncoding</filter-name>  
        <filter-class>com.frameworkset.common.filter.CharsetEncodingFilter</filter-class>  
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
          <param-name>wallfilterrules</param-name>  
          <param-value><![CDATA[><,%3E%3C,<iframe,%3Ciframe,<script,%3Cscript,<img,%3Cimg,alert(,alert%28,eval(,eval%28,style=,style%3D]]>  
          </param-value>  
            
        </init-param>  
          
        <init-param>  
          <param-name>wallwhilelist</param-name>  
          <param-value><![CDATA[content,fileContent]]>  
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
```

配置完毕后，可以通过IBM APPSCAN和Netsparker之类的安全扫描工具来验证配置的有效性，同时可以根据测试结果或者实际情况调整wallfilterrules和wallwhilelist两个参数的配置，直到你的系统变得足够安全为止。