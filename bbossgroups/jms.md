### bbossgroups jms组件框架

bboss aop框架的jms组件提供针对jms规范的一组简单的操作接口，可一通过 JMSTemplate组件来实现JMS消息的接收和发送功能。

系统中提供了两个JMS Template实现：

org.frameworkset.mq.JMSTemplate—提供所有的jms接收和发送接口，不带主题订阅功能接口

org.frameworkset.mq.JMSReceiveTemplate-提供所有的jms接收和发送接口，带主题订阅功能接口  

下面是举一些简单的例子，说明这连个模板类的使用方法。

系统中可以方便地通过扩展连接工程管理抽象类

org.frameworkset.mq.JMSConnectionFactory来实现不同的jms服务提供商的jms服务器的支持。开发人员只需要实现JMSConnectionFactory的抽象方法：

​     /**

​     \* 构建特定提供商的连接工厂

​     *

​     \* @return

​     */

public abstract ConnectionFactory buildConnectionFactory() throws Exception;  

bboss aop框架中的jms组件提供了对apache activemq server的实现：

org.frameworkset.mq. AMQConnectionFactory

本章都是以apache activemq server为例来说明jms组件的基本接口。

由于篇幅问题，省略相关配置的介绍，感兴趣的朋友可以到以下地址下载详细的文档介绍：

http://sourceforge.net/projects/bboss/files/bbossgroups-1.0/bbossgroups%20document.zip/download

其中aop框架技术白皮书中有bboss jms组件的详细介绍，这里只列出一些测试用例。

测试用例  

1.2.1   从连接池工厂中获取jms connection

下面的代码从连接池工厂中获取jms connection对象，先后获取两次验证连接池是否生效。

JMSConnectionFactory factory = 

(JMSConnectionFactory)BaseSPIManager.getBeanObject("test.amq.PooledConnectionFactory");

​        try

​        {

​            Connection connection = factory.getConnection();

​            connection.start();

​            connection.close();

​            connection = factory.getConnection();

​            connection.start();   

connection.close();    

​        }

​        catch (JMSException e)

​        {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​        }  

1.2.2   从连接工厂中获取jms connection

从连接工厂中获取jms connection,每次都会创建新的jms connection

JMSConnectionFactory factory = 

(JMSConnectionFactory)BaseSPIManager.getBeanObject("test.amq.ConnectionFactory");

​        try

​        {

​            Connection connection = factory.getConnection();

​            connection.start();

​            connection.close();

​            connection = factory.getConnection();

​            connection.start();     

​        }

​        catch (JMSException e)

​        {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​        }  

1.2.3   向队列atest中发送消息

JMSTemplate template = (JMSTemplate)BaseSPIManager.getBeanObject("test.jmstemplate");

​        try

​        {

​            template.send("atest", "ahello");

​        }

​        catch (JMSException e)

​        {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​        }

​        finally

​        {

​            template.stop();

​        }  

1.2.4   向队列atest发送持久化的消息

JMSTemplate template = (JMSTemplate)BaseSPIManager.getBeanObject("test.jmstemplate");

​        try

​        {

​            template.send("atest", "phello",true);

​        }

​        catch (JMSException e)

​        {

​            e.printStackTrace();

​        }

​        finally

​        {

​            template.stop();

​    }

1.2.5   向队列atest中发送持久化消息，指定优先级和过期时间

JMSTemplate template = (JMSTemplate)BaseSPIManager.getBeanObject("test.jmstemplate");

​        try

​        {

​            template.send("atest", "allhello",true,4,10000);

​        }

​        catch (JMSException e)

​        {

​            e.printStackTrace();

​        }

​        finally

​        {

​            template.stop();

​        }  

1.2.6   从队列atest中同步接收消息

JMSTemplate template = (JMSTemplate)BaseSPIManager.getBeanObject("test.jmstemplate");

​        try

​        {

​            Message msg = template.receive("atest");

​            System.out.println("testReceiveMessage:"+ msg);

​        }

​        catch (JMSException e)

​        {

​            e.printStackTrace();

​        }

​        finally

​        {

​            template.stop();

​        }  

1.2.7   从队列atest中异步接收消息

利用test.jms.receive.template模板从队列atest中异步接收消息。

JMSReceiveTemplate template = 

(JMSReceiveTemplate)BaseSPIManager.getBeanObject("test.jms.receive.template");

​        try

​        {

​            template.setMessageListener("atest",new MessageListener() {

​                public void onMessage(Message arg0)

​                {

​                    System.out.println("msg comming:"+arg0);

​                }              

​            });

​        }
​        catch (Exception e)

​        {

​            template.stop();

​        }

​        finally

​        {

​        }

1.2.8   向主题getsubtest发送消息

JMSTemplate template = (JMSTemplate)BaseSPIManager.getBeanObject("test.jmstemplate");

​        try

​        {

​            template.send("topic://getsubtest", "getsubtest");

​        }

​        catch (JMSException e)

​        {

​            e.printStackTrace();

​        }

​        finally

​        {

​            template.stop();

​        }  

1.2.9   从主题订阅消息

JMSReceiveTemplate template = 

(JMSReceiveTemplate)BaseSPIManager.getBeanObject("test.topic.receive.jmstemplate");

​        try

​        {

​            template.getTopicSubscriber("getsubtest", "subscribename").setMessageListener(new 

MessageListener() {

​                public void onMessage(Message arg0)

​                {

​                    System.out.println("topic msg comming:"+arg0);

​                }    

​            });

​        }

​        catch (Exception e)

​        {

​            template.stop();

​        }

​        finally

​        {

​        }  

1.2.10 从主题退订消息

JMSReceiveTemplate template = 

(JMSReceiveTemplate)BaseSPIManager.getBeanObject("test.topic.receive.jmstemplate");

​            try

​            {

​                template.unsubscribe("subscribename");

​            }

​            catch (Exception e)

​            {

​                template.stop();

​            }

​            finally

​            {

​            }

注意事项：使用主题发布和订阅消息时，不要使用连接池连接工厂。    