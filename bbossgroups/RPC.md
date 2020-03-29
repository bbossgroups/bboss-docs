### bbossgroups 2.0-RC版本中如何通过JGroups来实现集群节点间远程服务调用，或者多服务器之间远程服务调用

bbossgroups 2.0-RC中对jgroups已经升级到Jgroups 2.10.0版本，因此对aop中基于JGroups的rpc也做了相应的调整，本文详细讲解新的使用方法：

1.配置文件目录调整：

jgroups本身协议配置文件和存放目录（tcp，udp）

/bbossaop/resources/org/frameworkset/spi/jgroups/jgroups-tcp.xml

/bbossaop/resources/org/frameworkset/spi/jgroups/jgroups-udp.xml

manager-rpc-service.xml中针对JGroups的相应配置为：  

​      <!--

​        jgroups集群协议配置

​         -->

​        <**property name="cluster_name" value="Cluster"/>**

​        <**property name="cluster_protocol" value="udp"/>**

​        <**property name="cluster_protocol.tcp.configfile"  value="org/frameworkset/spi/jgroups/jgroups-tcp.xml"/>**

​        <**property name="cluster_protocol.udp.configfile" value="org/frameworkset/spi/jgroups/jgroups-udp.xml"/>**

2.JGroups协议单独启动方法没有改变，还是以下的方法：

RPCHelper.getRPCHelper().startJGroupServer();

基于bboss开发的平台中启动jgroups的方法，manager-rpc-service.xml中的相关项做如下设置即可：

<property name="rpc.default.protocol"

​      value="jgroup"/>

<property name="rpc.startup.protocols"

​      value="jgroup"/>  

<**property name="rpc.security">** 

<**map>**

<property name="rpc.login.module" enable="false" 

class="org.frameworkset.spi.security.SimpleLoginModule"/>

<property name="rpc.authority.module" enable="false" 

class="org.frameworkset.spi.security.SimpleAuthorityModule"/>

<property name="data.encrypt.module" enable="false" 

class="org.frameworkset.spi.security.SimpleEncryptModule"/>

<**/map>**

<**/property>**

每个节点应用的udp协议文件jgroups-udp.xml中的组播地址设置成一样的即可：

mcast_addr="232.10.10.10"

地址值可以根据需要进行修改。

3.客服端相关接口及使用方法

获取集群节点地址集合方法：

Vector<**Address>** addresses = JGroupHelper.getJGroupHelper().getAppservers();

获取远程服务组件方法：

​    \* 单播  

  Address address_ = addresses.get(0);

RPCTestInf testInf = (RPCTestInf)ApplicationContext.getApplicationContext().getBeanObject("(jgroup::" + address_ + ")/rpc.test");

调用远程方法：

Object value = testInf.getCount()；

   \* 多播

Address address_0 = addresses.get(0);

Address address_1 = addresses.get(1);

RPCTestInf testInf = (RPCTestInf)BaseSPIManager.getBeanObject("(jgroup::"+address_0+";"+address_0+")/rpc.test");  

调用远程方法：

Object value = testInf.getCount()；

获取address_0的结果：

Object ret_0 = BaseSPIManager.getRPCResult(address_0.toString(), value ,Target.BROADCAST_TYPE_JRGOUP);     

获取address_1的结果：

Object ret_1 = BaseSPIManager.getRPCResult("address_1.toString(), ret,Target.BROADCAST_TYPE_JRGOUP);

​    \* 组播

RPCTestInf testInf = (RPCTestInf)BaseSPIManager.getBeanObject("(jgroup::all)/rpc.test");   

  调用远程方法：

Object value = testInf.getCount()；

遍历所有结果：  

​         int size = BaseSPIManager.getRPCResultSize(ret);

​        for(int j = 0; j < size; j ++)

​        {

​            Object ret = BaseSPIManager.getRPCResult(j, ret);

​            System.out.println("ret:" + j + " = "+ret_1186);

​        }

