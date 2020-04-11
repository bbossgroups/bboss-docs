### bbossgroups RPC 基于aop的轻量级rpc框架

bbossgroups RPC 是基于bbossaop的轻量级rpc框架，感兴趣的朋友可以用一用。bbossgroups提供的RPC

框架是bboss aop子项目中一个子模块，具有以下特点：

1.支持多种通讯协议jms，jgroups，mina，webservice，restful，并且协议可扩展

2.提供强有力的安全管理插件（可插拔的认证、鉴权、数据包加/解密插件）,保证远程通讯安全可靠。

3.开发部署模式简便，打破传统的RPC开发模式，不依赖于任何应用服务器和容器，你只需启动aop框架中提供的各种协议之一（例如jms，jgroups，mina，webservice）或者同时启动几种协议,你就可以对aop框架中管理的任何组件发起远程方法调用，唯一的前提是你的方法参数和返回结果必须是实现java.io.Serializable接口。同时你可以通过rpc框架的各种安全管理插件来保护你开放的远程组件服务。

4.远程方法调用方式简单，你只需要按照以下格式即可发起一个远程方法调用

带认证信息格式：

(protocol::uri)/serviceid?user=userAccount&password=userPassword;

不带认证信息格式：

(protocol::uri)/serviceid;

protocol：远程通讯协议

uri：服务器地址

serviceid:部署在aop框架中的组件服务标识

例如：

(mina::172.16.17.216:12347)/test.security.bean?user=admin&password=123456,其中的账号为admin，密码为123456

@Test

​    public void testMinaSecurityBean()

​    {

​        BussinessBeanInf beaninf = (BussinessBeanInf)BaseSPIManager.getBeanObject("

(mina::172.16.17.216:12347)/test.security.bean?user=admin&password=123456");

​        System.out.println("testMinaSecurityBean beaninf.getCount():"+beaninf.getCount());

​        System.out.println("testMinaSecurityBean 

beaninf.printMessage(message):"+beaninf.printMessage("test.security.bean"));

​    }

 @Test

​    public void testJmsSecurityBean()

​    {

​        BussinessBeanInf beaninf = (BussinessBeanInf)BaseSPIManager.getBeanObject("(jms::yinbiaoping-jms)/test.security.bean?user=admin&password=123456");

​        System.out.println("testJmsSecurityBean beaninf.getCount():"+beaninf.getCount());

​        System.out.println("testJmsSecurityBean 

beaninf.printMessage(message):"+beaninf.printMessage("test.security.bean"));

​    }

@Test

​    public void testJGroupSecurityBean()

​    {

​        BussinessBeanInf beaninf = (BussinessBeanInf)BaseSPIManager.getBeanObject("

(jgroup::172.16.17.216:1186)/test.security.bean?user=admin&password=123456");

​        System.out.println("testJGroupSecurityBean beaninf.getCount():"+beaninf.getCount());

​        System.out.println("testJGroupSecurityBean 

beaninf.printMessage(message):"+beaninf.printMessage("test.security.bean"));

​    }

 @Test

​    public void testWebServiceSecurityBean()

​    {

​        BussinessBeanInf beaninf = (BussinessBeanInf)BaseSPIManager.getBeanObject("(webservice::http://172.16.17.216:8080/WebRoot/cxfservices)/test.security.bean?user=admin&password=123456");

​        System.out.println("testJGroupSecurityBean beaninf.getCount():"+beaninf.getCount());

​        System.out.println("testJGroupSecurityBean 

beaninf.printMessage(message):"+beaninf.printMessage("test.security.bean"));

​    }

5.安全管理机制可以方便地启用和关闭

6.远程方法调用过程可自动调优，即自动区分远程目标地址是本地地址还是远程地址，判别rpc调用是远程方法调用还是当做本地方法调用

7.可以简单地实现单点服务调用和多播服务调用，如果是多播服务调用，rpc框架提供了获取不同服务器返回结果的相应接口，简单实用

8.bbossgroups rpc服务框架提供远程服务通讯的质量保障，例如故障重连，访问超时等等

9.bbossgroups rpc应用场景广泛，可以用于普通的rpc服务调用场景，也可以用作集群环境中各节点应用
之间通讯工具，因为你可以轻易地发布你的应用的远程组件，轻易地发起远程方法调用（只是获取组件实例的方法不同，方法调用和普通的对象方法调用一样）

10.rpc框架充分集成并吸纳了各种通讯协议本身的优点（jms，webservice，jgroup，mina）。

11.通过restful风格的协议，可以方便地实现rpc服务的路由功能

目前提供的大致功能就这些了，有什么考虑不周或者不正确的地方还请大家批评指正，一起交流学习，更

详细的情况介绍请访问我的博客http://blog.csdn.net/yin_bp。

bbossgroups项目发布的版本是1.0，将在1.0rc版本中增加对jboss netty协议框架的支持，呵呵 。

bbossgroups rpc框架包含在子项目bboss aop框架中，下载地址：

http://sourceforge.net/projects/bboss/files/bbossgroups-1.0/bbossaop.zip/download

文档下载地址：

[http://sourceforge.net/projects/bboss/files/bbossgroups-1.0/bbossgroups%20document.zip/download](http://sourceforge.net/projects/bboss/files/bbossgroups-1.0/bbossgroups document.zip/download)