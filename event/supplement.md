### 远程事件配置补充说明

下载地址

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=322981&release_id=683333

manager-provider.xml

位于classpath根目录，主要有以下功能：

a.加载事件管理服务配置文件event-service-assemble.xml

< managerimport file="event-service-assemble.xml"  />

b.配置底层远程通讯功能

< properties >

​       < property name="cluster_enable" value="true" /><!--

 是否开启远程功能 

-->

​       < property name="cluster_mbean_enable" value="false" />

​       < property name="cluster_name" value="Cluster" /><!-- 

通讯组名称，用于集群环境 

-->

​    </ properties >

event-service-assemble.xml

第一部分

event.static-networks 如果有需要则定义远程事件默认发送的范围，前提是用户自己没有定义EventTarget对象

remoteevent.enabled 是否允许远程事件 true允许，false不允许，所有的事件将在本地传播

第二部分

event.threadpool.* 配置调度异步消息传输的线程池的相关信息

第三部分 远程事务管理服务配置

< manager id="event.serivce" singlable="true"  >

​       <!--

​           事件管理远程服务

​           属性描述：

​           type：不同provider实现的标识

​           class：实现类代码

​          

​       -->

​       < provider type="default" class="com.bboss.remote.EventRemoteServiceImpl"  />

​    </ manager >

事务管理框架通过该服务来广播远程事件。从上述3部分的配置内容可以看出事务管理框架的核心功能：

a. 增加是否启用远程事件开关，这样即使系统中开通了其他的远程服务，也不会影响事件处理框架

b.可以设定远程事件在集群中广播的范围

c.扩展了远程事件发送的机制：

1.点对点事件发送（只支持Event.REMOTE事件Event. REMOTELOCAL）

2.多个点的单播事件发送（支持Event.REMOTE事件Event. REMOTELOCA）

3.集群组播事件发送(支持Event.REMOTE事件Event. REMOTELOCA)

EventTarget target = **new** EventTarget("172.16.17.254",1185);//单点发送

​       Event event = **new** EventImpl("hello world type2 with target[" + target +"].",

​                            ExampleEventType.type2withtarget,

​                            target,

​                            Event.REMOTE);

 

​       EventHandle.getInstance().change(event);

通过定义不懂得target来实现不同的通讯模式：

EventTarget target = **new** EventTarget("172.16.17.254",1185);//单点发送

EventTarget target = **new** EventTarget("unicast:: 172.16.17.254:1186;172.16.17.254:1185");//多个点的单播事件发送

EventTarget target = **new** EventTarget("muticast:: 172.16.17.254:1186;172.16.17.254:1185",1185);// 集群组播事件发送

 

支持异步事件发送功能

通过jdk 1.5提供的线程池，事件管理框架中对原有的异步事件处理的功能进行了很大的改进。线程池的属性借助于bbos aop框架的全局属性配置来实现。具体可以参考上文中关于属性定义的第二部分的内容。

EventHandle.getInstance().change(event);//默认为同步发送

EventHandle.getInstance().change(event,false);//异步发送事件，关于异步发送消息的相关配置，请参考博客文章

**《JDK1.5中的线程池(java.util.concurrent.ThreadPoolExecutor)使用简介》**

#### replSync-service-aop.xml

远程通讯协议配置文件，主要配置jgroups通讯协议，位于classpath的etc/META-INF/目录下，主要的配置内容如下：

mcast_addr="232.10.10.10" 组播地址

mcast_port="45588" 组播端口

bind_port="1185" 远程通讯端口，对应于

EventTarget target = **new** EventTarget("unicast:: 172.16.17.254:1186;172.16.17.254:1185");

中的端口。