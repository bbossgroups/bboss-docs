### bboss aop 远程服务介绍-点对点远程服务调用和组播服务调用的区别

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

点对点远程服务调用和组播服务调用的区别主要在于

**1.**      **方法有返回值时其返回值不同。**

在实例中我们看到组件方法：

public  Object handle(){

​    return new Integer(1);

}

的返回值类型是Object，实际上返回的是一个Integer类型的对象。在点对点远程服务调用的测试方法中，我们直接将服务方法的返回值直接转换为Integer类型：

ServiceInf rpc = (test.ServiceInf)BaseSPIManager

​                .*getProvider*("(172.16.17.51:1185; 172.16.17.56:1185)/managerid");

​    Integer object = (Integer)rpc. handle();

但是在组播调用远程服务的测试方法中，不能这样处理，原因是发出请求的每台服务器都会有一个返回值，因此rpc. handle()调用的结果将是一个返回值的集合，如果需要获取特定服务器的返回值，必须通过以下方法来获取：

BaseSPIManager.*getRPCResult*(serverip, port, object);

Serverip参数对应服务器的ip，port参数对应服务器的端口，object参数为所有服务器的返回值的集合。

例如：

ServiceInf rpc = (test.ServiceInf)BaseSPIManager.*getProvider*("(172.16.17.51:1185; 172.16.17.56:1185)/managerid");

 Object object = rpc .handle();

Integer value = (Integer)BaseSPIManager.*getRPCResult*("172.16.17.56", "1185", object);

Integer value1 = (Integer)BaseSPIManager.*getRPCResult*("172.16.17.51", "1185", object);

**2.**         **配置远程组件时，对组播地址的配置要求不一样**

在远程管理组件的配置文件etc/META-INF/replSync-service-aop.xml，我们需要配置两个属性：

组播地址 mcast_addr="228.10.10.178"

绑定端口 bind_port="1185" 

每个服务器都可以配置自己的组播地址和绑定端口，如果两台服务器之间发出的所有远程服务请求都是点对点的方式发出的，那么组播地址 mcast_addr就可以配置成不相同的地址，当然相同的地址也可以（尽量不要配置成相同的组播地址）；如果服务器之间发出的远程服务请求只要有同时发出对多台服务器调用的情况时，就需要将组播地址mcast_addr配置成相同的地址。