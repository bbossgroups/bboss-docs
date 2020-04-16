### 事件管理框架扩展

下载地址 https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=322981&release_id=683333

事件管理框架通过bboss aop框架来实现远程事件管理功能。新增了事件远程服务配置文件：

/bbossevent/src/event-service-assemble.xml

文件的内容如下：

< properties >

​       <!--

 定义可以进行远程通讯的网络节点结合，默认为all 

-->

​       <!--

 < property name="event.static-networks" value="172.16.17.254:1185" /> 

-->     

​       <!--

​           是否启用远程事件开关，如果没有启用远程事件或者系统的rpc服务没有开启，事件的处理情况如下：

​           本地事件发给当前服务器的本地事件监听器

​           远程事件发给当前服务器的本地远程事件监听器、远程事件监听器

​           本地远程事件发给当前服务器的本地事件监听器、本地远程事件监听器、远程事件监听器

​           如果启用远程事件和开启rpc服务的情况下：

​           本地事件发给当前服务器的本地事件监听器

​           远程事件发给所有服务器的本地远程事件监听器、远程事件监听器

​           本地远程事件发给当前服务器的本地事件监听器、所有服务器的本地远程事件监听器、所有服务器的远程事件监听器

​        -->

​       <property name=*"remoteevent.enabled"* value=*"true"*/>

​       <!-- 

​           /**

​            \* 指定缺省消息传播类型：

​            \* 本地传播(Event.LOCAL)

​            \* 远程传播(Event.REMOTE)

​            \* 本地远程传播 (Event.REMOTELOCAL)

​            */

​       -->

​       <property name=*"event.destinction.type"* value=*"Event.REMOTELOCAL"*/>

​       <property name=*"event.threadpool.corePoolSize"* value=*"5"*/>

​       <property name=*"event.threadpool.maximumPoolSize"* value=*"10"*/>

​       <property name=*"event.threadpool.keepAliveTime"* value=*"30"*/>

​       <!--

​       TimeUnit.SECONDS

​       TimeUnit.MICROSECONDS

​       TimeUnit.MILLISECONDS

​       TimeUnit.NANOSECONDS

​        -->

​       <property name=*"event.threadpool.keepAliveTimeUnit"* value=*"TimeUnit.SECONDS"*/>

​       <property name=*"event.threadpool.blockingQueue"* value=*"100"*/>

​       <property name=*"event.threadpool.waitTime"* value=*"1"*/>

​       <!--

​       RejectedExecutionHandler

​       必须实现java.util.concurrent.RejectedExecutionHandler接口

​       目前系统提供以下缺省实现：

​       com.chinacreator.thread.WaitPolicy  循环等待event.threadpool.waitTime指定的时间，单位为秒

​       java.util.concurrent.ThreadPoolExecutor.DiscardPolicy 直接丢弃任务，不抛出异常

​       java.util.concurrent.ThreadPoolExecutor.AbortPolicy 直接丢弃任务，抛出异常RejectedExecutionException

​       java.util.concurrent.ThreadPoolExecutor.CallerRunsPolicy 直接运行

​       java.util.concurrent.ThreadPoolExecutor.DiscardOldestPolicy 放入队列，将最老的任务删除

​        -->

​       <property name=*"event.threadpool.rejectedExecutionHandler"* value=*"com.chinacreator.thread.WaitPolicy"*/>

​    </ properties >

​    <manager id=*"event.serivce"* singlable=*"true"* >

​       <!--

​           事件管理远程服务

​           属性描述：

​           type：不同provider实现的标识

​           class：实现类代码    

​       -->

​       <provider type=*"default"* class=*"com.chinacreator.remote.EventRemoteServiceImpl" />*

​    </ manager >

上述文件的内容分为三部分：

第一部分

event.static-networks 如果有需要则定义远程事件默认发送的范围，前提是用户自己没有定义EventTarget对象

remoteevent.enabled 是否允许远程事件 true**允许，**false**不允许，所有的事件将在本地传播

第二部分

**event.threadpool.** 配置调度异步消息传输的线程池的相关信息*

第三部分 远程事务管理服务配置

<manager id=*"event.serivce"* singlable=*"true"* >

​       <!--

​           事件管理远程服务

​           属性描述：

​           type：不同provider实现的标识

​           class：实现类代码    

​       -->

​       <provider type=*"default"* class=*"com.chinacreator.remote.EventRemoteServiceImpl"* />

​    </ manager >

事务管理框架通过该服务来广播远程事件。从上述3部分的配置内容可以看出事务管理框架的核心功能：

a.增加是否启用远程事件开关，这样即使系统中开通了其他的远程服务，也不会影响事件处理框架

b.可以设定远程事件在集群中广播的范围

c. 扩展了远程事件发送的机制：

1.点对点事件发送（只支持Event.*REMOTE**事件*Event. *REMOTELOCAL*）

2.多个点的单播事件发送（支持Event.*REMOTE**事件*Event. *REMOTELOCA*）

3.集群组播事件发送(支持Event.*REMOTE**事件*Event. *REMOTELOCA*)

EventTarget target = **new** EventTarget("172.16.17.254",1185);//单点发送

​       Event event = **new** EventImpl("hello world type2 with target[" + target +"].",

​                            ExampleEventType.*type2withtarget*,

​                            target,

​                            Event.*REMOTE*);

​       EventHandle.*getInstance*().change(event);

通过定义不懂得target来实现不同的通讯模式：

EventTarget target = **new** EventTarget("172.16.17.254",1185);//单点发送

EventTarget target = **new** EventTarget("unicast:: 172.16.17.254:1186;172.16.17.254:1185");//多个点的单播事件发送

EventTarget target = **new** EventTarget("muticast:: 172.16.17.254:1186;172.16.17.254:1185",1185);// 集群组播事件发送

 支持异步事件发送功能

通过jdk 1.5提供的线程池，事件管理框架中对原有的异步事件处理的功能进行了很大的改进。线程池的属性借助于bbos aop框架的全局属性配置来实现。具体可以参考上文中关于属性定义的第二部分的内容。

EventHandle.*getInstance*().change(event);//默认为同步发送

EventHandle.*getInstance*().change(event,false);//异步发送事件，关于异步发送消息的相关配置，请参考博客文章

《**JDK1.5中的线程池(java.util.concurrent.ThreadPoolExecutor)使用简介**》