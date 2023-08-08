### BaseSPIManager组件介绍

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

在bboss aop框架中，我们将业务组件配置到xml文件中（关于配置的语法请参考《bboss aop配置语法》），比如manager-provider.xml，然后就可以通过组件BaseSPIManager来获取这些业务组件的实例。除了对业务组件进行管理，bboss aop框架还提供了系统全局属性配置的功能，这些属性同样可以通过BaseSPIManager提供的相关接口来获取。下面分三个部分说明上述的功能。

BaseSPIManager介绍

BaseSPIManager管理业务组件

BaseSPIManager管理系统配置属性

#### BaseSPIManager介绍

包路径说明

BaseSPIManager的完整包路径如下：

com.bboss.spi.BaseSPIManager

#### BaseSPIManager管理业务组件

##### 管理业务组件的两个静态接口

- ​         接口1  获取id为managerid的管理服务接口实例（如果有多个provider，则获取第一个provider实现）

public static Object getProvider(String providerManagerType) throws SPIException

- ​        接口2  获取id为managerid的管理服务接口实例，参数二对应多个provider中相应的provider 类型标识

public static Object getProvider(String providerManagerType, String sourceType) throws SPIException 

方法1和方法2的区别是：方法1的返回值和抛出的异常以默认（或者多个中的第一个provider）的provider的相应方法的返回值和异常为准，方法2返回值和抛出的异常以指定类型的provider的相应方法的返回值和异常为准。方法1和方法2的事务管理机制是一致和相同的。

这两个接口返回的对象类型为java.lang.Object,调用程序可以将该对象转型为相应的组件接口类型。

举例说明如下：

配置文件

<manager id="managerid "  //管理服务id

singlable="true" //单列模式

 \>

<provider type="provider_a"  //provider实现a

​           class="test.A" />

<provider type="provider_b" //provider实现b

​           class="test.B" />

< transactions >

< method name="handle" txtype="REQUIRED_TRANSACTION" />

</ transactions >

</ manager >

获取实例

AI a = (AI)BaseSPIManager.getProvider("managerid");

a将是test.A的代理实例。因为默认获取第一个provider实现。 

AI a = (AI)BaseSPIManager.getProvider("managerid",”provider_b”);

A将是test.B的代理实例。

 