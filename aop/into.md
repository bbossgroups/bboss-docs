### bboss aop 实践（4） 防止循环依赖注入

 bboss项目下载列表 在sourceforge访问地址为：
https://sourceforge.net/project/showfiles.php?group_id=238653  

前两节介绍了bboss aop框架的两种依赖注入方式：属性依赖注入和构造函数依赖注入。这一节介绍一下bboss aop框架防止循环依赖注入的功能。

在介绍防止循环依赖注入之前，我们首先介绍一下java组件中的属性循环引用的情况。所谓循环引用就是说组件之间相互引用，导致循环引用，例如：

对象A引用了对象B，对象B引用对象C，对象C引用了对象A，这样就形成了一种循环引用的场景。

使用bboss aop框架的依赖注入功能时，应用避免出现业务组件的循环依赖注入的情况，bboss aop能够有效的防止这种情况的出现，一旦开发人员配置了这种场景，应用程序通过com.chinacreator.spi.BaseSPIManager的getProvider方法获取业务组件的实例时将抛出异常：

**throw** **new** CurrentlyInCreationException("loop inject error the inject context path is [A>B>C>A]");

明确地提示出现了A>B>C>A的循环依赖注入。

这种情况的配置示例如下：

< ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​    < manager id="a" singlable="true" >      

​       <provider type="DB"

​           class="com.chinacreator.spi.reference.A" />

​       < reference fieldname="b" refid="b" />

​    </ manager >   

​    < manager id="b" singlable="false" >

​       <provider type="DB"

​           class="com.chinacreator.spi.reference.B" />

​       < reference fieldname="c" refid="c"  />

​    </ manager >

​    < manager id="c" singlable="false" >      

​       < reference fieldname="a" refid="a" />        

​       <provider type="DB"

​           class="com.chinacreator.spi.reference.C" />

​    </ manager >

</ manager-config >

一般情况下只有在依赖的注入的setter方法和构造函数中出现循环引用应该防止外，情况出现循环引用是允许的。