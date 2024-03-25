## bboss kafka组件使用介绍

![](images\kafka.png)

本文使用的实例对应的gradle源码工程git访问地址：

http://git.oschina.net/bboss/bestpractice

testkafka子工程地址

http://git.oschina.net/bboss/bestpractice/tree/master/testkafka2x

bboss kafka组件作用

- 快速配置kafka客户端和消费者

- 发送数据到kafka

- 从kafka接收和处理数据（支持批量消息处理和按条处理)

# 1.导入bboss kafka组件

  maven坐标
  Xml代码

  ```xml
  
    <dependency>  
      <groupId>com.bbossgroups.plugins</groupId>  
      <artifactId>bboss-plugin-kafka2x</artifactId>  
      <version>6.2.7</version>  
  </dependency>
  ```

  **gradle坐标**

  Java代码

  ```java
compile 'com.bbossgroups.plugins:bboss-plugin-kafka2x:6.2.7' 
  ```

  kafka依赖包需要额外导入，下面给出示例，kafka2x变量可以指定为具体的kafka客户端版本号，例如：

  1.1.0

  2.3.0

  3.4.0

  

  kafka_2.12包可以根据实际情况调整为对应的kafka版本号，例如：

  kafka_2.12

  kafka_2.13

  ```groovy
  api (
  			[group: 'org.apache.kafka', name: 'kafka_2.12', version: "${kafka2x}", transitive: true],
  	){
  		exclude group: 'log4j', module: 'log4j'
  		exclude group: 'org.slf4j', module: 'slf4j-log4j12'
  	}
  
  	api ([group: 'org.apache.kafka', name: 'kafka-tools', version: "${kafka2x}", transitive: true],){
  		exclude group: 'log4j', module: 'log4j'
  		exclude group: 'org.slf4j', module: 'slf4j-log4j12'
  	}
  
  	api ([group: 'org.apache.kafka', name: 'kafka-clients', version: "${kafka2x}", transitive: true],){
  		exclude group: 'log4j', module: 'log4j'
  		exclude group: 'org.slf4j', module: 'slf4j-log4j12'
  	}
  
  	api ([group: 'org.apache.kafka', name: 'kafka-streams', version: "${kafka2x}", transitive: true],){
  		exclude group: 'log4j', module: 'log4j'
  		exclude group: 'org.slf4j', module: 'slf4j-log4j12'
  	}
  ```

# 2.使用kafka producer发送消息
## 2.1 kafka producer配置


编写kafka.xml配置文件，放到classpath跟路径下面

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
			<property name="bootstrap.servers" value="10.19.85.65:19092">
				<description> <![CDATA[ 指定kafka节点列表，用于获取metadata(元数据)，不必全部指定]]></description>
			</property>
			<property name="batch.size" value="10000">
				<description> <![CDATA[ 批处理消息大小：
				the producer will attempt to batch records together into fewer requests whenever multiple records are being sent to the same partition. This helps performance on both the client and the server. This configuration controls the default batch size in bytes.
No attempt will be made to batch records larger than this size.

Requests sent to brokers will contain multiple batches, one for each partition with data available to be sent.

A small batch size will make batching less common and may reduce throughput (a batch size of zero will disable batching entirely). A very large batch size may use memory a bit more wastefully as we will always allocate a buffer of the specified batch size in anticipation of additional records.
				]]></description>
			</property>

			<property name="linger.ms" value="10000">
				<description> <![CDATA[
				 <p>
 * The producer maintains buffers of unsent records for each partition. These buffers are of a size specified by
 * the <code>batch.size</code> config. Making this larger can result in more batching, but requires more memory (since we will
 * generally have one of these buffers for each active partition).
 * <p>
 * By default a buffer is available to send immediately even if there is additional unused space in the buffer. However if you
 * want to reduce the number of requests you can set <code>linger.ms</code> to something greater than 0. This will
 * instruct the producer to wait up to that number of milliseconds before sending a request in hope that more records will
 * arrive to fill up the same batch. This is analogous to Nagle's algorithm in TCP. For example, in the code snippet above,
 * likely all 100 records would be sent in a single request since we set our linger time to 1 millisecond. However this setting
 * would add 1 millisecond of latency to our request waiting for more records to arrive if we didn't fill up the buffer. Note that
 * records that arrive close together in time will generally batch together even with <code>linger.ms=0</code> so under heavy load
 * batching will occur regardless of the linger configuration; however setting this to something larger than 0 can lead to fewer, more
 * efficient requests when not under maximal load at the cost of a small amount of latency.
 * <p>
 * The <code>buffer.memory</code> controls the total amount of memory available to the producer for buffering. If records
 * are sent faster than they can be transmitted to the server then this buffer space will be exhausted. When the buffer space is
 * exhausted additional send calls will block. The threshold for time to block is determined by <code>max.block.ms</code> after which it throws
 * a TimeoutException.
 * <p>]]></description>
			</property>
			<property name="buffer.memory" value="10000">
				<description> <![CDATA[ 批处理消息大小：
				The <code>buffer.memory</code> controls the total amount of memory available to the producer for buffering. If records
 * are sent faster than they can be transmitted to the server then this buffer space will be exhausted. When the buffer space is
 * exhausted additional send calls will block. The threshold for time to block is determined by <code>max.block.ms</code> after which it throws
 * a TimeoutException.]]></description>
			</property>

		</propes>
	</property>
	
	<property name="kafkaproductor" 
		class="org.frameworkset.plugin.kafka.KafkaProductor"
		init-method="init"
		f:sendDatatoKafka="true"
		f:productorPropes="attr:productorPropes"/>
		
