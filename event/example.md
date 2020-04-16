### 事件框架使用实例

使用实例

10.6.1 定义事件类型

**public** **class** ExampleEventType **extends** AbstractEventType  {

​    **public** **static** EventType *type1* = **new** ExampleEventType("type1");

​    **public** **static** EventType *type2* = **new** ExampleEventType("type2");

​    **public** **static** EventType *type2withtarget* = **new** ExampleEventType("type2 with target");   

​    **public** ExampleEventType(String eventtype)

​    {

​       **super**.eventtype = eventtype;

​    }

} 

10.6.2定义事件监听器，实现事件处理方法并注册在事件激发器中

**public** **class** ExampleListener **implements** Listener{

​    /**

​     \* 注册监听器,监听ExampleEventType.type1和ExampleEventType.type2两种类型的事件

​     \* 事件消息可以是本地事件，可以是远程本地消息，也可以是远程消息

​     */

​    **public** **void** init()

​    {

​       List eventtypes = **new** ArrayList();

​       eventtypes.add(ExampleEventType.*type1*);

​       eventtypes.add(ExampleEventType.*type2*);

​       eventtypes.add(ExampleEventType.*type2withtarget*);

​       //监听器监听的事件消息可以是本地事件，可以是远程本地消息，也可以是远程消息

​       //如果不指定eventtypes则监听所有类型的事件消息

​       NotifiableFactory.*getNotifiable*().addListener(**this**, eventtypes);

​       /**

​        \* 只监听本地消息和本地发布的本地远程消息

​        \* NotifiableFactory.getNotifiable().addListener(this, eventtypes,Listener.LOCAL);

​        \* 只监听本地消息和从远程消息广播器发布的远程消息和远程本地消息

​        \* NotifiableFactory.getNotifiable().addListener(this, eventtypes,Listener.LOCAL_REMOTE);

​        \* 只监听从远程消息广播器发布的远程消息和远程本地消息

​        \* NotifiableFactory.getNotifiable().addListener(this, eventtypes,Listener.REMOTE);

​        */      

​    }

​    /**

​     \* 处理监听到的消息

​     */

​    **public** **void** handle(Event e) {

​       **if**(e == **null**)

​           **return**;

​       **if**(e.getType().equals(ExampleEventType.*type1*))

​       {

​           System.*out*.println("Receive event type is " + e.getType());          

​       }

​       **else** **if**(e.getType().equals(ExampleEventType.*type2*))

​       {

​           System.*out*.println("Receive event type is " + e.getType());

​       }

​       **else** **if**(e.getType().equals(ExampleEventType.*type2withtarget*))

​       {

​           System.*out*.println("Receive event type is " + e.getType() + " from "  + e.getEventTarget());

​       }

​       **else**

​       {

​           System.*out*.println("Unknown event type :" + e.getType());

​       }      

​    } 

} 

10.6.3 发布具体的事件消息 

**public** **class** ExampleEventPublish {

​    **public** **static** **void** publishEventtype1()

​    {

​       /**

​        \* 构建事件消息【hello world type2.】，指定了事件的类型为ExampleEventType.type2

​        \* 默认的事件消息广播途径为Event.REMOTELOCAL,你可以指定自己的事件广播途径

​        \*  消息只在本地传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的本地消息监听器和本地远程事件监听器

​         \* Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.LOCAL);

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器，同时也会直接发送给本地监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTELOCAL);

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTE);

​        */

​       Event event = **new** EventImpl("hello world type1.",ExampleEventType.*type1*);      

​       /**

​        \* 事件以同步方式传播

​        */      

​       EventHandle.*getInstance*().change(event);      

​    }   

​    **public** **static** **void** publishEventtype2()

​    {

​       /**

​      \* 构建事件消息【hello world type2.】，指定了事件的类型为ExampleEventType.type2

​        \* 默认的事件消息广播途径为Event.REMOTELOCAL,你可以指定自己的事件广播途径

​        \*  消息只在本地传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的本地消息监听器和本地远程事件监听器

​        \* Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.LOCAL);

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器，同时也会直接发送给本地监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTELOCAL);

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTE);

​        */   

​       Event event = **new** EventImpl("hello world type2.",ExampleEventType.*type2*);

​       /**

​        \* 消息以异步方式传递*/        EventHandle.*getInstance*().change(event,**false**);      

​    }   

​    **public** **static** **void** publishEventtype2Withtarget()

​    {

​       /**

​        \* 构建事件消息【hello world type2.】，指定了事件的类型为ExampleEventType.type2withtarget

​        \* 默认的事件消息广播途径为Event.REMOTELOCAL,你可以指定自己的事件广播途径

   

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器，同时也会直接发送给本地监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTELOCAL);

​        \*  消息在网络上传播，消息被发送给所有对ExampleEventType.type2类型消息感兴趣的远程消息监听器和本地远程事件监听器

​        \*  Event event = new EventImpl("hello world type2.",ExampleEventType.type2,Event.REMOTE);

​        */EventTarget target = **new** EventTarget("149.16.20.177",8088);

​       Event event = **new** EventImpl("hello world type2 with target[" + target +"].",

​                            ExampleEventType.*type2withtarget*,

​                            target,

​                            Event.*REMOTE*);EventHandle.*getInstance*().change(event);    }

}

10.6.4运行ExampleEventPublish中发布事件的方法

运行ExampleEventPublish中发布事件的方法，ExampleListener将会监听到其发布的事件，并且调用handle方法处理到监听到的消息。    

**public** **class** TestRun {

​    **public** **static** **void** main(String args[])

​    {

​       ExampleListener listener = **new** ExampleListener();

​       //调用init方法注册监听器，这样就能收到事件发布器发布的事件

​       listener.init();      

​       ExampleEventPublish.*publishEventtype1*();

​       ExampleEventPublish.*publishEventtype2*();

​       ExampleEventPublish.*publishEventtype2Withtarget*();    }

}


  