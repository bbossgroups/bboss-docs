### Event组件接口说明

#### 10.5.1监听器接口

组件接口

com.bboss.event.Listener

方法：

public void handle(Event e);   

说明：事件监听器通过本方法来处理监听到的事件消息。由具体的监听器实现类来实现这个方法。

使用实例： 

**public** **void** handle(Event e) {

​       Object source = e.getSource();//获取事件消息源

​       **if**(e.getType().equals( ACLEventType.*ROLE_INFO_CHANGE*))//判断事件类型

​       { 

​           //进行特殊处理

​       } 

}   

#### 10.5.2注册监听器接口

注册事件监听器需要的相关组件接口说明如下：

##### 10.5.2.1 com.bboss.event.NotifiableFactory

 

**方法：**

public static Notifiable getNotifiable()

说明：通过NotifiableFactory提供的静态方法获取到一个通用激发器对象

​      然后在这个激发器对象上注册所有监听器 

##### 10.5.2.2 com.bboss.event.Notifiable

- ​        **方法** **1**：

public void addListener(Listener listener) 

说明：注册监听器，监听所有类型的本地事件和远程本地事件

实例：

Listener listener = new ExampleListener();

NotifiableFactory.getNotifiable().addListener(listener); 

- ​        **方法2：**

public void addListener(Listener listener,boolean remote)

说明：

注册监听器，根据参数remote的值监听不同传播方式的消息，

True-监听远程消息和本地远程消息以及远程消息。

False-监听本地消息和本地远程消息

实例：

Listener listener = new ExampleListener();

NotifiableFactory.getNotifiable().addListener(listener,false); 

- ​         **方法3：**

public void addListener(Listener listener,List eventtypes);

说明：

注册监听器，监听远程消息，本地消息，本地远程消息，只监听eventtypes列表中列出的消息类型的消息。

实例：

List eventTypes = **new** ArrayList();

eventTypes.add(CMSEventType.*EVENT_SITE_ADD*);

eventTypes.add(CMSEventType.*EVENT_SITE_DELETE*);

eventTypes.add(CMSEventType.*EVENT_SITE_UPDATE*);     

eventTypes.add(CMSEventType.*EVENT_SITESTATUS_UPDATE*);      

eventTypes.add(CMSEventType.*EVENT_CHANNEL_MOVE*);

Listener listener = new ExampleListener();   

NotifiableFactory.getNotifiable().addListener(listener,eventType); 

- ​        **方法4：**

public void addListener(Listener listener,List eventtypes,boolean remote);

说明：注册监听器，remote为true时监听远程消息、本地消息、本地远程消息，为false时监听本地消息、本地远程消息，同时只监听eventtypes列表中列出的消息类型的消息 

实例：

List eventTypes = **new** ArrayList();

//设置监听器需要监听的事件类型

eventTypes.add(CMSEventType.*EVENT_SITE_ADD*);

eventTypes.add(CMSEventType.*EVENT_SITE_DELETE*);

eventTypes.add(CMSEventType.*EVENT_SITE_UPDATE*);     

eventTypes.add(CMSEventType.*EVENT_SITESTATUS_UPDATE*);      

eventTypes.add(CMSEventType.*EVENT_CHANNEL_MOVE*);

Listener listener = new ExampleListener();   

NotifiableFactory.getNotifiable().addListener(listener,eventTypes,false);  

- ​         **方法5：**

public void addListener(Listener listener,List eventtypes,int listenerType);

说明：

注册监听器，监听eventtypes中列出的对应消息类型的事件消息，根据listenerType的值来决定监听的不同传播途径的事件消息。listenerType的取值范围如下：

/**

​     \* 本地事件消息和本地远程事件西欧系监听器，

​     */

​    **public** **static** **final** **int** Listener.*LOCAL* = 0;

​    /**

​     \* 本地远程事件和远程事件监听器

​     */

​    **public** **static** **final** **int** Listener.*REMOTE* = 1;   

​    /**

​     \* 本地和远程事件及本地远程事件监听器

​     */

​    **public** **static** **final** **int** Listener.*LOCAL_REMOTE* = 2;

使用实例：

