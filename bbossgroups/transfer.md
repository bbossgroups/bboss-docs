### bbossgroups 组件方法异步调用

bbossgroups-3.1 支持组件方法异步调用，本文介绍一下aop框架中的组件方法异步调用功能的特性，提供了对组件方法异步执行的几乎所有模式。

**1.异步机制**

Bbossgroup 3.1版本中新增了组件异步调用功能，大致的机制如下：

是否需要返回调用结果，默认不返回，主线程继续往前走（真正的异步）

如果需要返回则，根据timeout和callback两个参数来决定

返回结果的等待处理模式：

当timeout > 0 则等待特定的时间来来获取结果，超过指定的时间后就抛超时异常，等待超时的模式又分为两种情况：

如果指定了回调函数，不阻塞主程序，将结果交给回调函数来处理

如果没有指定回调函数则阻塞主程序，将结果交给主程序来处理

当timeout <= 0 时，则永久等待结果，直到结果返回，这种模式也分两种情况：

如果指定了回调函数 则不阻塞主程序，

如果没有指定回调函数，则阻塞主程序，直到结果返回来

**2.六种异步模式**

模式一 纯异步模式-不需要等待返回结果，不需要回调的异步模式，不阻塞主调程序对应方法的Async注解使用方式为：

Java代码

```java
@Async  
    public String testHelloworld(String message) {  
          
        System.out.println(message);  
        return "testHelloworld:"+message;  
          
    }  
```

模式二 等待超时的异步模式-不需要等待返回结果，不需要回调，但是指定了异步执行超时时间的异步模式，不阻塞主调程序

对应方法的Async注解使用方式为：

Java代码

```java
@Async(timeout=5000)  
    public String testHelloworld0(String message) {  
          
        System.out.println(message);  
        return "testHelloworld:"+message;  
          
    }  
```

模式三 等待结果的异步模式-方法异步执行，但是调用方会一直等待执行结果，阻塞主调程序

对应方法的Async注解使用方式为：

Java代码

```java
@Async(result=Result.YES)  
    public String testHelloworld3(String message) {  
          
        System.out.println(message);  
        return "testHelloworld3:"+message;  
    }  
```

模式四 超时等待结果的异步模式-方法异步执行，但是调用方会一直等待执行结果，直到超过指定时间，抛出TimeoutException，阻塞主调程序

对应方法的Async注解使用方式为：

Java代码

```java
@Async(timeout=5000,result=Result.YES)  
public String testHelloworld1(String message) {  
      
    System.out.println(message);  
    return "testHelloworld1:"+message;  
      
}  
```

模式五 执行结果交给回调处理函数的异步处理模式，不阻塞主调程序

对应方法的Async注解使用方式为：

Java代码

```java
@Async(result=Result.YES,callback="asyn.AsynbeanCallBackTest")  
    public String testHelloworld4(String message) {  
          
        System.out.println(message);  
        return "testHelloworld4:"+message;  
          
    }  
```

模式六 执行结果交给回调处理函数的超时异步处理模式，不阻塞主调程序

对应方法的Async注解使用方式为：

Java代码

```java
@Async(timeout=5000,result=Result.YES,callback="asyn.AsynbeanCallBackTest")  
    public String testHelloworld2(String message) {  
          
        System.out.println(message);  
        return "testHelloworld2:"+message;  
          
    }  
```

**3.组件异步方法注解**

org.frameworkset.spi.async.annotation. Async

其作用就是标识需要异步执行的方法以及设定异步执行的相关参数。

可以指定以下属性：

timeout：指定结果等待调用超时时间，默认为-1（不用超时），只有

Callback：指定异步调用结果处理回调组件名字

Result：异步调用时是否需要将结果返回给主调程序或者是交个回调函数处理结果

**4 异步回调接口**

Java代码

```java
package org.frameworkset.spi.async;  
public interface CallBack<T> {  
    public void handleResult(T result);  
    public void handleError(Throwable result);  
}  
```

**5 异步组件实现**

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
  
package org.frameworkset.spi.asyn;  
  
import org.frameworkset.spi.async.annotation.Async;  
import org.frameworkset.spi.async.annotation.Result;  
  
