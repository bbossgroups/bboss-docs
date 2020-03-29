### 基于quartz任务调度组件的使用

2.0版本的aop框架中集成了quartz任务调度组件，该组件包含三部分：

1. 任务管理组件: 用来启动和停止任务调度引擎，管理任务（新增，启动，修改，停止，挂起/继续，删除任务）

   org.frameworkset.task.TaskService

2. 任务管理服务组件：按分组模式管理所有的job任务，并从相关的数据源加载作业任务

   所有的任务管理组件都从抽象类org.frameworkset.task. ScheduleService继承

3. 任务配置文件：用来配置不同的作业服务和具体的作业job。对应的配置文件为：

   /bbossaop/resources/org/frameworkset/task/quarts-task.xml  

下面分别举例说明：

15.1 任务管理组件

任务管理控件：用来启动和停止任务调度引擎，管理任务（启动，修改，停止，删除任务）

org.frameworkset.task.TaskService

启动任务引擎

TaskService.getTaskService().startService();  

停止任务引擎

TaskService.getTaskService().stopService();

删除任务

TaskService.getTaskService().deleteJob(String jobname, String groupid)

暂停任务

TaskService.getTaskService().pauseJob(String jobname, String groupid)

继续任务

TaskService.getTaskService().resumeJob(String name, String groupid)

新增和启动任务

TaskService.getTaskService().startExecuteJob(String groupid, SchedulejobInfo jobInfo)

更新任务

TaskService.getTaskService().updateExecuteJob(String groupid, SchedulejobInfo jobInfo)

15.2 任务管理服务组件

所有的任务管理组件都从抽象类org.frameworkset.task. ScheduleService继承，定义了一下抽象方法：

public abstract void startService(Scheduler scheduler) throws ScheduleServiceException;

public  abstract void startExecuteJob(Scheduler scheduler,SchedulejobInfo jobInfo);

public  abstract void updateJob(Scheduler scheduler,SchedulejobInfo jobInfo);

public  abstract void updateTriger(Scheduler scheduler,SchedulejobInfo jobInfo);

public  abstract void updateJobAndTriger(Scheduler scheduler, SchedulejobInfo jobInfo);

系统中默认提供的任务管理组件

org.frameworkset.task.DefaultScheduleService，用来加载系统中默认的静态的任务，可以配置多个作业任务
如果用户需要动态管理自己的作业任务，那么可以编写自己的ScheduleService，实现org.frameworkset.task. 

ScheduleService类的抽象方法：

public class DemoScheduleService extends ScheduleService {

  @Override

public void startExecuteJob(Scheduler scheduler, SchedulejobInfo jobInfo) {

//执行新定义的任务

}

@Override

public void startService(Scheduler scheduler)

throws ScheduleServiceException {

//系统启动时，从资源库中获取所有的已经存在的任务，并加载到scheduler引擎中。

}  

  @Override

public void updateJob(Scheduler scheduler, SchedulejobInfo jobInfo) {

更新一个已经加载的作业任务

}

@Override

public void updateJobAndTriger(Scheduler scheduler, SchedulejobInfo jobInfo) {

更新作业任务，并触发任务的执行

}

  @Override

public void updateTriger(Scheduler scheduler, SchedulejobInfo jobInfo) {

更新任务触发器

}

}

编写好自定义的任务服务组件后就可以将其配置到quarts-task.xml文件中了，例如：

<**properties>**

<property name="taskconfig"

 enable="true">

<**list>**

<property name="demo任务执行器" taskid="DemoScheduleService"

class="org.frameworkset.task.DemoScheduleService" used="true"><**/property>**

<**/list>**

<**/property>**

<**/properties>**

必须指定唯一的taskid属性，use属性用来设置该任务组件是否生效。    

15.3 任务管理配置文件

一个简单的配置文件

/bbossaop/resources/org/frameworkset/task/quarts-task.xml的内容如下：

<**properties>**

<property name="taskconfig"

 enable="true">

<**list>**

<property name="定时任务执行器" taskid="default"

class="org.frameworkset.task.DefaultScheduleService" used="true">

<!--

可执行的任务项

属性说明：

name：任务项名称

id:任务项标识

action:具体的任务执行处理程序,实现org.frameworkset.task.Execute接口

cron_time： cron格式的时间表达式，用来管理任务执行的生命周期，相关的规则请参照日期管理控件quartz的说明文档

used 是否使用

true 加载，缺省值

false 不加载  

子元素说明：

Map 和property:设置任务执行的参数，name标识参数名称，value指定参数的值

-->

<**list>**

<property name="workbroker" jobid="workbroker"

class="org.frameworkset.task.TestJob"cronb_time="0 56 14 * * ?" used="true">

<**map>**

<**property name="send_count" value="2" />**

<**map>**

<**/property>**

<**/list>**

<**/property>**

<**/list>**

<**/property>**

<**properties>**

说明：

org.frameworkset.task.DefaultScheduleService是系统中默认提供的任务管理组件，用来加载系统中默认的静态的任务，可以配置多个，例如：

<property name="workbroker" jobid="workbroker" action="org.frameworkset.task.TestJob"
cronb_time="0 56 14 * * ?" used="true">

<**map>**

<**property name="send_count" value="2" />**

<**/map>**

<**/property>**

属性说明：

Taskid：用来区分任务组，可以作为任务组的唯一标识，系统中通过Taskid和jobid来区分唯一的一个作业任务，TaskService组件的很多方法中都有groupid和jobname两个参数，taskid就对应于groupid参数，jobid对应于jobname参数。

Name 任务的名称

Jobid 任务的标识

Action任务执行的操作

cronb_time 任务执行的调度时间，具体需要参考cronb_time的语法。

use：区分任务是否生效

内嵌的节点

<**map>**

<**property name="send_count" value="2" />**

<**/map>**

用来指定任务执行时需要的参数。

需要特别说明的是Action类必须实现org.frameworkset.task.Execute接口，例如：

public class TestJob implements Execute, Serializable {

public void execute(Map parameters) {

System.out.println("send_count = "+parameters.get("send_count"));

}

}

如果用户需要动态管理自己的作业任务，那么可以实现org.frameworkset.task. ScheduleService类  