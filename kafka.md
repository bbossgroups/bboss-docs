## bboss kafka组件使用介绍

本文使用的实例对应的gradle源码工程git访问地址：
http://git.oschina.net/bboss/bestpractice
testkafka子工程地址
http://git.oschina.net/bboss/bestpractice/tree/master/testkafka
bboss kafka组件作用

- 快速配置kafka客户端和消费者

- 发送数据到kafka

- 从kafka接收和处理数据（支持批量消息处理和按条处理)

  ### 1.导入bboss kafka组件

  maven坐标
  Xml代码

  ```xml
  <dependency>  
      <groupId>com.bbossgroups.plugins</groupId>  
      <artifactId>bboss-plugin-kafka</artifactId>  
      <version>5.1.2</version>  
  </dependency>
  ```

  **gradle坐标**

  Java代码

  ```java
  compile 'com.bbossgroups.plugins:bboss-plugin-kafka:5.1.2' 
  ```

  ### 2.使用kafka producer，发送消息

  #### 2.1 kafka producer配置

  编写kafka.xml配置文件，放到classpath跟路径下面

  Xml代码

  ```xml
  <properties>  
      <property name="productorPropes">  
          <propes>  
                
              <property name="value.serializer" value="org.apache.kafka.common.serialization.StringSerializer">  
                  <description> <![CDATA[ 指定序列化处理类，默认为kafka.serializer.DefaultEncoder,即byte[] ]]></description>  
              </property>  
              <property name="key.serializer" value="org.apache.kafka.common.serialization.LongSerializer">  
                  <description> <![CDATA[ 指定序列化处理类，默认为kafka.serializer.DefaultEncoder,即byte[] ]]></description>  
              </property>  
                        
              <property name="compression.type" value="gzip">  
                  <description> <![CDATA[ 是否压缩，默认0表示不压缩，1表示用gzip压缩，2表示用snappy压缩。压缩后消息中会有头来指明消息压缩类型，故在消费者端消息解压是透明的无需指定]]></description>  
              </property>  
              <property name="bootstrap.servers" value="hadoop85:9092,hadoop86:9092,hadoop88:9092">  
                  <description> <![CDATA[ 指定kafka节点列表，用于获取metadata(元数据)，不必全部指定]]></description>  
              </property>  
          </propes>  
      </property>  
          <property name="workerThreadSize" value="100"/>  
          <property name="workerThreadQueueSize" value="10240"/>  
      <property name="kafkaproductor"   
          class="org.frameworkset.plugin.kafka.KafkaProductor"  
          init-method="init"  
          f:sendDatatoKafka="true"  
  f:sendAsyn="true"  
          f:productorPropes="attr:productorPropes"/>          
            
  </properties>  
  ```

  相关配置说明：

  **bootstrap.servers** kafka服务器地址配置
  **value.serializer** kafka消息序列化插件配置
  **key.serializer** kafka消息key序列化插件配置
  **f:sendDatatoKafka="true"** 是否启动消息发送功能，false 禁用，true 启用
  **f:sendAsyn="true**" 控制组件是否异步发送消息，默认为true
  **workerThreadSiz**e 异步发送消息线程池，默认100
  **workerThreadQueueSize** 异步发送消息队列，默认10240

  #### **2.2 发送kafka消息**

  发送kafka消息相关组件：

  org.frameworkset.plugin.kafka.KafkaUtil

  org.frameworkset.plugin.kafka.KafkaProductor

  KafkaUtil组件加载配置文件并获取KafkaProductor ,通过KafkaProductor 发送kafka消息

  Java代码

  ```java
  KafkaProductor productor = KafkaUtil.getKafkaProductor("kafkaproductor");  
          productor.send("blackcat",//kafka topic  
                  1l, //message key  
                  "aaa");//message  
          productor.send("blackcat", //kafka topic  
                  "bbb"); //message  
  ```

    **异步方式发送消息**

  <property name="workerThreadSize" value="100"/>
  <property name="workerThreadQueueSize" value="10240"/>

  <property name="kafkaproductor"
  class="org.frameworkset.plugin.kafka.KafkaProductor"
  init-method="init"
  f:sendDatatoKafka="true"
  f:sendAsyn="true"
  f:productorPropes="attr:productorPropes"/>

  通过api控制是否异步发送消息：

  //异步方式发送消息
  productor.send("blackcat",3l,"aaa",true);
  productor.send("blackcat",4l,"bbb",true);

  //同步方式发送消息
  productor.send("blackcat",5l,"aaa",false);
  productor.send("blackcat",6l,"bbb",false);

  ### 3.接收和处理kafka消息

  #### 3.1 kafka consumer配置

  新建kafkaconsumer.xml文件，放到classpath根路径下面

  Xml代码