/** 
 * <p>Title: AsynbeanTest.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-4-20 下午05:18:54 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class AsynbeanTest {  
    @Async  
    public String testHelloworld(String message) {  
          
        System.out.println(message);  
        return "testHelloworld:"+message;  
          
    }  
      
    /** 
     * 5秒超时，但是不返回结果，也不指定回调函数（这种模式没有实际意义，只是在调用的时候超过5秒 
     * 后给出超时异常） 
     * @param message 
     * @return 
     */  
    @Async(timeout=5000)  
    public String testHelloworld0(String message) {  
          
        System.out.println(message);  
        return "testHelloworld:"+message;  
          
    }  
      
    /** 
     * 需要返回结果，等10秒超时 
     * @param message 
     */  
    @Async(timeout=5000,result=Result.YES)  
    public String testHelloworld1(String message) {  
          
        System.out.println(message);  
        return "testHelloworld1:"+message;  
          
    }  
    @Async(timeout=5000,result=Result.YES,callback="asyn.AsynbeanCallBackTest")  
    public String testHelloworld2(String message) {  
          
        System.out.println(message);  
        return "testHelloworld2:"+message;  
          
    }  
      
      
    @Async(result=Result.YES)  
    public String testHelloworld3(String message) {  
          
        System.out.println(message);  
        return "testHelloworld3:"+message;  
    }  
      
      
    @Async(result=Result.YES,callback="asyn.AsynbeanCallBackTest")  
    public String testHelloworld4(String message) {  
          
        System.out.println(message);  
        return "testHelloworld4:"+message;  
          
    }  
  
}  
```

**6 异步回调组件定义**

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
  
package org.frameworkset.spi.asyn;  
  
import org.frameworkset.spi.async.CallBack;  
  
/** 
 * <p>Title: AsynbeanCallBackTest.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-4-21 下午06:39:52 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class AsynbeanCallBackTest implements CallBack {  
  
    public void handleResult(Object result) {  
        System.out.println(result);  
          
    }  
  
    public void handleError(Throwable result) {  
        result.printStackTrace();  
          
    }  
  
}  
```

**7 异步组件和回调组件配置**

Xml代码

```xml
<properties>  
        <!--  
            异步调用处理服务组件 
         -->  
        <property name="asyn.AsynbeanTest"   
                          class="org.frameworkset.spi.asyn.AsynbeanTest"/>  
        <property name="asyn.AsynbeanCallBackTest"   
                          class="org.frameworkset.spi.asyn.AsynbeanCallBackTest"/>                         
</properties>  
```

**8 异步组件测试用例**

Java代码

```java
/* 
 *  Copyright 2008 biaoping.yin 
 * 
 *  Licensed under the Apache License, Version 2.0 (the "License"); 
 *  you may not use this file except in compliance with the License. 
 *  You may obtain a copy of the License at 
 * 
 *      http://www.apache.org/licenses/LICENSE-2.0 
 * 
 *  Unless required by applicable law or agreed to in writing, software 
 *  distributed under the License is distributed on an "AS IS" BASIS, 
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 *  See the License for the specific language governing permissions and 
 *  limitations under the License. 
 */  
  
package org.frameworkset.spi.asyn;  
  
import org.frameworkset.spi.ApplicationContext;  
import org.junit.Before;  
import org.junit.Test;  
  
/** 
 * <p>Title: TestRun.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2011-4-21 下午06:46:37 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestRun {  
    private ApplicationContext context ;  
    @Before  
    public void init()  
    {  
        context = ApplicationContext.getApplicationContext("org/frameworkset/spi/asyn/asyn.xml");  
    }  
    @Test  
    public void testAsync()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        for(int i = 0; i < 10 ; i++)  
            test.testHelloworld("Async call.");  
        System.out.println("runned.");  
    }  
      
      
    @Test  
    public void testTimeout5000WithResult()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        System.out.println(test.testHelloworld1("Async call Timeout 5000ms with Result."));  
        System.out.println("runned.");  
    }  
      
    @Test  
    public void testTimeout5000NoResult()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        System.out.println(test.testHelloworld0("Async call Timeout 5000ms No Result."));  
        System.out.println("runned.");  
    }  
      
    @Test  
    public void testTimeout5000WithCallBackService()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        System.out.println(test.testHelloworld2("Async call Timeout 5000 With CallBackService."));  
        System.out.println("runned.");  
    }  
    @Test  
    public void testWithCallBackService()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        System.out.println(test.testHelloworld4("Async call With CallBackService."));  
        System.out.println("runned.");  
    }  
      
    @Test  
    public void testResult()  
    {  
        AsynbeanTest test = (AsynbeanTest)context.getBeanObject("asyn.AsynbeanTest");  
        System.out.println(test.testHelloworld3("call With"));  
        System.out.println("runned.");  
    }  
      
      
      
  
}  
```



