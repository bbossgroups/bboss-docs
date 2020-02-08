### bboss aop拦截器使用简介

bboss aop拦截器使用简介,本文适用于最新的bboss版本，下载方法参考：
http://yin-bp.iteye.com/blog/1080824

**1.概述**

bboss aop/ioc框架支持给组件配置一到多个拦截器，拦截器执行的顺序为类似堆栈的先入后出的模式，before方法按顺序执行（先入），其他方法采用逆序方式执行（后出，先执行最后一个拦截器的其他方法，最后执行第一个拦截器的其他方法）。

这里需要补充说明一下，声明式事务拦截器是bboss内置的一个拦截器，事务拦截器作为组件的所有拦截器中最后一个拦截器执行，同样遵循上面的先入后出原则。

**2.拦截器接口**

Java代码

```java
package com.frameworkset.proxy;  
import java.lang.reflect.Method;  
public interface Interceptor {  
    public void before(Method method,Object[] args) throws Throwable;  
    public void after(Method method,Object[] args) throws Throwable;  
    public void afterThrowing(Method method,Object[] args,Throwable throwable) throws Throwable;  
      
    public void afterFinally(Method method,Object[] args) throws Throwable;  
}  
```

Interceptor 接口定义了四个接口方法：

before-在方法执行之前执行，如果抛出异常，就会终止方法继续执行，同时将异常传递到应用层。

after-在方法执行之后执行，如果抛出异常，就会终止后续其他方法执行，同时将异常传递到应用层。

afterThrowing-当方法执行过程中抛出异常时执行，如果抛出异常，就会终止后续其他方法执行，同时将异常传递到应用层。

afterFinally-在方法finally后执行，如果抛出异常，就会终止后续其他方法执行，同时将异常传递到应用层。

**所以在实现自己的Interceptor 时，一定要注意异常的处理，因为拦截器方法的异常会影响组件方法的执行流程。**

**3.一个具体的拦截器**

Java代码

```java
package org.frameworkset.spi.properties.interceptor;  
  
import java.lang.reflect.Method;  
  
import com.frameworkset.proxy.Interceptor;  
public class InterceptorImpl implements Interceptor {  
    private A a;  
    public void after(Method method, Object[] args) throws Throwable {  
        System.out.println("Insterceptor.after(" + method.getName() + ", Object[] args)=" + args[0]);  
  
    }  
  
    public void afterFinally(Method method, Object[] args) throws Throwable {  
        System.out.println("Insterceptor.afterFinally(" + method.getName() + ", Object[] args)=" + args[0]);  
    }  
  
    public void afterThrowing(Method method, Object[] args, Throwable throwable)  
            throws Throwable {  
        System.out.println("Insterceptor.afterThrowing(" + method.getName() + ", Object[] args, Throwable throwable)=" + args[0]);  
  
    }  
  
    public void before(Method method, Object[] args) throws Throwable {  
        System.out.println("Insterceptor.before(" + method.getName() + ", Object[] args)=" + args[0]);  
  
  
    }  
}  
```

拦截器InterceptorImpl 中定义了一个全局变量：

private A a;

我们可以对拦截器应用依赖注入（ioc）机制来注入其他业务组件和属性，也就是说拦截器组件本身也是以组件的方式来管理，ioc的所有机制都可以应用于拦截器xml配置节点interceptor，下面具体介绍配置xml元素-interceptor的用法。

**4.拦截器配置xml元素-interceptor**

interceptor元素作为组件定义元素property的内置元素使用，用来配置组件的aop拦截器。interceptor元素内置method元素，用来配置组件中需要被该拦截器拦截的具体方法，如果没有配置method元素，则对应的拦截器将拦截组件中所有的方法。如果组件配置了多个拦截器，每个拦截器都可以配置自己需要拦截的具体方法。

下面是一个配置实例：

Xml代码

```xml
<properties>  
    <property name="test.interceptorbean" singlable="true" class="org.frameworkset.spi.properties.interceptor.A">  
        <interceptor class="org.frameworkset.spi.properties.interceptor.InterceptorImpl"           
            f:a="attr:test.bean"  
        />         
    </property>  
      
    <property name="test.interceptorbeanmethod" singlable="true" class="org.frameworkset.spi.properties.interceptor.A">  
        <interceptor class="org.frameworkset.spi.properties.interceptor.InterceptorImpl"           
            f:a="attr:test.bean"  
        >  
            <method name="test">  
                <param type="java.lang.String"/>  
            </method>           
        </interceptor>          
    </property>  
      
    <property name="test.bean" singlable="true" class="org.frameworkset.spi.properties.interceptor.A">  
    </property>  
</properties>  
```

说明：

情形一

**<interceptor class="org.frameworkset.spi.properties.interceptor.InterceptorImpl"**

**f:a="attr:test.bean"**

**/**>

这种配置拦截所有方法，同时我们为拦截器中的属性a注入了另外一个组件实例。

情形二
<interceptor class="org.frameworkset.spi.properties.interceptor.InterceptorImpl"

f:a="attr:test.bean">

**<method name="test"**>

**<param type="java.lang.String"**/>

**</method**>

**</interceptor**>

这里通过method元素指定了拦截器需要拦截的组件方法，可以配置多个：

**<method name="test"**>