```xml
<properties>  
    <property name="consumerPropes">  
        <propes>  
  
  
            <property name="group.id" value="test">  
                <description> <![CDATA[ 指定kafka group id]]></description>  
            </property>  
            <property name="zookeeper.session.timeout.ms" value="30000">  
                <description> <![CDATA[ 指定kafkazk会话超时时间]]></description>  
            </property>  
              
  
            <property name="auto.commit.interval.ms" value="3000">  
                <description> <![CDATA[ 指定kafka自动提交时间间隔]]></description>  
            </property>  
  
            <property name="auto.offset.reset" value="smallest">  
                <description> <![CDATA[ ]]></description>  
            </property>  
            <property name="zookeeper.connect" value="hadoop85:2181,hadoop86:2181,hadoop88:2181">  
                <description> <![CDATA[ 指定kafka节点列表，用于获取metadata(元数据)，不必全部指定]]></description>  
            </property>  
  
        </propes>  
    </property>  
    <property name="kafkaconsumer"  
        class="org.frameworkset.plugin.kafka.KafkaBatchConsumer" init-method="init"  
f:batchsize="-1"  
        f:checkinterval="10000"  
        f:productorPropes="attr:consumerPropes" f:topic="blackcat"  
        f:storeService="attr:storeService" f:partitions="4" />  
    <property name="storeService"  
         class="org.frameworkset.plugin.kafka.StoreServiceTest" />     
  
</properties>  
```

配置说明：
**storeService** 配置消息处理组件
**zookeeper.connect** 配置管理kafka服务器和消息的zookeeper集群地址
**f:topic="blackcat"** 消费的kafka topic
**f:partitions="4"** topic对应的分区数，决定并行处理消息的工作线程
**f:batchsize="-1"** 批处理消息条数，-1禁用批处理，>0时按照批处理方式按批次提交消息给storeservice组件
**f:checkinterval="10000"** 指定批处理消息接收最大等待时间，单位毫秒。按照批处理方式时，如果超过checkinterval指定的时间，到达的消息没有到达batchsize，则强制提交处理当前批次的数据到storeservice组件

#### **3.2 接收和处理消息**

接收和处理消息相关组件：
org.frameworkset.plugin.kafka.KafkaConsumer
org.frameworkset.plugin.kafka.StoreService

**编写消息处理组件，处理组件需要实现接口**
org.frameworkset.plugin.kafka.StoreService
//按条处理数据
public void store(MessageAndMetadata<byte[], byte[]> message)  throws Exception ;
public void closeService();
//按批处理消息
public void store(List<MessageAndMetadata<byte[], byte[]>> messages) throws Exception
StoreServiceTest实现：

**Java代码**

```java
package org.frameworkset.plugin.kafka;  
  
import org.apache.kafka.common.serialization.LongDeserializer;  
import org.apache.kafka.common.serialization.StringDeserializer;  
  
import kafka.message.MessageAndMetadata;  
  
public class StoreServiceTest extends BaseStoreService {  
    StringDeserializer sd = new StringDeserializer();  
    LongDeserializer ld = new LongDeserializer();  
    @Override  
    public void store(List<MessageAndMetadata<byte[], byte[]>> messages) throws Exception {  
        for(MessageAndMetadata<byte[], byte[]> message:messages){  
            String data = sd.deserialize(null,message.message());  
            long key = ld.deserialize(null, message.key());  
            System.out.println("key="+key+",data="+data);  
        }  
    }  
  
    @Override  
    public void closeService() {  
        sd.close();  
        ld.close();  
    }  
  
    @Override  
    public void store(MessageAndMetadata<byte[], byte[]> message) throws Exception {  
        String data = sd.deserialize(null,message.message());  
        long key = ld.deserialize(null, message.key());  
        System.out.println("key="+key+",data="+data);  
    }  
  
}  
```

#### **3.3 加载kafka consumer配置并启动消息接收线程**

Java代码 

```java
BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("kafkaconfumer.xml");  
        KafkaListener consumer = context.getTBeanObject("kafkaconsumer", KafkaListener.class);  
        Thread t = new Thread(consumer);  
        t.start();  
```