</properties>
```

  相关配置说明：

  

**bootstrap.servers** kafka服务器地址配置

**value.serializer** kafka消息序列化插件配置

  **key.serializer** kafka消息key序列化插件配置

  **f:sendDatatoKafka="true"** 是否启动消息发送功能，false 禁用，true 启用



## 2.2 发送kafka消息

发送kafka消息相关组件：

org.frameworkset.plugin.kafka.KafkaUtil

org.frameworkset.plugin.kafka.KafkaProductor

KafkaUtil组件加载配置文件并获取KafkaProductor ,通过KafkaProductor 发送kafka消息（默认采用异步方式发送消息）


  ```java
  KafkaProductor productor = KafkaUtil.getKafkaProductor("kafkaproductor");  
          productor.send("blackcat",//kafka topic  
                  1l, //message key  
                  "aaa");//message  
        productor.send("blackcat", //kafka topic  
                  "bbb"); //message  
  ```



  通过api控制同步发送消息：
```java
Future<RecordMetadata> recordMetadataFuture = productor.send("blackcatstore", (long)12, SimpleStringUtil.object2json(datas));
RecordMetadata recordMetadata = recordMetadataFuture.get();//同步等待
```
# 3. 接收和处理kafka消息

## 3.1 kafka consumer配置

  新建[kafkaconfumersimple.xml](https://gitee.com/bboss/bestpractice/blob/master/testkafka2x/resources/kafka_2.12-2.3.0/kafkaconfumersimple.xml)文件，放到classpath根路径下面

```xml
<properties>
	<property name="consumerPropes">
		<propes>
