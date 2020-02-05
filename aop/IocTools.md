### bboss ioc提供的组件初始化工具类介绍

bboss ioc提供一些非常便利的工具类和接口，业务组件可以实现这些工具接口或者继承这些工具类，从而在组件初始化的时候将ioc容器对象、组件的配置信息、国际化组件对象、类加载器对象注入到组件实例中。

  这些接口和抽象类路径及作用如下：

**接口**  

 org.frameworkset.spi.BeanClassLoaderAware 为组件注入类加载器对象，需要自己在组件中定义

ClassLoader字段
org.frameworkset.spi.ApplicationContextAware 将ioc容器对象注入组件实例
org.frameworkset.spi.BeanNameAware 将组件名称注入组件实例

org.frameworkset.spi.support.MessageSourceAware 将ioc容器对象对应的国际化组件注入组件实例

 **抽象类**

org.frameworkset.spi.support.ApplicationObjectSupport 将ioc容器对象注入组件实例，已经定义容器对象属性

org.frameworkset.spi.BeanInfoAware 将组件所有配置信息注入组件实例  