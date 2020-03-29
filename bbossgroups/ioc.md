### bboss aop ioc机制配置

bboss aop框架通过其动态代理模块来创建所有业务组件的代理对象实例，代理对象保持业务组件对象的引用，以便对声明式事务、注解事务、同步控制、拦截器、远程服务组件方法进行拦截调用。当业务组件引用其他业务组件的实例时，可以通过依赖注入的方式来初始化该引用实例的值，也可以通过依赖注入方式指定业务组件基本属性的值（目前支持两种基本类型，数字类型和字符串类型）。Bbossgroups 2.0中的Aop框架提供两种proxy机制：基于java的动态代理和cglib代理，系统默认采用cglib代理机制，cglib是在2.0版本中新引入的代理机制。用户自动切换代理模式，切换的方法如下：

找到manager-provider.xml文件中的属性项aop.proxy.type直接进行修改即可：

<!--

aop实现机制：

javaproxy java动态代理模式

cglib cglib模式

-->
<property name="aop.proxy.type" 

value="cglib"/>

引入cglib机制后，组件无需实现任何接口即可实现基于bboss aop框架的aop功能即声明式事务、注解事务、同步控制、拦截器、远程服务方法调用等等。

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/  