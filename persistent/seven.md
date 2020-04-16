### bboss persistent事务管理介绍 （七）

[bboss persistent事务管理介绍 （七）](http://www.iteye.com/post/931345)

- ######         配置步骤

从上述的语法当中可以看出，要声明一个业务组件的事务方法的步骤为：

第一步 准备好业务组件，参考下面的模型



第二步 在配置文件中配置一个管理服务，也就是添加一个manager元素(指定管理服务的惟一标识id属性，是否单实例singlable属性)，

第三步 在manager中配置一个或者多个provider（管理服务提供者，每个管理服务提供者需要指定自己的type属性，以及具体的实现类class）

第四步 在transactions元素中使用method元素、rollbackexcepiton元素、exception元素、param元素声明所有需要进行事务控制的方法以及控制事务的规则。

- ######         配置示例

< manager-config >

​    < manager id="tx.a" singlable="false"  >

​       <!--

​           属性描述：

​           type：代表数据存储的类型,例如DB，LDAP,ACTIVEDIRECTORY等等

​           default:缺省实现，不管used是否指定都会在同步方法中调用

​           class：实现类代码

​           used：标识是否使用，默认为false

​       -->

​       <provider type="DB" default="true"

​           class="com.chinacreator.spi.transaction.A1" />

​       <!-- 

​           在下面的节点对provider的业务方法事务进行定义

​           只要将需要进行事务控制的方法配置在transactions中即可

​          

​       -->

​       < transactions >

​           <!--

​              定义需要进行事务控制的方法

​              属性说明：

​              name-方法名称，可以是一个正则表达式，正则表达式的语法请参考jakarta-oro的相关文档，如果使用

​              正则表达式的情况时，则方法中声明的方法参数将被忽略，但是回滚异常有效。

​              pattern-方法名称的正则表达式匹配模式，通常匹配

​              txtype-需要控制的事务类型，取值范围：

​              NEW_TRANSACTION，

​              REQUIRED_TRANSACTION，

​              MAYBE_TRANSACTION，

​              NO_TRANSACTION

​           -->

​           <!--

​           模式匹配，模式匹配的顺序受配置位置的影响，如果配置在后面或者中间，那么会先执行之前的方法匹配，如果匹配上了就不会

​           对该模式方法进行匹配了，否则执行匹配操作。

​      

​           如果匹配上特定的方法名称，那么这个方法就是需要进行同步的方法

​           模式testInt.*匹配接口中以testInt开头的任何方法

​            -->

​           < method name="testTXInvoke" txtype="NEW_TRANSACTION">

​              < param type="java.lang.String"/>

​           </ method >         

​           < method name="testTXInvoke" txtype="REQUIRED_TRANSACTION" />

​           < method name="testTXInvokeWithReturn" txtype="REQUIRED_TRANSACTION"/>

​           < method name="testTXInvokeWithException" txtype="MAYBE_TRANSACTION"/>    

​           < method name="testSameName" txtype="NO_TRANSACTION"/>          

​           < method name="testSameName1">

​              < param type="java.lang.String"/>

​           </ method>

​           <!--              

​                  定义使事务回滚的异常,如果没有明确声明需要回滚事务的异常，那么当有异常发生时，事务管理框架将自动回滚当前事务

​                  class-异常的完整类路径

​                  type-是否检测类型异常的子类控制标识，

​                  IMPLEMENTS只检测异常类本身，忽略异常类的子类；

​                  INSTANCEOF检查异常类本省及其所有子类                 

​              -->