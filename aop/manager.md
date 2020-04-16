### bboss aop 系统全局属性管理

Bboss aop 框架在1.0.6版本中增加全局属性配置管理功能，并提供了相应的接口来获取这些属性，本节详细介绍。

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

#### 属性配置

Bboss aop中可以在业务组件的配置文件配置系统全局属性，用户可以在

- ​         manager-provider.xml配置

- ​         manager-xxxx-provider.xml中配置

- ​         专名的manager-xxx-propeties.xml配置，需要导入到manager-provider.xml文件中

下面是manager-provider.xml的一个例子：

< manager-config >

​    < properties >

​       <property name=*"cluster_enable"* value=*"true"*/>

​       <property name=*"cluster_mbean_enable"* value=*"true"*/>

​       <property name=*"cluster_name"* value=*"Cluster"*/>     

​    </ properties >   

​    <managerimport file=*"com/chinacreator/spi/properties/properties.xml"* />

​    <managerimport file=*"com/chinacreator/spi/rpc/service-assemble.xml"* />

</ manager-config >

其中单独配置了几个属性，同时导入了一个文件

*com/chinacreator/spi/properties/properties.xml*

其内容为：

< manager-config >

​    < properties >

​       <property name=*"int_enable"* value=*"1"*/>

​       <property name=*"cluster_str"* value=*"cluster_str"*/>

​    </ properties >

</ manager-config >  

#### 属性获取

BaseSPIProvider组件中提供了如下接口用来获取全局属性的值：

**public static String getProperty(String name)** ；

​    **public static int getIntProperty(String name)** **；** 

​    **public static boolean getBooleanProperty(String name)**；

​     **public static String getProperty(String name, String defaultValue)**；

​    **public static int getIntProperty(String name, int defaultValue)** **；** 

​    **public static boolean getBooleanProperty(String name, boolean defaultValue)** **；**

通过上述接口，你可以获取以下类型的属性：

String

Int

Boolean

并且可以指定缺省值，具体用法就不举例了。 