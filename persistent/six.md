### bboss persistent事务管理介绍 （六）

#### 4.9.9声明式事务使用实例

##### 4.9.9.1声明式事务简介

声明式事务通过系统中基于java动态代理机制的aop框架实现，这个框架对业务组件进行有效的管理，包括业务组件的依赖注入，业务组件同步方法调用管理，业务组件拦截器的配置管理，业务组件方法事务控制。要想实现这些功能只需将业务组件配置到xml文件中即可，配置文件主文件在位于classes目录下，主文件为manager-provider.xml,可以有多个配置文件，将这些文件相对路径配置到主文件中，再通过BaseSPIManager组件提供的管理接口获取每个业务组件的接口实例即可。

##### 4.9.9.2声明式事务配置管理

- ######        配置语法

整个aop框架的xml配置文件语法如下：

​        <!--

​     manager-config(manager+,mangerimport*)

​     manager(provider+,synchronize?,transactions?,reference*,interceptor*)

​     manager-attributelist{

​        id-管理服务者id

​        jndiname-管理服务者jndi属性，目前未使用

​        singlable-是否是单实例模式

​        default-是否是缺省管理服务

​        interceptor-管理服务器的拦截器

​     }

​     \##

​    \# 如果在系统中配置了多个拦截器，整个框架能够确保每个拦截器都被调用

​     \# 调用的顺序为 拦截器配置的顺序,如果同时通过manager节点的属性interceptor定义了一个拦截器，

​     \# 则会首先调用这个属性对应的拦截器，然后再调用其他拦截器

​     \##

​     interceptor(pcdata)

​     interceptor-attributelist{

​        class-拦截器的实现类，所有的拦截器都必须实现

​              com.frameworkset.proxy.Interceptor接口

​              目前系统中提供了以下缺省拦截器：

​                 数据库事务管理拦截器（com.chinacreator.spi.

​                 interceptor.TransactionInterceptor）,支持对声明式事务的管理

​     }

​     reference(pcdata)

​     reference-attributelist{

​        fieldname-对应的管理服务提供者中的字段名称，必选属性

​        refid-引用的管理服务的id，对应manager节点的id属性，必选属性

​        reftype-对应的管理服务提供者类型，可选属性

​     }

​     provider(pcdata)  

​     provider-attributelist{

​        type-服务提供者标识id

​        used-服务提供者是否被启用,缺省值为false

​        class-服务提供者对应的class类

​        prior-provider调用的优先级

​     }

​     synchronize(method+)

​     synchronize-attributelist{

​        enabled-是否启用同步功能，如果没有启用同步功能

​                即使配置了多个服务提供者的同步方法，所有的同步功能将不起作用

​     }

​     transactions(method+)

   

​     method(param*,rollbackexceptions?)

​     method-attributelist{

​        name-方法名称，name和pattern不能同时出现

​        pattern-匹配方法名称的正则表达式

​        txtype-需要控制的事务类型，取值范围：

​                   NEW_TRANSACTION，

​                   REQUIRED_TRANSACTION，

​                   MAYBE_TRANSACTION，

​                   NO_TRANSACTION

​     }

​     param(pcdata)

​     param-attributelist{

​        type-

​     }

​     rollbackexceptions(exception+)

   

​     exception(pcdata)

​     exception-attributelist{

​        class-

​        type-IMPLEMENTS,INSTANCEOF

​     }

​     mangerimport(pcdata)

​     mangerimport-attributelist{

​        file-

​     }

 -->

在上述配置语法中的粉红色规则就是与声明式事务相关的语法。这里只介绍有关事务的配置语法。