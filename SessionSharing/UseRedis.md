### bboss session redis插件使用指南

bboss session 采用redis存储会话功能介绍，bboss session在线演示地址：
http://sessionmonitor.bbossgroups.com/

bboss session支持mongodb和redis两种方式存储web应用的session数据,二者在实际生产环境运行效果都不错。采用redis存储session相较mongodb性能更好，但是由于redis不是真正意义上的nosql数据库，不能像mongodb一样提供强大的类数据库的查询统计功能，因此基于redis的bboss session的监控统计功能比较弱，只能做到：

- 可以浏览接入的应用系统清单，但是无法直观地查看应用的会话数

- 可以查看每个应用的bboss session配置参数

- 只能根据session id查询、管理对应的session和查看session中存放的数据，无法直接获取应用的所有session列表

    bboss session redis插件采用jedis来连接redis服务器和操作redis中的session数据。

  用户可以根据需要选择mongodb和redis中的一种机制来存储session数据，mongodb的集成配置参考文档章节【6.mongodb客户端配置】：
  [bboss session共享使用方法介绍](http://yin-bp.iteye.com/blog/2064662)  

  本文介绍bboss session的redis的集成配置

  #### **一、redis服务器配置**

  redis服务器配置请参考文档

  http://yin-bp.iteye.com/blog/2360280

  #### **二、bboss session配置redis存储插件**

  修改/resources/sessionconf.xml配置文件，默认配置mongodb存储插件和mongodb统计监控插件

  Xml代码

  ```xml
  <property name="sessionStaticManager" f:monitorScope="all" class="org.frameworkset.security.session.statics.MongoSessionStaticManagerImpl"/>    
        
      <property name="sessionstore" class="org.frameworkset.security.session.impl.MongDBSessionStore"/>  
  ```

  采用redis时，只需将存储插件和统计插件改为redis的对应的实现即可：
  Xml代码

  ```xml
  <property name="sessionStaticManager" f:monitorScope="all" class="org.frameworkset.security.session.statics.RedisSessionStaticManagerImpl"/>  
        
      <property name="sessionstore" class="org.frameworkset.security.session.impl.RedisSessionStore"/>  
  ```

  统计监控插件sessionStaticManager的f:monitorScope监控范围属性仍然有效，all监控所有接入的应用session配置和session数据（redis环境下只能根据sessionid查看单个session），self监控本应用的session配置和session数据（redis环境下只能根据sessionid查看单个session）。

  需要集成bboss session请参考demo使用文档，demo中提供了集成bboss session以及session监控模块的最小依赖jar文件和相关资源、配置文件：
  [bboss会话共享demo使用指南](http://yin-bp.iteye.com/blog/2087308)