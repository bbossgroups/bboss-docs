### bboss aop 实践（3）构造函数依赖注入

系列文章的前两篇介绍bboss aop框架的配置文件语法和属性依赖注入功能，本篇介绍bboss aop框架的构造函数依赖注入功能。

bboss-aop-1.0.5，下载地址： https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290546&release_id=658454 

构造函数注入业务组件的属性值是bboss aop框架提供的另一种依赖注入的方法，这种方法可以通过以下三种方式注入业务组件的属性：

- ​        常量方式注入

- ​        给定属性对应类型名称，由框架实时构建该类型的实例

- ​         引用已有的其他业务组件

这一节详细介绍这种依赖注入法。

##### 准备工作，定义一个带有构造函数的业务组件

- ​         组件接口

**public** **interface** ConstructorInf {

   

​    **public** **void** testHelloworld();

 

}

- ​         组件实现 

**public** **class** ConstructorImpl **implements** ConstructorInf {

​    **private** String message;//验证常量方式注入

​    **private** AI ai;// 引用已有的其他业务组件

​    **private** Test test;// 给定属性对应类型名称，由框架实时构建该类型的实例

​    **public** ConstructorImpl(String message, AI ai,Test test)//依赖注入的构造函数

​    {

​       **this**.message = message;

​       **this**.ai = ai;

​       **this**.test = test;

​    }

​    **public** ConstructorImpl() //默认的构造函数

​    {

​       **this**.message = message;

​       **this**.ai = ai;

​    }

​    **public** **void** testHelloworld() { //业务方法      

​       System.*out*.println(message);

​       System.*out*.println(ai);

​    } 

}

##### 准备后组件之后，编写bboss aop框架的业务组件配置文件manager-constructor.xml

内容如下：

< ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​    <manager id="constructor.a"

​       singlable="true">

​       <provider type="A" default="true"

​           class="com.bboss.spi.constructor.ConstructorImpl" />   

​       < construction >   

​           < param value="hello world" />

​           < param refid="interceptor.a" />

​           < param type="com.bboss.spi.constructor.Test" />

​       </ construction >

​    </ manager >

</ manager-config >

将manager-constructor.xml文件存储在classes下的包com.bboss.spi.constructor目录下，

再将manager-constructor.xml配置在主文件manager-provider.xml(该文件在classes根目录下)中： 

< manager-config >

​    ... ...   

​    < managerimport file="com/bboss/spi/interceptor/manager-interceptor.xml" />  

​    < managerimport file="com/bboss/spi/constructor/manager-constructor.xml" />

​    ... ... 

</ manager-config >

其中引用的业务组件"interceptor.a"

< param refid="interceptor.a" />

配置在拦截器实例配置文件manager-interceptor.xml中：

< manager-config >

​    <manager id="interceptor.a"

​       singlable="true">

​       <provider type="A" default="true"

​           class="com.bboss.spi.interceptor.A" />

​       <!-- against the loop ioc rules,because constructor.a reference interceptor.a 

-->

​       <!-- 

< reference fieldname="const" refid="constructor.a"/>-->      

​       < synchronize enabled="true">

​           < method name="testInterceptorsBeforeafterWithTX"/>

​           < method name="testInterceptorsBeforeAfter"/>

​           < method name="testInterceptorsBeforeThrowing"/> 

​           < method name="testInterceptorsBeforeThrowingWithTX">

​           </ method>

​       </ synchronize>

​       < interceptor class="com.bboss.spi.interceptor.Insterceptor"/>

​       < interceptor class="com.bboss.spi.interceptor.Insterceptor1"/>     

​       <!-- 

​           在下面的节点对provider的业务方法事务进行定义

​           只要将需要进行事务控制的方法配置在transactions中即可

​       -->

​       < transactions>

​           < method name="testInterceptorsBeforeafterWithTX"/>

​           < method name="testInterceptorsBeforeAfter"/>

​           < method name="testInterceptorsBeforeThrowing">