<!-- 
			<property name="request.timeout.ms" value="30000">
				<description> <![CDATA[ 指定kafka节点列表，用于获取metadata(元数据)，不必全部指定]]></description>
			</property>
 -->


			<property name="session.timeout.ms" value="30000">
				<description> <![CDATA[ The timeout used to detect client failures when using "
                                                        + "Kafka's group management facility. The client sends periodic heartbeats to indicate its liveness "
                                                        + "to the broker. If no heartbeats are received by the broker before the expiration of this session timeout, "
                                                        + "then the broker will remove this client from the group and initiate a rebalance. Note that the value "
                                                        + "must be in the allowable range as configured in the broker configuration by <code>group.min.session.timeout.ms</code> "
                                                        + "and <code>group.max.session.timeout.ms</code>.]]></description>
			</property>
			<property name="auto.commit.interval.ms" value="1000">
				<description> <![CDATA[ The frequency in milliseconds that the consumer offsets are auto-committed to Kafka if <code>enable.auto.commit</code> is set to <code>true</code>.]]></description>
			</property>



			<property name="auto.offset.reset" value="latest">
				<description> <![CDATA[ What to do when there is no initial offset in Kafka or if the current offset does not exist any more on the server (e.g. because that data has been deleted): <ul><li>earliest: automatically reset the offset to the earliest offset<li>latest: automatically reset the offset to the latest offset</li><li>none: throw exception to the consumer if no previous offset is found for the consumer's group</li><li>anything else: throw exception to the consumer.</li></ul>]]></description>
			</property>
			<property name="bootstrap.servers" value="10.19.85.65:19092">
				<description> <![CDATA[ A list of host/port pairs to use for establishing the initial connection to the Kafka cluster. The client will make use of all servers irrespective of which servers are specified here for bootstrapping&mdash;this list only impacts the initial hosts used to discover the full set of servers. This list should be in the form "
                                                       + "<code>host1:port1,host2:port2,...</code>. Since these servers are just used for the initial connection to "
                                                       + "discover the full cluster membership (which may change dynamically), this list need not contain the full set of "
                                                       + "servers (you may want more than one, though, in case a server is down).]]></description>
			</property>
			<property name="enable.auto.commit" value="true">
				<description> <![CDATA[If true the consumer's offset will be periodically committed in the background.]]></description>
			</property>

			<!--可以在代码级覆盖的配置开始-->
			<property name="max.poll.records" value="1000">
				<description> <![CDATA[ 指定kafka节点列表，用于获取metadata(元数据)，不必全部指定]]></description>
			</property>

			<property name="value.deserializer" value="org.apache.kafka.common.serialization.StringDeserializer">
				<description> <![CDATA[ Deserializer class for value that implements the <code>org.apache.kafka.common.serialization.Deserializer</code> interface.]]></description>
			</property>
			<property name="key.deserializer" value="org.apache.kafka.common.serialization.LongDeserializer">
				<description> <![CDATA[ Deserializer class for key that implements the <code>org.apache.kafka.common.serialization.Deserializer</code> interface.]]></description>
			</property>
			<property name="group.id" value="test">
				<description> <![CDATA[ A unique string that identifies the consumer group this consumer belongs to. This property is required if the consumer uses either the group management functionality by using <code>subscribe(topic)</code> or the Kafka-based offset management strategy.]]></description>
			</property>
			<!--可以在代码级覆盖的配置完毕-->

		</propes>
	</property>



	<!-- 
	threads:启动接收消息线程数，默认4，可以根据kafka topic分区总数来决定
	workThreads: 每个接收消息线程会派生workThreads个线程来处理接收到消息
	workQueue: 派生线程池等待队列长度
	 f:pollTimeout="5000" 从kafka拉取消息超时时间
			  f:maxPollRecords="1000"  每次从kafka拉取的消息数量
			  f:batch="true" true调用批量store方法， false调用单条store方法
	-->
	<property name="kafkabatchconsumerstore"
			  class="org.frameworkset.plugin.kafka.TestKafkaBatchConsumer2ndStore" init-method="init"
			  f:consumerPropes="attr:consumerPropes"
			  f:topic="blackcatstore"
			  f:threads="2"
			  f:pollTimeout="5000"
			  f:maxPollRecords="1000"
			  f:batch="true"
	          f:workThreads="10"
			  f:workQueue="100"
			  f:groupId="test"
			  f:keyDeserializer="org.apache.kafka.common.serialization.LongDeserializer"
			  f:valueDeserializer="org.apache.kafka.common.serialization.StringDeserializer"
	/>

</properties>
```

配置说明：

topic： 消费的主题

groupId：消费端id

threads:启动接收消息线程数，默认4，可以根据kafka topic分区总数来决定

workThreads: 每个接收消息线程会派生workThreads个线程来处理接收到消息

workQueue: 派生线程池等待队列长度

pollTimeout="5000" 从kafka拉取消息超时时间

maxPollRecords="1000"  每次从kafka拉取的消息数量

batch="true" true调用批量store方法， false调用单条store方法

consumerPropes 指定kafka consumer属性，具体项目参考kafka官方concumer配置

keyDeserializer key反序列化插件配置

valueDeserializer value反序列化插件配置



## 3.2 接收和处理消息

接收和处理消息相关组件：

org.frameworkset.plugin.kafka.KafkaConsumersStarter

org.frameworkset.plugin.kafka.KafkaBatchConsumer2ndStore



**编写消息处理组件，处理组件需要实现接口**

```java
org.frameworkset.plugin.kafka.KafkaBatchConsumer2ndStore

//按条处理数据


	public void store(ConsumerRecord<Object,Object> message)  throws Exception ;



//按批处理消息

	public void store(ConsumerRecords<Object, Object> records)  throws Exception ;
```

StoreServiceTest实现：

```java
public class TestKafkaBatchConsumer2ndStore extends KafkaBatchConsumer2ndStore{
	@Override
	public void store(ConsumerRecords<Object, Object> messages) throws Exception {
		for(ConsumerRecord<Object,Object> message:messages){
			Object data = message.value();
			Object key =  message.key();
			System.out.println("key="+key+",data="+data+",topic="+message.topic()+",partition="+message.partition()+",offset="+message.offset());
		}
	}



	@Override
	public void store(ConsumerRecord<Object,Object> message) throws Exception {
		Object data = message.value();
		Object key =  message.key();
		System.out.println("key="+key+",data="+data+",topic="+message.topic()+",partition="+message.partition()+",offset="+message.offset());
	}
}
```

## 3.3 加载配置并启动管理kafka consumer

```java
 //启动ioc配置对应的容器中管理的kafka消费程序，自动注册消费程序销毁hook，以便在jvm退出时自动关闭消费程序
		KafkaConsumersStarter.startConsumers("kafkaconfumersimple.xml");

        //启动ioc配置对应的容器中管理的kafka消费程序，通过addShutdownHook控制是否注册消费程序销毁hook，以便在jvm退出时自动关闭消费程序 true 自动注册，false不自动注册（需手动通过addShutdownHook控制是销毁消费程序）
        //false 情况下需要手动调用shutdownConsumers(String applicationContextIOC)方法或者shutdownAllConsumers()方法销毁对应的消费程序
//        KafkaConsumersStarter.startConsumers("kafka_2.12-2.3.0/kafkaconfumersimple.xml",false);

        //手动销毁ioc配置对应的容器中管理的kafka消费程序
//        KafkaConsumersStarter.shutdownConsumers("kafka_2.12-2.3.0/kafkaconfumersimple.xml");


        //手动销毁所有容器中管理的kafka消费程序
//        KafkaConsumersStarter.shutdownAllConsumers();
```

# 4 弹性扩展和缩减kafka consumer消费线程

通过kaka组件相关的api可以方便地调整kafka consumer消费线程数量:

1. 增加kafka consumer消费线程
2. 缩减kafka consumer消费线程
3. 重置kafka consumer消费线程

## 4.1 api使用案例

```java
 KafkaUtil kafkaUtil = new KafkaUtil("kafka_2.12-2.3.0/kafkaconfumersimple.xml");
        BaseKafkaConsumer kafkaConsumer = kafkaUtil.getKafkaConsumer("kafkabatchconsumerstore");
//增加给定数量的消费线程
        kafkaConsumer.increamentConsumerThead(2);
        //消减给定数量的消费线程
        kafkaConsumer.decreamentConsumerThead(2);

        //重置消费线程数量
        kafkaConsumer.resetConsumerThreads(3);
```

## 4.2 实时监听apollo配置中线程数

可以实时监听apollo配置中线程数变化，实现动态弹性扩展和缩减kafka consumer消费线程功能

在kafkaconfumersimple.xml增加apollo相关的配置：

```xml
    <!--
          指定apolloNamespace属性配置namespace
          kafka消费线程数量变化监听器
       -->

    <config apolloNamespace="application"
            configChangeListener="org.frameworkset.plugin.kafka.ConsumerThreadChangeListener"/>
```

org.frameworkset.plugin.kafka.ConsumerThreadChangeListener继承抽象类：

**org.frameworkset.apollo.PropertiesChangeListener**

具体的实现如下：

```java
package org.frameworkset.plugin.kafka;


import com.ctrip.framework.apollo.model.ConfigChange;
import com.ctrip.framework.apollo.model.ConfigChangeEvent;
import org.frameworkset.apollo.PropertiesChangeListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Set;

/**
 * <p>Description: kafka消费线程数量变化监听器

 * </p>
 * <p></p>
 * <p>Copyright (c) 2020</p>
 * @Date 2020/8/9 23:10
 * @author biaoping.yin
 * @version 1.0
 */
public class ConsumerThreadChangeListener extends PropertiesChangeListener {
	private static Logger logger = LoggerFactory.getLogger(ConsumerThreadChangeListener.class);


	public void onChange(ConfigChangeEvent changeEvent) {
		if(logger.isInfoEnabled()) {
			logger.info("Changes for namespace {}", changeEvent.getNamespace());
		}
        Set<String> changedKeys = changeEvent.changedKeys();
        ConfigChange threadChange = null;

        String threadKey = "thread";

        for (String key : changedKeys) {
            if(key.equals(threadKey) ){
                threadChange = changeEvent.getChange(key);
                break;

            }
        }
        if(threadChange != null){
            String thread = threadChange.getNewValue();
            int i_thread = Integer.parseInt(thread);
            BaseKafkaConsumer kafkaConsumer = applicationContext.getTBeanObject("kafkabatchconsumerstore",BaseKafkaConsumer.class);
            //重置消费线程数量
            kafkaConsumer.resetConsumerThreads(i_thread);
        }


	}

	@Override
	public void completeLoaded() {

	}
}
```

ConsumerThreadChangeListener实时监听配置参数thread，如果有变化，则从ioc容器applicationContext中获取BaseKafkaConsumer组件kafkabatchconsumerstore，然后调用下面的方法调整消费线程数量:

```java
kafkaConsumer.resetConsumerThreads(i_thread);
```

## 4.3 bboss apollo使用参考文档

非spring boot项目

https://esdoc.bbossgroups.com/#/apollo-config

spring boot项目

https://esdoc.bbossgroups.com/#/springboot-bbosses-apollo

