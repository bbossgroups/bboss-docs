### ApplicationContext-拥有独立上下文件环境的组件容器管理类

即将发布的bbossgroups-1.0RC版本新增以下功能：

ApplicationContext-拥有独立上下文件环境的组件容器管理类，这里先介绍一下。

bbossgroups-1.0及以前的版本全部只支持manager-provider.xml文件为总根配置文件的配置模型

bbossgroups- 1.0-rc及以后的版本支持多个配置文件作为根配置文件的配置模型，这种模型中每个根文件表示独立的组件工厂上下文，彼此之间互不相关，这样必将影响远程服务调用时组件的寻址算法，原来只在一个组件上下文中寻址，现在有多个上下文，每个上下文中可能存在相同标识的组件，因此重新定义了服务组件的寻址算法，保证调用组件客服端的上下文和组件服务器端的上下文保持一致。  

新增程序：

org.frameworkset.spi.ApplicationContext

ApplicationContext 类主要用来构建不同的组件容器的上下文环境，ApplicationContext包含一下以下静态方法：

/**

  \* 获取默认上下文的bean组件管理容器，配置文件从manager-provider.xml文件开始

  \* @return

  */

public static ApplicationContext getApplicationContext()

/**

  \* 获取指定根配置文件上下文bean组件管理容器，配置文件从参数configfile对应配置文件开始

  \* 不同的上下文件环境容器互相隔离，组件间不存在依赖关系，属性也不存在任何引用关系。

  \* @return

  */

public static ApplicationContext getApplicationContext(String configfile)

上述两个静态方法用来创建组件容器实例，当创建好ApplicationContext实例后就可以在其上调用与BaseSPIManager组件中提供的一系列静态方法功能一致的实用方法。默认ApplicationContext组件容器相应方法和BaseSPIManager组件中提供的方法功能一致。  

使用实例：

本地服务调用

ApplicationContext context = ApplicationContext.getApplicationContext("org/frameworkset/spi/beans/testapplicationcontext.xml");

  RestfulServiceConvertor convertor = (RestfulServiceConvertor)context.getBeanObject("rpc.restful.convertor");

System.out.println(convertor.convert("a", "rpc.test"));  

远程服务调用

ApplicationContext context = ApplicationContext.getApplicationContext("org/frameworkset/spi/beans/testapplicationcontext.xml");

​     RestfulServiceConvertor convertor = (RestfulServiceConvertor)context.getBeanObject("(mina::192.168.11.102:1186)/rpc.restful.convertor");

​     System.out.println(convertor.convert("a", "rpc.test"));  