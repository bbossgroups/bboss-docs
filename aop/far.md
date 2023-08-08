### bboss aop 远程服务介绍-远程服务id定义规则

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

Bboss aop框架的业务组件既可以作为本地服务调用，又可以作为远程服务调用，那么怎么进行远程调用和本地调用呢，本小节就详细的进行说明。

我们进行服务调用时，首先要将提供服务的业务组件配置到bboss的部署描述文件中，这样就可以通过BaseSPIManager组件提供的getProvider方法获取相应组件的实例，然后进行方法调用了。例如：

A) ServiceInf rpc = (test.ServiceInf)

BaseSPIManager.*getProvider*("(172.16.17.56:1185)/managerid");

B) ServiceInf local = (test.ServiceInf)

BaseSPIManager.*getProvider*("managerid");

很明显情况A)获取的是主机172.16.17.56:1185上的服务实例，而B)则获取的是本地的服务实例。

从上面的示例我们能够看出，远程服务和本地服务的调用主要是通过*getProvider*方法的第一个参数serviceid来决定的。那么这个serviceid的定义规则到底是怎样的呢，下面来详细说明。

##### 本地服务id

本地服务id的规则很简单，就是部署描述文件中的配置的maanger 节点的id属性。例如：

<manager id="managerid"  //管理服务id

singlable="true" //单列模式

 \>

<provider type="provider_a"  //provider实现a

​           class="test.A" />

</ manager >

本地服务id就是managerid。

##### 远程服务id

远程服务id的规则如下:

   **(all|_self|ip:port[;ip:port])/serviceid**

说明：

- ​         **(all) /serviceid**

在集群中的所有服务器节点中调用远程服务**serviceid**。服务方法返回值必须是Object类型，如果要获取相应服务器的返回值，可以用以下的方式获取：

Object retvalue = rpc .handle();

Integer value = (Integer)BaseSPIManager.*getRPCResult*("172.16.17.56", "1185", retvalue);

- ​         **(ip:port) /serviceid**

单点调用远程服务**serviceid**。服务方法返回值可以是具体的类型，调用方法如下：

Integer value = (Integer) = rpc .handle();

这种模式需要说明的是，如果ip和port就是本地的ip地址和port，那么bboss aop就会将本次调用作为本地服务调用。

- ​         **(ip:port;ip1:port) /serviceid**

在集群中的指定的几个服务器节点中调用远程服务**serviceid**。服务方法返回值必须是Object类型，如果要获取相应服务器的返回值，可以用以下的方式获取：

Object retvalue = rpc .handle();

Integer value = (Integer)BaseSPIManager.*getRPCResult*("172.16.17.56", "1185", retvalue);

- ​         **(_self) /serviceid**

相当于本地服务调用。

##### 异常的处理

所有的远程调用的过程中抛出的异常，都会作为

com.bboss.remote.RemoteException类型的异常抛出。