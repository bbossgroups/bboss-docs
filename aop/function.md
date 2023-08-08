### bboss aop 实践 (3-1) 构造函数依赖注入

   </ method >    

​       </ synchronize >

​       < interceptor class="com.bboss.spi.interceptor.Insterceptor" />

​       < interceptor class="com.bboss.spi.interceptor.Insterceptor1" />

​      

​       <!-- 

​           在下面的节点对provider的业务方法事务进行定义

​           只要将需要进行事务控制的方法配置在transactions中即可

​       -->

​       < transactions >

​           < method name="testInterceptorsBeforeafterWithTX" />

​           < method name="testInterceptorsBeforeAfter" />

​           < method name="testInterceptorsBeforeThrowing" /> 

​           < method name="testInterceptorsBeforeThrowingWithTX" >           

​           </ method >        

​       </ transactions >

​    </ manager >

</ manager-config >

##### 配置完毕后，使用业务组件 

**import** com.bboss.spi.BaseSPIManager;

**import** com.bboss.spi.SPIException; 

**public** **class** TestConstructor {

​    **public** **static** **void** testConstructor()

​    {

​       **try** {

​           ConstructorInf a = (ConstructorInf)BaseSPIManager.*getProvider*("constructor.a");    

​              a.testHelloworld();

​       } **catch** (SPIException e) {

​           // **TODO** Auto-generated catch block

​           e.printStackTrace();          

​       }

​    }   

​    **public** **static** **void** main(String[] args)

​    {

​       *testConstructor*();

​    }

}