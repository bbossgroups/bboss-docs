### bboss持久层事务管理组件托管第三方持久层框架（mybatis等）事务功能介绍

bboss持久层框架中的TXDataSource数据源类，可以实现第三方数据库事务代理功能

com.frameworkset.orm.transaction.TXDataSource

**1.事务托管原理**

TXDataSource可以托管hibernate，ibatis，mybatis等持久层框架的事务管理，原理如下：

我们只需要通过TXDataSource的构造函数传入需要托管事务的实际数据源DataSource即可，这个DataSource可以是bboss内置的数据源，也可以是第三方数据源（common dbcp，C3P0，Proxool ，Druid）等等

public TXDataSource(DataSource datasource)

这样我们只要通过TXDataSource实例的getConnection()方法既可以获取到事务环境中的connection资源从而实现数据库事务的托管功能。

**2.TXDataSource数据源的具体使用方法**

我们以托管开源工作流activiti的事务作为示例，采用bboss内置数据源

在继续之前需要知道一下组件com.frameworkset.common.poolman.util.SQLManager中的两个工具方法：

public static DataSource getTXDatasourceByDBName(String dbname) --直接获取bboss的内置数据源，并将该数据源转换为一个代理事务的数据源，bboss持久层的poolman.xml文件中需要定义dbname代表的数据源

public static DataSource getTXDatasource(DataSource ds) --直接将ds数据源转换为一个代理事务的数据源

**如果在poolman.xml配置中已经制定了enablejta参数为true，则通过SQLManager的getDatasource方法直接就会返回一个支持TX事务的数据源。**

首先在poolman.xml文件中配置一个名称叫mysql的datasource

Xml代码

```xml
<datasource>  
  
   <dbname>mysql</dbname>  
<loadmetadata>false</loadmetadata>  
   <enablejta>true</enablejta>  
   <jndiName>jdbc/mysql-ds</jndiName>  
   <driver>com.mysql.jdbc.Driver</driver>  
  
    <url>jdbc:mysql://localhost:3306/activiti</url>   
  
   <username>root</username>  
   <password>123456</password>  
   .........  
</datasource>  
```

如果然后在activiti的配置文件activiti.cfg.xml中做如下配置：

Xml代码

```xml
<properties>  
  <property name="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">  
     <property name="dataSource" factory-class="com.frameworkset.common.poolman.util.SQLManager" factory-method="getTXDatasourceByDBName">  
        <construction>  
            <property value="mysql" />  
        </construction>  
    </property>  
    <!-- Database configurations -->  
    <property name="databaseSchemaUpdate" value="true" />      
    <!-- job executor configurations -->  
    <property name="jobExecutorActivate" value="false" />      
    <!-- mail server configurations -->  
    <property name="mailServerPort" value="5025" />      
    <property name="history" value="full" />  
  </property>  
</properties>  
```

从activiti.cfg.xml配置文件中可以看出，我们已经可以使用bboss ioc框架来管理activiti流程引擎，bboss ioc容器中管理的组件都可以用于activiti的相关活动环节和事件监听器中（该功能参考文章[开源工作流引擎activiti与bboss整合使用方法浅析](http://yin-bp.iteye.com/blog/1506335)）。这里需要关注的是配置内容：

Xml代码 

```xml
<property name="dataSource" factory-class="com.frameworkset.common.poolman.util.SQLManager" factory-method="getTXDatasourceByDBName">  
        <construction>  
            <property value="mysql" />  
        </construction>  
    </property>  
```

我们采用bboss ioc的静态工厂模式来定义一个TXDatasource并注入到activiti的org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration
组件中，这样activiti流程引擎的db事务就可以被bboss数据库事务所托管了。bboss数据库事务管理可以参考[http://sourceforge.net/projects/bboss/files/bbossgroups-3.4/bbossgroups%20training.ppt/download](http://sourceforge.net/projects/bboss/files/bbossgroups-3.4/bbossgroups training.ppt/download)中的数据库相关事务介绍章节。

配置好了后就可以，启动流程引擎（相关方法请参考activiti的十分钟指南），看看两个事务管理的代码示例。

**3.代码示例**

**3.1 创建activiti的用户信息--将两个创建用户操作包含在事务中，activiti采用mybatis作为持久层框架**

Java代码 

```java
TransactionManager tm = new TransactionManager();  
    try  
    {  
        tm.begin();//开启事务  
        identityService.saveUser(identityService.newUser("kermit"));  
        identityService.saveUser(identityService.newUser("gonzo"));       
        tm.commit();//提交事务  
    }  
    catch(Throwable e)  
    {  
        throw e  
    }  
    finally  
{  
    tm.release();  
}  
```

**3.2 完成流程任务和业务逻辑处理相结合--将业务处理和流程操作包含在一个事务中，activiti采用mybatis作为持久层框架**

Java代码

```java
// Start process instance  
    ProcessInstance processInstance = runtimeService.startProcessInstanceByKey("taskAssigneeExampleProcess");  
    TransactionManager tm = new TransactionManager();  
    try {  
        tm.begin();  
        //处理业务逻辑,省略处理代码  
        .....  
        // 获取用户任务列表  
        List<Task> tasks = taskService  
          .createTaskQuery()  
          .taskAssignee("kermit")  
          .list();  
          
        Task myTask = tasks.get(0);  
        //完成任务  
        taskService.complete(myTask.getId());  
          
        tm.commit();  
    } catch (Throwable e) {  
        throw e  
    }  
    finally  
{  
    tm.release();  
}  
```

**3.3 在spring中引用bboss数据源**  

Xml代码

```xml
<bean id="dataSource"   
        class="com.frameworkset.common.poolman.util.SQLManager"  
        factory-method="getTXDatasourceByDBName">  
        <constructor-arg value="wood"></constructor-arg>  
    </bean>  
```

相关资源：

activiti和bboss结合工程下载：https://github.com/yin-bp/activiti-engine-5.12