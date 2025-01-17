# Rocketmq客户端组件使用介绍

bboss提供一个简单的分布式消息中间件Rocketmq操作组件，基于Rocketmq client客户端进行封装。

![领域模型](images\Rocketmq.png)

Rocketmq客户端组件gradle源码工程git访问地址：

https://gitee.com/bboss/bboss-plugins

Rocketmq客户端组件消息发送和接收案例地址：

https://gitee.com/bboss/bboss-plugins/tree/master/bboss-plugin-rocketmq/src/test/java/org/frameworkset/rocketmq

Rocketmq客户端组件作用

- 快速配置Rocketmq客户端和消费者
- 发送数据到Rocketmq
- 从Rocketmq接收和处理数据

## 1.导入Rocketmq组件

maven坐标 Xml代码

```xml
    <dependency>  
      <groupId>com.bbossgroups.plugins</groupId>  
      <artifactId>bboss-plugin-rocketmq</artifactId>  
      <version>6.3.7</version>  
  </dependency>
```

**gradle坐标**

Java代码

```java
compile 'com.bbossgroups.plugins:bboss-plugin-rocketmq:6.3.7' 
```

组件默认依赖的[Rocketmq client 5.3.1](https://central.sonatype.com/artifact/org.apache.rocketmq/rocketmq-client)版本的依赖包，无需额外导入。

## 2.使用Rocketmq producer发送消息

###  2.1 Rocketmq producer配置

编写rocketmq.xml配置文件，放到classpath跟路径下面

```xml
<properties>
    <property name="productorPropes">
        <propes>

            <!--扩展配置，示例说明，暂时没有实际意义-->
            <property name="buffer.memory" value="10000">
                <description> <![CDATA[ 批处理消息大小：
				The <code>buffer.memory</code> controls the total amount of memory available to the producer for buffering. If records
 * are sent faster than they can be transmitted to the server then this buffer space will be exhausted. When the buffer space is
 * exhausted additional send calls will block. The threshold for time to block is determined by <code>max.block.ms</code> after which it throws
 * a TimeoutException.]]></description>
            </property>

        </propes>
    </property>
   
    <property name="rocketmqproductor"
              class="org.frameworkset.rocketmq.RocketmqProductor"
              init-method="init"
              f:productGroup="testgroup"
              f:namesrvAddr="172.24.176.18:9876"
              f:accessKey="Rocketmq"
              f:secretKey="12345678"
              f:valueCodecSerial="org.frameworkset.rocketmq.codec.StringBytesCodecSerial"
              f:keyCodecSerial="org.frameworkset.rocketmq.codec.StringCodecSerial"
              f:sendDatatoRocketmq="true"
              f:productorPropes="attr:productorPropes"/>
    
</properties>
```

相关配置说明：

**namesrvAddr** rocketmq namesrv服务器地址配置

**valueCodecSerial** rocketmq 消息序列化插件配置

**keyCodecSerial** rocketmq 消息key序列化插件配置

**f:sendDatatoRocketmq="true"** 是否启动消息发送功能，false 禁用，true 启用

### 2.2 发送消息

发送消息相关组件：

org.frameworkset.rocketmq.RocketmqUtil

org.frameworkset.rocketmq.RocketmqProductor

RocketmqUtil组件加载配置文件并获取RocketmqProductor ,通过RocketmqProductor发送消息（默认采用异步方式发送消息）

```java
  RocketmqUtil rocketmqUtil = new RocketmqUtil("rocketmq.xml");
        RocketmqProductor rocketmqProductor = rocketmqUtil.getProductor("rocketmqproductor");

        for (int i = 0; i < 10000; i++) {
            try {
                Map<String,Object> data = new LinkedHashMap<>();
                data.put("name","石宇奇-"+i);
                data.put("job","羽毛球职业运动员-"+i);
                data.put("id",i);
                SendResult sendResult = rocketmqProductor.send("etltopic",//topic
                                                               "key-"+i,//key
                                                               data, //消息正文
                                                               "json" //tag
                                                              );
               
                /*
                 * There are different ways to send message, if you don't care about the send result,you can use this way
                 * {@code
                 * rocketmqProductor.sendOneway(msg);
                 * }
                 */

                /*
                 * if you want to get the send result in a synchronize way, you can use this send method
                 * {@code
                 * SendResult sendResult = rocketmqProductor.send(msg);
                 * System.out.printf("%s%n", sendResult);
                 * }
                 */

                /*
                 * if you want to get the send result in a asynchronize way, you can use this send method
                 * {@code
                 *
                 *  producer.rocketmqProductor(msg, new SendCallback() {
                 *  @Override
                 *  public void onSuccess(SendResult sendResult) {
                 *      // do something
                 *  }
                 *
                 *  @Override
                 *  public void onException(Throwable e) {
                 *      // do something
                 *  }
                 *});
                 *
                 *}
                 */

                System.out.printf("%s%n", sendResult);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
		//销毁productor
        rocketmqProductor.destroy();
```

### 2.3 销毁productor

销毁productor        

```java
rocketmqProductor.destroy();
```



## 3. 接收和处理消息

### 3.1 Consumer配置

新建rocketmq.xml文件，放到classpath根路径下面

```xml
<properties>
    
    <property name="consumerPropes">
        <propes>
            <!-- 
                   消费端扩展配置，暂时不用，可用于序列化和反序列化参数配置
             -->

            <property name="extendparam" value="30000">
                <description> <![CDATA[  消费端扩展配置，暂时不用，可用于序列化和反序列化参数配置.]]></description>
            </property>
         

        </propes>
    </property>
 
    <!--  
    
    workThreads: 每个接收消息线程会派生workThreads个线程来处理接收到消息
              f:maxPollRecords="1000"  每次从Rocketmq拉取的消息数量
              
   consumeFromWhere：拉取消息的开始位置，取值范围
            
    CONSUME_FROM_LAST_OFFSET,

    @Deprecated
    CONSUME_FROM_LAST_OFFSET_AND_FROM_MIN_WHEN_BOOT_FIRST,
    @Deprecated
    CONSUME_FROM_MIN_OFFSET,
    @Deprecated
    CONSUME_FROM_MAX_OFFSET,
    CONSUME_FROM_FIRST_OFFSET,
    CONSUME_FROM_TIMESTAMP,
    
    etltopic
    
    f:tag="A"
              f:topic="TestTopic"
    -->
    <property name="rocketmqconsumer"
              class="org.frameworkset.rocketmq.TestRocketmqConsumer2ndStore" 
              f:consumerPropes="attr:consumerPropes"
              f:tag="A"
              f:topic="TestTopic,TestTopic1"
              f:consumerGroup="etlgroup"
              f:accessKey="Rocketmq"
              f:secretKey="12345678"
              f:namesrvAddr="172.24.176.18:9876"
              f:maxPollRecords="100"  
              f:consumeMessageBatchMaxSize="50"
              f:consumeFromWhere="CONSUME_FROM_LAST_OFFSET"
              f:workThreads="10"
              f:keyDeserializer="org.frameworkset.rocketmq.codec.StringCodecDeserial"
              f:valueDeserializer="org.frameworkset.rocketmq.codec.StringCodecDeserial"
    />
</properties>
```

配置说明：

topic： 消费的主题,多个用逗号分隔

consumerGroup：消费端组标识

workThreads:启动接收消息线程数

maxPollRecords="1000" 每次从拉取的消息数量

consumeMessageBatchMaxSize 批次处理的消息数据量

consumeFromWhere 消费起始位置策略

keyDeserializer key反序列化插件配置

valueDeserializer value反序列化插件配置

### 3.2 接收和处理消息

接收和处理消息相关组件：

org.frameworkset.rocketmq.RocketMQConsumersStarter 加载配置，启动消费端

org.frameworkset.rocketmq.RocketmqConsumer2ndStore 消息处理接口

**编写消息处理组件，处理组件需要实现接口**

```java
/**
 * 数据持久化处理
 */
public interface StoreService<T> {
	public void store(List<RocketmqMessage<T>> messages)  throws Exception ;
}
```

RocketmqConsumer2ndStore实现实例：

```java


import org.frameworkset.rocketmq.codec.RocketmqMessage;

import java.util.List;

 
public class TestRocketmqConsumer2ndStore extends RocketmqConsumer2ndStore<String>{

    @Override
    public void store(List<RocketmqMessage<String>> messages) throws Exception   {
        for(RocketmqMessage<String> message:messages) {
            String data = message.getData();
            String key = message.getMessageExt().getKeys();
            String topic = message.getMessageExt().getTopic();
            int qid = message.getMessageExt().getQueueId();
            long offset = message.getMessageExt().getQueueOffset();
            System.out.println("key=" + key + ",data=" + data + ",topic=" + topic + ",partition=" + qid + ",offset=" + offset);
        }
    } 
}
```

### 3.3 加载配置并启动管理consumer

```java
package org.frameworkset.rocketmq;

public class TestRocketmqConsumerSimple {

	public static void main(String[] args) {
        
        //启动ioc配置对应的容器中管理的消费程序，需手动销毁消费程序
        RocketMQConsumersStarter.startConsumers("rocketmq.xml");

        //启动ioc配置对应的容器中管理的消费程序，通过addShutdownHook控制是否注册消费程序销毁hook，以便在jvm退出时自动关闭消费程序 true 自动注册，false不自动注册（需手动通过addShutdownHook控制是销毁消费程序）
        //false 情况下需要手动调用shutdownConsumers(String applicationContextIOC)方法或者shutdownAllConsumers()方法销毁对应的消费程序
//        RocketMQConsumersStarter.startConsumers("rocketmq.xml",true);

        //手动销毁ioc配置对应的容器中管理的kafka消费程序
//        RocketMQConsumersStarter.shutdownConsumers("rocketmq.xml");


        //手动销毁所有容器中管理的消费程序
//        RocketMQConsumersStarter.shutdownAllConsumers();
	}

}

```

## 4. 参考资料

### 4.1 高阶应用

基于Rocketmq客户端组件，提供数据ETL及流批处理框架Rocketmq输入和输出插件，实现基于Rocketmq的数据集成和数据实时流处理计算

[Rocketmq输入插件](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_114-rocketmq%e8%be%93%e5%85%a5%e6%8f%92%e4%bb%b6)

[Rocketmq输出插件](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_213-rocketmq%e8%be%93%e5%87%ba%e6%8f%92%e4%bb%b6)

### 4.2 Rocketmq官方资料

https://rocketmq.apache.org/zh/docs/domainModel/01main

