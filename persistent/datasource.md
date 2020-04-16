### bboss persistent通过jndi引用外部数据源（datasource）方法

关于bboss-persistent持久层框架通过jndi引用外部数据源（datasource）的说明 

bboss persistent框架可以引用外部数据源，例如tomcat，weblogic和WebSphere平台提供的datasource,需要在poolman.xml文件中配置，具体的做法可以参考本博客文章《bboss persistent框架数据库连接池配置介绍》，这里补充一点关于jndi名称的配置问题。这里以tomcat为例。

假如在tomcat的context上下文配置文件中配置了如下的datasource：

Java代码

```java
<Context path="" crossContext="true">  
  
        <!-- JAAS -->  
  
        <Realm  
                className="org.apache.catalina.realm.JAASRealm"  
                appName="PortalRealm"  
                userClassNames="com.liferay.portal.kernel.security.jaas.PortalPrincipal"  
                roleClassNames="com.liferay.portal.kernel.security.jaas.PortalRole"  
        />  
          
                <Resource  
                name="jdbc/LiferayPool"  
                auth="Container"  
                type="javax.sql.DataSource"  
                driverClassName="oracle.jdbc.driver.OracleDriver"  
                url="jdbc:oracle:thin:@localhost:1521:orcl"  
                username="lportal"  
                password="lportal"  
                maxActive="20"  
        />  
  
        <!--  
        Uncomment the following to disable persistent sessions across reboots.  
        -->  
  
        <!--<Manager pathname="" />-->  
  
        <!--  
        Uncomment the following to not use sessions. See the property  
        "session.disabled" in portal.properties.  
        -->  
  
        <!--<Manager className="com.liferay.support.tomcat.session.SessionLessManagerBase" />-->  
</Context>  
```

 其中的jdbc/LiferayPool即为该数据源jndi查找名称。

下面说明如何在poolman中配置一个连接池，该池直接通过【jdbc/LiferayPool】引用上下文中配置的连接池。poolman.xml文件的配置如下：

Xml代码

```xml
<?xml version="1.0" encoding="utf-8"?>  
<poolman>  
  
   <datasource external="true">  
    <dbname>datareuse</dbname>  
    <externaljndiName>jdbc/LiferayPool</externaljndiName>  
    <showsql>false</showsql>  
    <jndiName>dbcp_datasource_jndiname</jndiName>  
  </datasource>  
</poolman>  
```

 在应的web.xml文件中添加以下jndi引用配置：

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
  
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"  
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"  
         version="2.4">  
  
    <display-name>Creator esb Web Application</display-name>  
    ........  
    <resource-ref>  
        <description>DB Connection</description>  
        <res-ref-name>jdbc/LiferayPool</res-ref-name>  
        <res-type>javax.sql.DataSource</res-type>  
        <res-auth>Container</res-auth>  
    </resource-ref>  
</web-app>  
```

注意：
对于tomcat的jndi，bboss3.4及之前的版本需要在jdbc/LiferayPool前面添加了java:com/env/，这样才能正确地发现配置在上下文中的datasource，否则将无法获取这个datasource。

 < externaljndiName >java:com/env/jdbc/LiferayPool</ externaljndiName > 

3.5及以后的版本已经无需添加java:com/env/

externaljndiName>jdbc/LiferayPool</ externaljndiName >