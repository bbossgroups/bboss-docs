### bboss persistent事务管理介绍 （九）

**事务控制规则**

根据实际情况事务控制规则划分为：

1. 不带参数方法

 < method name="testTXInvoke"/>

说明：指定需要控制事务的方法为testTXInvoke，这里没有声明事务类型，默认的事务类型为REQUIRED_TRANSACTION

​     在这种规则下一旦方法执行时向外抛出异常，则会导致事务回滚，如果方法正常结束则事务提交。如果声明了回滚异常规则，则按照回滚异常规则控制事务的回滚和提交。 

2. 带参数事务方法，只声明需要进行事务控制的方法信息，包括方法的名称和方法参数

< method name="testTXInvoke" >

​    < param type="java.lang.String" />

</ method >

说明：指定需要控制事务的方法为testTXInvoke，并且方法的带一个String类型的参数，这里没有声明事务类型，默认的事务类型为REQUIRED_TRANSACTION

​     在这种规则下一旦方法执行时向外抛出异常，则会导致事务回滚，如果方法正常结束则事务提交。如果声明了回滚异常规则，则按照回滚异常规则控制事务的回滚和提交。

​     如果参数为数组时，需要配置param的type属性需要指定为特定类型的数组类型名称，比如字符串数组类型为：[Ljava.lang.String;

3. 指定事务类型规则      

< method name="testTXInvoke" txtype="REQUIRED_TRANSACTION" >

​    < param type="java.lang.String" />

</ method >

说明：指定需要控制事务的方法为testTXInvoke，并且方法的带一个String类型的参数，同时通过txtype属性明确地将事务类型指定为

REQUIRED_TRANSACTION

Txtype属性的取值范围如下，具体的含义参考【4.9.2】节：

NEW_TRANSACTION，

​                  REQUIRED_TRANSACTION，

​                  MAYBE_TRANSACTION，

​      NO_TRANSACTION

在这种规则下一旦方法执行时向外抛出异常，则会导致事务回滚，如果方法正常结束则事务提交。如果声明了回滚异常规则，则按照回滚异常规则控制事务的回滚和提交。

4. 通过模式指定控制事务的方法范围

<method pattern="testPatternTX[1-9.]*"

txtype="REQUIRED_TRANSACTION">

​    < param type="java.lang.String" />

</ method >

​       说明：通过pattern属性指定了一个模式testPatternTX[1-9.]*，表示以testPatternTX开头的所有方法都需要控制事务

​             Name属性和pattern属性只能设定一个，如果同时设定了两个属性，则只有pattern生效，name无效。

​             在这种规则下一旦方法执行时向外抛出异常，则会导致事务回滚，如果方法正常结束则事务提交。如果声明了回滚异常规则，则按照回滚异常规则控制事务的回滚和提交。

5. 指定事务的回滚异常

< method name="testSystemException" >   

​              < rollbackexceptions >

​                  <exception class="com.bboss.spi.transaction.RollbackInstanceofException"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions >

</ method >

或者

< method name="testSystemException" >   

​              < rollbackexceptions >

​                  <exception class="com.bboss.spi.transaction.RollbackInstanceofException"

​                  type="INSTANCEOF"/>

​              </ rollbackexceptions >

</ method >

说明：声明事务方法时，可以通过rollbackexceptions和exception元素指定方

法事务回滚的异常信息，包括异常名称，异常的范围类型：IMPLEMENTS，INSTANCEOF。异常名称通过class属性执行，范围类型通过type属性指定，只能设置IMPLEMENTS和INSTANCEOF这两个值。IMPLEMENTS类型指异常只能是异常本身的对象，其子类不属于回滚异常，INSTANCEOF类型指异常本身和其子类都属于回滚异常。

除了指定的回滚异常需要回滚事务外，如果抛出的异常是系统级别的异常，即使这些异常没有进行声明，也会导致事务回滚。系统级别的异常是指以下几种异常：

java.lang.RuntimeException及其子类

java.lang.Error及其子类

另外以下几种异常也是默认回滚事务的异常：

java.sql.SQLException

javax.transaction.RollbackException

com.frameworkset.orm.transaction.TransactionException