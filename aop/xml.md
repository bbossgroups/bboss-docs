### bboss-aop 实践(1) xml配置文件语法

bboss-aop框架是一个基于动态代理技术实现的轻量级aop框架，提供基本的组件管理功能(支持组件单实例和多实例模式)，支持声明式事务管理，拦截器（可配置多个拦截器），以及依赖注入（提供防止循环依赖注入的能），管理服务方法的同步调用。

后续的文章将介绍这些功能。本系列文章适用于bboss-aop-1.0.5，下载地址：

[https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290546&release_id=658454](https://sourceforge.net/project/showfiles.php?group_id=238653&amp;amp;package_id=290546&amp;amp;release_id=658454)

在介绍各个功能之前先介绍以下bboss-aop的xml配置文件的配置语法，非常简单。

1.bboss-aop的xml配置文件的配置语法

​        <!--

​        manager-config(manager+,managerimport)

​        \##

​        \# 通过manager节点可以配置管理服务的一个实现或者多个实现者，这些实现者全部实现共同的接口（直接实现或者间接实现都可以）

​        \# 一个manager节点配置多个接口的目的是为了进行方法同步调用，当然这种情况并不多见

​        \# 通过singlable属性用来标识管理服务是否是单实例模式

​        \##

​        manager(provider+,synchronize?,transactions?,reference*,interceptor*,construction)

​        manager-attributelist{

​                id-管理服务者id

​                singlable-是否是单实例模式   

​                           callorder_sequence-拦截器执行顺序，true按顺序执行，false按链式顺序执行，缺省值true

​        }

​        \##

​        \# 如果在系统中配置了多个拦截器，框架能够确保每个拦截器都被调用

​        \# 调用的顺序为 拦截器配置的顺序

​        \##

​        interceptor(pcdata)

​        interceptor-attributelist{

​                class-拦截器的实现类，所有的拦截器都必须实现

​                      com.frameworkset.proxy.Interceptor接口

​                      目前系统中提供了以下缺省拦截器：

​                              数据库事务管理拦截器（com.chinacreator.spi.

​                              interceptor.TransactionInterceptor）,支持对声明式事务的管理

​        }

​    ##

#属性依赖注入节点，可以用来注入管理服务引用的其他管理服务

​    ##

​    reference(pcdata)

​    reference-attributelist{

​            fieldname-对应的管理服务提供者中的字段名称，必选属性

​            refid-引用的管理服务的id，对应manager节点的id属性，

​            reftype-对应的管理服务提供者类型，可选属性,可以作为refid的辅助属性

​            class-直接应用class指定的一个实例对象

​            value-直接指定属性对应的标量值

​            refid，class，value三个属性只能任意指定一个

​    }

​    ##

#一个provider对应管理的服务的一个实现者

​    ##

​    provider(pcdata) 

​    provider-attributelist{

​            type-服务提供者标识id

​            used-服务提供者是否被启用,缺省值为false

​            class-服务提供者对应的class类

​            prior-provider调用的优先级

​    }

​    ##

#construction节点用来实现构造函数的依赖注入(ioc)功能

​    ##

​    construction(param)

​    ##

#synchronize节点用来配置多个provider中需要进行同步调用的方法

​    ##

​    synchronize(method+)

​    synchronize-attributelist{

​            enabled-是否启用同步功能，如果没有启用同步功能

​                    即使配置了多个服务提供者的同步方法，所有的同步功能将不起作用

​    }

​    ##

#transactions节点可以配置需要进行声明式事务控制的方法

​    ##
​    transactions(method+)

​    ##

#method节点可以内嵌在synchronize和transactions节点中用来配置同步方法和事务控制方法

​    ##
​    method(param*,rollbackexceptions?)

​    method-attributelist{

​            name-方法名称，name和pattern不能同时出现

​            pattern-匹配方法名称的正则表达式

​            txtype-需要控制的事务类型，取值范围：

​                                    NEW_TRANSACTION，

​                                    REQUIRED_TRANSACTION，

​                                    MAYBE_TRANSACTION，

​                                    NO_TRANSACTION

​    }

​    ##

#param节点用来指定构造函数的参数，也用来指定同步方法和事务方法的参数

​    ##
​    param(pcdata)

​    param-attributelist{

​            type-参数的类型,如果是构建函数的参数，那么将创建type对应的class的对象实例作为构建函数的实例参数

​            value-用来指定构造函数的参数值

​            refid-用来指定构造函数的引用其他对象标识

​    }

​    ##

#rollbackexceptions节点内嵌在mehtod节点中，用来声明需要回滚事务的异常集，如果声明了事务回滚异常，

#那么除了这些配置的异常（如果异常的type属性为INSTANCEOF，还包括异常的子类）、默认的SQLException、系统级别异常会回滚事务外，

#其他的异常都不会导致事务回滚

#如果事务方法没声明回滚异常，那么所有的异常都会导致事务回滚

​    ##
​    rollbackexceptions(exception+)
​    ##

#exception节点内嵌在rollbackexceptions节点中，用来声明需要回滚事务的异常，对应的异常将会导致事务回滚，

#那么除了这些配置的异常（如果异常的type属性为INSTANCEOF，还包括异常的子类）、默认的SQLException、系统级别异常会回滚事务外，

#其他的异常都不会导致事务回滚

​    ##

​    exception(pcdata)

​    exception-attributelist{

​            class-异常对应的class

​            type-IMPLEMENTS,INSTANCEOF

​    }

​    ##

#managerimport节点可以在bboss-aop配置文件中导入其他的管理服务配置文件

​    #

​    ##

​    managerimport(pcdata)

​    managerimport-attributelist{

​            file-导入其模块配置文件

​    }       

-->

以下是一个简单的带构造函数注入的配置实例：

< ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​        < manager id="constructor.a"

​              singlable="true">

​                < provider type="A"

​                        class="com.chinacreator.spi.constructor.ConstructorImpl" />   

​                < construction >    

​                        < param value="hello world" />

​                        < param refid="interceptor.a" />

​                        < param type="com.chinacreator.spi.constructor.Test" />

​                </ construction >

​        </ manager >

</ manager-config >

了解了基本的语法后，再来看一下bboss-aop框架的bean管理组件及其通过bean管理组件获取管理服务实例的具体方法。

2.bboss-aop框架的bean管理组件及其通过bean管理组件获取管理服务实例的具体方法

​        bean管理组件：

​        com.chinacreator.spi.BaseSPIManager           

​    BaseSPIManager中定义的获取管理服务实例的静态方法：

/**

*获取管理服务的实例方法

*@param providerManagerType String 对应于配置文件中manager节点id属性的值

*@return Object 本方法返回管理服务中的第一个provider的实例对象Object

*@throws SPIException 如果获取管理服务失败将抛出SPIException，失败的原因可能是，对应的管理服务不存在

*或者配置不正确，或者是出现了循环依赖注入（属性依赖注入和构建函数依赖注入）的情况

 */

public static Object getProvider(String providerManagerType) throws SPIException

/**

*获取管理服务的实例方法

*@param providerManagerType String 对应于配置文件中manager节点id属性的值

*@param sourceType String 对应于配置文件中provider节点type属性的值

*@return Object 本方法返回管理服务中type为sourceType的provider的实例对象Object

*@throws SPIException 如果获取管理服务失败将抛出SPIException，失败的原因可能是，对应的管理服务不存在

*或者配置不正确，或者是出现了循环依赖注入（属性依赖注入和构建函数依赖注入）的情况

 */

public static Object getProvider(String providerManagerType,String sourceType) throws SPIException

  获取组件实例示例：

  以第一节中的配置文件为例，组件com.chinacreator.spi.constructor.ConstructorImpl直接（也可以间接）实现了

  接口com.chinacreator.spi.constructor.ConstructorInf

   try {

​                        ConstructorInf a = (ConstructorInf)BaseSPIManager.getProvider("constructor.a");

​                        //等价的调用

ConstructorInf a = (ConstructorInf)BaseSPIManager.getProvider("constructor.a","A");

​                                          a.testHelloworld();                       

​            } catch (SPIException e) {

​                    e.printStackTrace();

​            }