List eventTypes = **new** ArrayList();

//设置监听器需要监听的事件类型

eventTypes.add(CMSEventType.*EVENT_SITE_ADD*);

eventTypes.add(CMSEventType.*EVENT_SITE_DELETE*);

eventTypes.add(CMSEventType.*EVENT_SITE_UPDATE*);     

eventTypes.add(CMSEventType.*EVENT_SITESTATUS_UPDATE*);      

eventTypes.add(CMSEventType.*EVENT_CHANNEL_MOVE*);

Listener listener = new ExampleListener();   

NotifiableFactory.getNotifiable().addListener(listener,eventTypes, Listener.*LOCAL_REMOTE*); 

- ​         **方法6：**

public void addListener(Listener listener,int listenerType);

说明：注册监听器，该监听器监听所有类型的事件消息，listenerType参数用来指定监听器需要监听的消息的传播方式，也就是说，监听器只监听以listenerType指定的传播方式传播的事件消息。listenerType的取值范围如下：

/**

​     \* 本地事件消息和本地远程事件西欧系监听器，

​     */

​    **public** **static** **final** **int** Listener.*LOCAL* = 0;

​    /**

​     \* 本地远程事件和远程事件监听器

​     */

​    **public** **static** **final** **int** Listener.*REMOTE* = 1;   

​    /**

​     \* 本地和远程事件及本地远程事件监听器

​     */

​    **public** **static** **final** **int** Listener.*LOCAL_REMOTE* = 2;

使用实例：

Listener listener = new ExampleListener();   

NotifiableFactory.getNotifiable().addListener(listener, Listener.*LOCAL_REMOTE*);

#### 10.5.3事件消息发布接口

与事件发布相关的组件接口如下：

com.bboss.event.EventHandle

com.bboss.event.EventImpl

com.bboss.event.EventType

com.bboss.event.EventTarget

com.bboss.remote.Utils 

EventHandle是一个抽象类封装了所有事件广播（事件本地广播和事件远程广播）的所有功能。EventHandle分发事件消息可以同步分发消息也可以通过异步的方式发布消息。

EventImpl是事件消息的封装对象，封装的信息包括：

- ​         事件源信息，也就是事件需要广播的消息数据，是一个java.lang.Object类型的对象，当消息作为远程消息传递时消息对象必须实现java.io.Serializable接口，。

- ​        事件类型，所有的事件消息都需要指定一个事件类型，这样才会被特定的事件监听器所接收

- ​         事件消息广播方式，指定事件的传播途径，分为3种方式：

本地传播(Event.LOCAL) 只将相应的事件消息在本地应用中传播，发给本地监听器和本地远程监听器

远程传播(Event.REMOTE) 事件消息传递给远程服务器中的远程监听器和远程本地监听器（远程服务器也包括本地服务器，因为本地服务器也启动了远程消息监听器）

本地远程传播 (Event.REMOTELOCAL)事件消息除了传递给本地监听器，也会在远程服务器之间传播，远程服务器上的本地远程监听器和远程监听器会接收到这个消息。

- ​         事件消息的目标地址，包含目标地址ip和端口。当指定的消息为REMOTE和REMOTELOCAL这两种类型时，才能指定目标地址，如果对应的target不存在，那么这个事件消息就不会在服务器之间传播。如果指定目标地址，那么该消息就会直接传递给该地址对应的应用服务器上的监听器，否则就会广播给所有的远程服务器上的相关的事件监听器 。

EventTarget封装事件发送目标地址，包括ip和端口两个信息。只有事件的eventBroadcastType为远程传播(Event.REMOTE)和 本地远程传播 (Event.REMOTELOCAL)两种类型时，才可以指定target属性，如果target为null，那么事件将被广播到所有的远程节点上面的监听器，否则只广播到target指定的目的地址对应的远程监听器，如果对应的target不存在，那么直接丢弃这个事件消息。 

com.bboss.remote.Utils:EventHandle组件采用Utils组件来广播远程消息。Utils中采用JGroups组播通讯控件来实现事件消息在远程服务器之间广播。具体消息的远程广播机制请参考系统集群配置说明一节。