**<param type="java.lang.String"/**>

**</method**>

  如果是具体的方法就需要指定方法的参数，如果是采用pattern（正则表达式）则无需指定方法参数，例如：

**<method pattern="test.*"/**>//表示拦截所有以test开头的方法，这里遵循标准的java正则表达式语法

**<method pattern="*"/**>//表示拦截所有方法，这里遵循标准的java正则表达式语法  

情形三

Xml代码 

```xml
<interceptor class="org.frameworkset.spi.properties.interceptor.PermissioncheckInterceptorImpl"            
            f:a="attr:test.bean"  
        />  
<interceptor class="org.frameworkset.spi.properties.interceptor.LogInterceptorImpl"            
            f:a="attr:test.bean"  
        />  
```

**5.内置的声明式事务拦截器配置需要拦截的事务方法配置示例**

借助transactions元素和method来进行声明式事务方法配置。transactions和interceptor元素不一样，当transactions中没有配置method时，事务拦截器不会拦截组件的任何方法;而在transactions元素中method方法可以通过txtype属性指定方法需要开启的事务类型，并且在method元素中还可以通过rollbackexceptions和exception元素结合配置需要回滚事务的异常信息。下面是transactions的配置实例：

Xml代码 

```xml
<property id="tx.a" singlable="true" class="org.frameworkset.spi.transaction.A1" >  
        <!--    
            在下面的节点对组件的业务方法事务进行定义  
            只要将需要进行事务控制的方法配置在transactions中即可  
        -->  
        <transactions>  
            <!--   
                定义需要进行事务控制的方法  
                属性说明：  
                name-方法名称，可以是一个正则表达式，正则表达式的语法请参考jakarta-oro的相关文档，如果使用  
                正则表达式的情况时，则方法中声明的方法参数将被忽略，但是回滚异常有效。  
                pattern-方法名称的正则表达式匹配模式，模式匹配的顺序受配置位置的影响，如果配置在后面或者中间，  
                        那么会先执行之前的方法匹配，如果匹配上了就不会对该模式方法进行匹配了，否则执行匹配操作。  
                        如果匹配上特定的方法名称，那么这个方法就是需要进行事务控制的方法  
                        例如：模式testInt.*匹配接口中以testInt开头的任何方法  
                txtype-需要控制的事务类型，取值范围：  
                NEW_TRANSACTION，  
                REQUIRED_TRANSACTION，  
                MAYBE_TRANSACTION，  
                NO_TRANSACTION  
                RW_TRANSACTION  
            -->  
            <method name="testTXInvoke" txtype="NEW_TRANSACTION">  
                <param type="java.lang.String"/>  
            </method>           
            <method name="testTXInvoke" txtype="REQUIRED_TRANSACTION"/>             
            <method name="testTXInvokeWithReturn" txtype="REQUIRED_TRANSACTION"/>           
            <method name="testTXInvokeWithException" txtype="MAYBE_TRANSACTION"/>               
            <method name="testSameName" txtype="NO_TRANSACTION"/>           
            <method name="testSameName1">  
                <param type="java.lang.String"/>  
            </method>  
            <!--                   
                    定义使事务回滚的异常,如果没有明确声明需要回滚事务的异常，那么当有异常发生时，  
                    事务管理框架将自动回滚当前事务  
                    class-异常的完整类路径  
                    type-是否检测类型异常的子类控制标识，  
                    IMPLEMENTS只检测异常类本身，忽略异常类的子类；  
                    INSTANCEOF检查异常类本省及其所有子类                   
                -->  
            <method name="testTXWithSpecialExceptions">  
                <rollbackexceptions>  
                    <exception class="org.frameworkset.spi.transaction.RollbackInstanceofException"   
                    type="INSTANCEOF"/>  
                    <exception class="org.frameworkset.spi.transaction.Exception1"   
                    type="IMPLEMENTS"/>  
                </rollbackexceptions>  
                <param type="java.lang.String"/>  
            </method>           
            <method name="testTXWithInstanceofExceptions">  
                <rollbackexceptions>  
                    <exception class="org.frameworkset.spi.transaction.RollbackInstanceofException"   
                    type="INSTANCEOF"/>  
                </rollbackexceptions>  
                <param type="java.lang.String"/>  
            </method>           
            <method name="testTXWithImplementsofExceptions">  
                <rollbackexceptions>  
                    <exception class="org.frameworkset.spi.transaction.RollbackInstanceofException"   
                    type="IMPLEMENTS"/>  
                </rollbackexceptions>  
                <param type="java.lang.String"/>  
            </method>  
            <!--   
                      
                    如果涉及的方法名称是一个正则表达式的匹配模式，则无需配置方法参数   
                    如果指定的方法没有参数则无需指定参数  
                    注意参数出现的位置顺序和实际方法定义的参数顺序保持一致  
                    模式为* 表示匹配所有的方法  
                -->                
            <!--   
                通过模式方法进行声明式事务控制，同时声明了需要回滚事务的异常  
                pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法  
             -->            
            <!--<method pattern="testPatternTX[1-9.]*">  
                <rollbackexceptions>  
                    <exception class="org.frameworkset.spi.transaction.RollbackInstanceofException"   
                    type="IMPLEMENTS"/>  
                </rollbackexceptions>  
                <param type="java.lang.String"/>  
            </method>-->              
            <!-- 通过模式方法进行声明式事务控制  
                pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法          
                 -->   
            <method pattern="testPatternTX[1-9.]*"/>                
                              
            <!--  
                系统级别的异常java.lang.NullPointException，导致事务回滚 
             -->  
            <method name="testSystemException">     
                <rollbackexceptions>  
                    <exception class="org.frameworkset.spi.transaction.RollbackInstanceofException"   
                    type="IMPLEMENTS"/>  
                </rollbackexceptions>  
            </method>  
        </transactions>  
    </property>  
```

  说明：transactions和interceptor元素一起使用，这时transactions将会作为最后一个拦截器执行。

ok，bboss的所有拦截器机制和示例就介绍到此。  

