### bboss aop 远程服务介绍-远程服务调用实例

##### 环境准备

准备三台服务器

- ​         服务器A

Ip 172.16.17.56

端口 1185 

- ​         服务器B

Ip 172.16.17.51

端口 1185

- ​         服务器C

Ip 172.16.17.52

端口 1185

##### 服务部署

假设我们已经定义了业务组件test.A和test.B，分别实现接口test.ServiceInf。服务中定义了方法handle(),返回值为Object,方法代码如下：

public  Object handle(){

​    return new Integer(1);

}

在A，B，C三台服务器上分别部署管理服务managerid,建立配置文件service-assemble.xml内容如下：

< manager-config >

<manager id="managerid"  //管理服务id

singlable="true" //单列模式

 \>

<provider type="provider_a"  //provider实现a

​           class="test.A" />

<provider type="provider_b" //provider实现b

​           class="test.B" />

< transactions >

< method name="handle" txtype="REQUIRED_TRANSACTION" />

</ transactions >

</ manager >

< manager-config > 

然后将service-assemble.xml导入到主配置文件manager-provider.xml文件：

< manager-config >   

​    <managerimport file=*"com/chinacreator/spi/rpc/service-assemble.xml"* />

</ manager-config >

在主配置文件manager-provider.xml中添加以下配置，作为远程管理组件启用的控制开关：

< manager-config >

​    < properties >

​       <property name=*"cluster_enable"* value=*"true"*/> //启用远程管理组件

​       <property name=*"cluster_mbean_enable"* value=*"true"*/> //启用远程管理组件的mbean监控功能，目前未使用

​       <property name=*"cluster_name"* value=*"Cluster"*/>  //远程管理组件名称

</ properties >

​    <managerimport file=*"com/chinacreator/spi/rpc/service-assemble.xml"* />

</ manager-config >

配置服务器间通讯端口和组播地址 

在远程管理组件的配置文件etc/META-INF/replSync-service-aop.xml

中修改bind_port属性：

bind_port=*"1185"*

修改组播地址：

mcast_addr="228.10.10.178"

##### 客服端程序编写

**package** com.chinacreator.spi.rpc;

**import** com.chinacreator.spi.BaseSPIManager;

**import** com.chinacreator.spi.SPIException; 

**public** **class** Test {

​    **public** **static** **void** testMutirpcCall() {

​        **try** {

​            ServiceInf rpc = (test.ServiceInf)BaseSPIManager

​                .*getProvider*("(172.16.17.51:1185; 172.16.17.56:1185)/managerid"); 

​            // RPCTest rpc = (RPCTest)BaseSPIManager.getProvider("managerid");

​            // RPCTest rpc =

​            // (RPCTest)BaseSPIManager.getProvider("(_self)/managerid");

​            // RPCTest rpc1 = (RPCTest)BaseSPIManager.getProvider(

​            // "(172.16.17.52: 1185;172.16.17.56: 1185)/managerid ");

​            // RPCTest rpc2 =

​            // (RPCTest)BaseSPIManager.getProvider("(all)/managerid");

​            **long** s = System.*currentTimeMillis*();

​            Integer object = (Integer)rpc. handle();

​                System.*out*.println(BaseSPIManager.*getRPCResult*("172.16.17.51", "1185", object));

​                System.*out*.println(BaseSPIManager.*getRPCResult*("172.16.17.56", "1185", object)); 

​            **long** e = System.*currentTimeMillis*(); 

​            System.*out*.println((e - s) / 1000 + "秒");          

​        } **catch** (SPIException e) {

​            // **TODO** Auto-generated catch block

​            e.printStackTrace();

​        }

​    }

  **public** **static** **void** testsinglerpcCall() {

​        **try** {

​            ServiceInf rpc = (test.ServiceInf)BaseSPIManager.*getProvider*("(172.16.17.56:1185)/managerid");         

​            **long** s = System.*currentTimeMillis*(); 

​           Object object = rpc. handle();              

​                System.*out*.println(object);                          

​            **long** e = System.*currentTimeMillis*(); 

​            System.*out*.println((e - s) / 1000 + "秒");          

​        } **catch** (SPIException e) {

​            // **TODO** Auto-generated catch block

​            e.printStackTrace();

​        }

​    }

  **public** **static** **void** testAllrpcCall() {

​        **try** {

​            ServiceInf rpc = (test.ServiceInf)BaseSPIManager.*getProvider*("(all)/managerid"); 

​            **long** s = System.*currentTimeMillis*();          

​                Object object = rpc .handle();

​                System.*out*.println(BaseSPIManager.*getRPCResult*("172.16.17.51", "1185", object));

​                System.*out*.println(BaseSPIManager.*getRPCResult*("172.16.17.52", "1185", object));

​                System.*out*.println(BaseSPIManager.*getRPCResult*("172.16.17.56", "1185", object));         

​            **long** e = System.*currentTimeMillis*();

​            System.*out*.println((e - s) / 1000 + "秒"); 

​        } **catch** (SPIException e) {         

​            e.printStackTrace();

​        }

​    }

}

##### 启动服务器

分别在每台服务器上运行类com.chinacreator.remote.RunAop的主方法，既可以启动服务器。

注意，客服端不要启动服务器。

##### 运行客服端

Test.*testAllrpcCall*();

​        Test.*testMutirpcCall*();

 Test.*testsinglerpcCall*();

更详细的信息请参考章节【BaseSPIProvider组件介绍中.实现远程服务调用】 