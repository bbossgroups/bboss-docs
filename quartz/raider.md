### bboss quartz组件全攻略

本文全面介绍bboss中quartz组件的配置和使用方法。

定时任务默认配置文件一般在resources下面：

/resources/org/frameworkset/task/quarts-task.xml

组件源码地址

https://gitee.com/bboss/bboss/tree/master/bboss-schedule

https://github.com/bbossgroups/bboss/tree/master/bboss-schedule

摘要

1.resources下的quartz.properties文件为quartz引擎的默认配置，同时也可在bboss quartz引擎xml家配置文件中配置quartz引擎的配置参数覆盖默认配置（这里不做介绍）

2.quartz监听器配置

全局schedule监听器，全局job监听器，job监听器，全局trigger监听器，trigger监听器

3.日历配置

4.触发器配置

cron，simple，DateInterval，NthIncludedDay四种基本触发器类型

还有一种bboss提供特定类型builder类型，通过这种类型用户可以自己实现TriggerBuilder接口来动态创建自己的
Trigger类型，如果任务配置参数有变化还可以重新加载这些参数并重启对应的作业。

  5.quartz 任务配置

6.任务的启用和停用

下面详细介绍2，3，4，5，6部分。  

一、实例

用户可以看一个配置实例：task-quartz-exception.xml

加载改配置并启动一个quartz引擎：

Java代码

```java
TaskService taskService = TaskService.getTaskService("org/frameworkset/task/task-quartz-exception.xml");  
        taskService.startService(); 
```

我们结合实例来说明上述的几个部分。

二、quartz监听器配置

1.全局schedule监听器

schedule监听器监听quartz引擎中任务生命周期管理事件，包括任务加载、创建、销毁、挂起、执行等事件的监听，引擎全局配置，可以配置多个schedule监听器。配置实例如下：

Xml代码

```xml
   <property name="quartz.config.schedulerlistener">  
<list componentType="bean">  
<property name="DefaultSchedulerlistener" class="org.frameworkset.task.DefaultSchedulerListener"/>  
</list>  
</property>  
```

可以在list元素中配置多个scheduler监听器，配置顺序就是他们的执行顺序。DefaultSchedulerListener是bboss缺省提供的scheduler监听器，配置该监听器可以将相关的事件信息用log4j输出到日志文件中，日志级别为info级。

用户可以定义自己的scheduler监听器，必须实现org.quartz.SchedulerListener接口。

2.全局job监听器

全局job监听器可以监听所有quartz job事件，配置实例：

Xml代码

```xml
 <property name="quartz.config.globaljoblistener">  
<list componentType="bean">  
<property name="DefaultGlobalJobListener" class="org.frameworkset.task.DefaultGlobalJobListener"/>  
  
</list>  
</property>  
```

可以在list元素中配置多个GlobalJobListener监听器，配置顺序就是他们的执行顺序。

DefaultGlobalJobListener是bboss缺省提供的GlobalJobListener，配置该监听器可以将相关的事件信息用log4j输出到日志文件中，日志级别为info级。

用户可以定义自己的GlobalJobListener监听器，必须实现org.frameworkset.task.BaseJobListener基础抽象类。

3.job监听器

job监听器可以监听指定quartz job任务的执行情况，配置实例：

Xml代码

```xml
 <property name="quartz.config.joblistener">  
<list componentType="bean">  
<property name="DefaultJobListener" class="org.frameworkset.task.DefaultJobListener"/>  
</list>  
  
</property>  
```

可以在list元素中配置多个JobListener监听器，然后在特定的任务上面指定相应的监听器的名称即可（可以指定多个，用逗号分隔）。DefaultJobListener是bboss缺省提供的JobListener，配置该监听器可以将相关的事件信息用log4j输出到日志文件中，日志级别为info级。

用户可以定义自己的JobListener监听器，必须实现org.frameworkset.task.BaseJobListener基础抽象类。在具体的任务上配置job listener的实例：

Xml代码 

```xml
 <property name="beanmethodjobnoargs" jobid="beanmethodjobnoargs"   
bean-name="methodjob"  
method="actionexception"  
calendar="2012year"  
joblistenername="DefaultJobListener"  
triggerlistenername="DefaultTriggerListener"  
trigger="cron"  
cronb_time="2 * * * * ?" used="false"  
shouldRecover="false"/>    
```

joblistenername元素属性指定了job监听器的名称

joblistenername="DefaultJobListener"

如果有多个listener，配置方法为：

joblistenername="DefaultJobListener,listener1,listener2"

4.全局trigger监听器

全局trigger监听器可以监听所有quartz job任务被触发的事件，配置实例：

Xml代码 

```xml
  <property name="quartz.config.globaltriggerlistener">  
<list componentType="bean">  
<property name="GlobleTriggerListener" class="org.frameworkset.task.GlobalTriggerListener"/>  
</list>  
</property>  
```

可以在list元素中配置多个GlobleTriggerListener监听器，配置顺序就是他们的执行顺序。

org.frameworkset.task.GlobalTriggerListener是bboss缺省提供的GlobleTriggerListener，配置该监听器可以将相关的事件信息用log4j输出到日志文件中，日志级别为info级。

用户可以定义自己的GlobleTriggerListener监听器，必须实现org.frameworkset.task.BaseTriggerListener基础抽象类。

5.trigger监听器

trigger监听器可以监听指定quartz 任务的执行情况，配置实例：

Xml代码

```xml
 <property name="quartz.config.triggerlistener">  
<list componentType="bean">  
<property name="DefaultTriggerListener" class="org.frameworkset.task.DefaultTriggerListener"/>  
</list>  
</property>  
```

可以在list元素中配置多个TriggerListener监听器，然后在特定的任务上面指定相应的监听器的名称即可（可以指定多个，用逗号分隔）。org.frameworkset.task.DefaultTriggerListener是bboss缺省提供的TriggerListener，配置该监听器可以将相关的事件信息用log4j输出到日志文件中，日志级别为info级。

用户可以定义自己的TriggerListener监听器，必须实现org.frameworkset.task.BaseTriggerListener基础抽象类。

在具体的任务上配置trigger listener的实例：

Xml代码

```xml
 <property name="beanmethodjobnoargs" jobid="beanmethodjobnoargs"   
bean-name="methodjob"  
method="actionexception"  
calendar="2012year"  
joblistenername="DefaultJobListener"  
triggerlistenername="DefaultTriggerListener"  
trigger="cron"  
cronb_time="2 * * * * ?" used="false"  
shouldRecover="false"/>    
```

triggerlistenername元素属性指定了trigger监听器的名称

triggerlistenername="DefaultTriggerListener"

如果有多个listener，配置方法为：

triggerlistenername="DefaultTriggerListener,listener1,listener2"

三、日历配置

quartz引擎可以定义各种日历，比如节假日等等，然后可以在任务上指定一个日历，这样就可以结合日历和时间周期规则来执行quartz任务了。

1.日历的定义

我们可以定义以下类型的日历：

AnnualCalendar

CronCalenda

rDailyCalendar

HolidayCalendar

MonthlyCalendar

WeeklyCalendar

我们可以通过配置日历构建类和配置日历脚本（依赖bsh脚本引擎），下面包含两个情况的实例：

Xml代码 

```xml
 <property name="quartz.config.calendar">  
<map>  
<property name="2012year">  
<![CDATA[ 
//法定节日是以每年为周期的，所以使用AnnualCalendar 
AnnualCalendar holidays = new AnnualCalendar(); 
//五一劳动节 
java.util.Calendar laborDay = new GregorianCalendar(); 
laborDay.add(java.util.Calendar.MONTH,5); 
laborDay.add(java.util.Calendar.DATE,1); 
holidays.setDayExcluded(laborDay, true); //排除的日期，如果设置为false则为包含 
//国庆节 
java.util.Calendar nationalDay = new GregorianCalendar(); 
nationalDay.add(java.util.Calendar.MONTH,10); 
nationalDay.add(java.util.Calendar.DATE,1); 
holidays.setDayExcluded(nationalDay, true);//排除该日期 
return holidays; 
]]>  
</property>  
<property name="2013year" class="org.frameworkset.task.TestCalendarBuilder"/>  
</map>  
</property>  
```

org.frameworkset.task.TestCalendarBuilder实现了抽象类org.frameworkset.task.BaseCalendarBuilder，代码如下：

Java代码

```java
package org.frameworkset.task;  
  
import java.util.GregorianCalendar;  
  
import org.quartz.Calendar;  
import org.quartz.impl.calendar.AnnualCalendar;  
public class TestCalendarBuilder extends BaseCalendarBuilder {  
      
    public TestCalendarBuilder() {  
        // TODO Auto-generated constructor stub  
    }  
  
  
  
    public Calendar buildCalendar() {  
        //法定节日是以每年为周期的，所以使用AnnualCalendar  
        AnnualCalendar holidays = new AnnualCalendar();  
        //五一劳动节  
        java.util.Calendar laborDay = new GregorianCalendar();  
        laborDay.add(java.util.Calendar.MONTH,5);  
        laborDay.add(java.util.Calendar.DATE,1);  
        holidays.setDayExcluded(laborDay, true); //排除的日期，如果设置为false则为包含  
        //国庆节  
        java.util.Calendar nationalDay = new GregorianCalendar();  
        nationalDay.add(java.util.Calendar.MONTH,10);  
        nationalDay.add(java.util.Calendar.DATE,1);  
        holidays.setDayExcluded(nationalDay, true);//排除该日期  
        return holidays;  
          
    }  
}  
```

可以配置多个，每个日历都必须有一个唯一的名称，quartz任务通过名称来引用日历。

2.任务上指定日历

实例如下：

Xml代码

```xml
 <property name="beanmethodjobnoargs" jobid="beanmethodjobnoargs"   
bean-name="methodjob"  
method="actionexception"  
calendar="2012year"  
joblistenername="DefaultJobListener"  
triggerlistenername="DefaultTriggerListener"  
trigger="cron"  
cronb_time="2 * * * * ?" used="false"  
shouldRecover="false"/>    
```

通过calendar属性指定日历：

calendar="2012year"

四、触发器配置

quartz提供了四种基本触发器类型trigger，对应于bboss中cron，simple，DateInterval，NthIncludedDay四种触发器类型， 缺省类型为cron,还有一种bboss提供特定类型builder类型，通过这种类型用户可以自己实现TriggerBuilder接口来动态创建自己的Trigger类型，如果任务配置参数有变化还可以重新加载这些参数并重启对应的作业。

所有时间格式采用yyyy-MM-dd HH:mm:ss格式配置，每个trigger可以定义的属性集说明如下。

1.cron 属性集：

timeZone 时区标识

volatility boolean 是否持久化任务

shouldRecover

description

durability boolean 任务失效后是否保留

cronb_time

2.simple 属性集：

startTime 例如：startTime="2013-01-23 16:29:00"

endTime

repeatCount

repeatInterval The number of milliseconds to pause between the repeat firing.

volatility boolean 是否持久化任务

shouldRecover

description

durability boolean 任务失效后是否保留

3.DateInterval 属性集

startTime

endTime

intervalUnit { SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, YEAR }

repeatInterval

volatility boolean 是否持久化任务

shouldRecover

description

durability boolean 任务失效后是否保留

4.NthIncludedDay 属性集

startTime

endTime

fireAtTime HH:MM[:SS]

intervalType WEEKLY MONTHLY YEARLY

N 第几

timeZone 时区标识

volatility boolean 是否持久化任务

shouldRecover

description

durability boolean 任务失效后是否保留

5.builder

builder类型是bboss提供的特殊类型，通过这种类型用户可以自己实现TriggerBuilder接口来动态创建自己的
Trigger类型，如果任务配置参数有变化还可以重新加载这些参数并重启对应的作业。trigger由实现接口org.frameworkset.task.TriggerBuilder的组件方法创建：

public Trigger builder(SchedulejobInfo jobInfo) throws Exception;

通过方法参数SchedulejobInfo可以读取到任务的各种静态配置属性，这些属性可以是cron ，simple，DateInterval，NthIncludedDay四种类型中的属性集，取决于方法返回的Trigger类型（cron ，simple，DateInterval，NthIncludedDay），同时还可以从其他诸如数据库表中读取和加载任务作业属性，如果这些属性发生变化，还可以重启作业实时加载变化后的配置

属性集

自动继承cron ，simple，DateInterval，NthIncludedDay四种类型中的属性集，特有的属性如下：

triggerbuilder-bean 指定trigger生成器为一个bboss ioc 组件

triggerbuilder-class 指定trigger生成器的实现类class路径

用法

当任务时间周期发生变化时，可以调用TaskService接口重启相应的任务：

更新默认组的作业

public void updateExecuteJob(String jobname);

更新指定作业组的任务

public void updateExecuteJob(String groupid, String jobname)

我们在任务上用trigger属性来制定任务的trigger类型，实例如下：

Xml代码 

```xml
 <property name="simplebeanmethodjobnoargs" jobid="simplebeanmethodjobnoargs"   
bean-name="methodjob"  
method="actionexception"  
calendar="2013year"  
triggerlistenername="DefaultTriggerListener"  
trigger="simple"  
startTime="2013-01-23 16:29:00"  
repeatCount="2"  
repeatInterval="2000"  
used="true"  
shouldRecover="false"/>   
```

实例中通过trigger属性指定了任务的trigger类型为simple，然后指定了相应的其他时间属性：startTime，repeatCount，repeatInterval

并且还会结合calendar来设置节假日不执行任务。其他三种类型类似，篇幅关系就不说明了。

重点说明一下Builder类型两种使用方式：

triggerbuilder-class方式

Xml代码

```xml
<property name="simplebeanmethodjobnoargs-triggerbuilderclass" jobid="simplebeanmethodjobnoargs-triggerbuilderclass"                       
                        bean-name="methodjob"  
                        method="actionexception"  
                        calendar="2013year"  
                        triggerlistenername="DefaultTriggerListener"  
                        trigger="builder"  
                        triggerbuilder-class="org.frameworkset.task.TestTiggerBuilder"                        
                        used="true"   
                        shouldRecover="false"/>  
```

triggerbuilder-bean方式

Xml代码

```xml
<property name="simplebeanmethodjobnoargs-triggerbuilderbean" jobid="simplebeanmethodjobnoargs-triggerbuilderbean"                         
                        bean-name="methodjob"  
                        method="actionexception"  
                        calendar="2013year"  
                        triggerlistenername="DefaultTriggerListener"  
                        trigger="builder"  
                        triggerbuilder-bean="testTiggerBuilder"  
                        used="true"   
                        shouldRecover="false"/>  
```

triggerbuilder-bean方式必须在quartz引擎的ioc容器中配置一个TiggerBuilder组件：

**<property name="testTiggerBuilder" class="org.frameworkset.task.TestTiggerBuilder"/**>TestTiggerBuilder组件的实现代码如下：

Java代码

```java
package org.frameworkset.task;  
  
import java.text.SimpleDateFormat;  
import java.util.Date;  
  
import org.quartz.SimpleTrigger;  
import org.quartz.Trigger;  
  
import com.frameworkset.util.StringUtil;  
import com.frameworkset.util.ValueObjectUtil;  
  
public class TestTiggerBuilder implements TriggerBuilder {    
  
    public Trigger builder(SchedulejobInfo jobInfo) throws Exception {  
                  //builder方法中的各种参数都可以从数据库中获取   
        String s_startTime = jobInfo.getJobPro().getStringExtendAttribute("startTime");  
        Date startTime = null;  
        if(!StringUtil.isEmpty(s_startTime) )  
        {  
            SimpleDateFormat format = ValueObjectUtil.getDefaultDateFormat();  
            startTime = format.parse(s_startTime);  
        }  
        else  
            startTime = new Date();  
          
              
        String s_endTime = jobInfo.getJobPro().getStringExtendAttribute("endTime");  
        Date endTime = null;   
        if(!StringUtil.isEmpty(s_endTime) )  
        {  
            SimpleDateFormat format = ValueObjectUtil.getDefaultDateFormat();  
            endTime = format.parse(s_endTime);  
        }  
        String s_repeatCount = jobInfo.getJobPro().getStringExtendAttribute("repeatCount");  
        int repeatCount = 0;   
        if(!StringUtil.isEmpty(s_repeatCount) )  
        {  
            repeatCount = Integer.parseInt(s_repeatCount);  
        }  
        else  
        {  
            repeatCount = 3;  
        }  
        String s_repeatInterval = jobInfo.getJobPro().getStringExtendAttribute("repeatInterval");  
        long repeatInterval = 0;  
        if(!StringUtil.isEmpty(s_repeatInterval) )  
        {  
            repeatInterval = Long.parseLong(s_repeatInterval);  
        }  
        else  
            repeatInterval = 2000;  
          
        SimpleTrigger simpletrigger = new SimpleTrigger(jobInfo.getId(), jobInfo.getScheduleServiceInfo().getId(),  startTime,  
                 endTime,  repeatCount,  repeatInterval);  
        return simpletrigger;  
    }  
  
}  
```

builder方法中的各种参数都可以从数据库中获取，如果参数有调整则可以调用以下方法重启作业：

Java代码

```java
TaskService taskService = TaskService.getTaskService("org/frameworkset/task/test-quarts-task.xml")//通过加载自定义配置文件生成的quartz引擎;  
taskService.updateExecuteJob("simplebeanmethodjobnoargs-triggerbuilderbean");  
```

或者

Java代码

```java
TaskService taskService = TaskService.getTaskService()//默认quartz引擎;  
taskService.updateExecuteJob("simplebeanmethodjobnoargs-triggerbuilderbean"); 
```

五、任务配置

这里简单地介绍一下bboss quartz组件任务配置方式，主要有三种：

1.实现org.frameworkset.task.Execute接口的配置方式

例如：

Execute接口实现类

Java代码

```java
package org.frameworkset.task;  
  
import java.io.Serializable;  
import java.util.Map;  
  
public class TestJob implements Execute, Serializable {  
  
      
  
    public void execute(Map parameters) {  
        System.out.println("send_count = "+parameters.get("send_count"));  
    }  
  
}  
```

任务配置：

Xml代码

```xml
<property name="workbroker" jobid="workbroker"  
                        action="org.frameworkset.task.TestJob"  
                        cronb_time="0 56 14 * * ?" used="true"  
                        shouldRecover="false"  
                        >  
                        <map>  
                            <property name="send_count" value="2" />  
                        </map>  
                    </property>  
```

如果任务在执行的时候需要一些配置参数，可以通过map来指定，然后可以再任务执行的时候在execute方法的Map参数parameters中获取；如果没有参数则无需配置map子元素。

2.直接配置任务处理类名并指定执行方法的方式

class方式指定 任务程序，method方法对应要执行的方法，通过construction指定方法的参数值，多个参数按照参数的顺序指定多个property属性即可。

Xml代码

```xml
<property name="beanclassmethodjob" jobid="beanclassmethodjob"                         
                        bean-class="org.frameworkset.task.ClassMethodJob"  
                        method="action"   
                        cronb_time="2 * * * * ?" used="true"  
                        shouldRecover="false">  
                        <construction>  
                            <property name="hello" value="hello" />  
                        </construction>  
                    </property>  
```

如果方法没有参数就无需配置construction元素，ClassMethodJob实现类代码如下：

Java代码

```java
package org.frameworkset.task;  
public class ClassMethodJob {  
    public void action(String hello)  
    {  
        System.out.println("ClassMethodJob:doing " + hello);  
    }  
      
    public void action()  
    {  
        System.out.println("ClassMethodJob:doing no params");  
    }  
  
}  
```

3.组件方式指定任务执行程序

通过bean-name指定任务程序组件，method指定要执行的方法，通过construction指定方法的参数值，多个参数按照参数的顺序指定多个property属性即可。

Xml代码

```xml
<property name="beanmethodjob" jobid="beanmethodjob"                       
                        bean-name="methodjob"  
                        method="action"  
                        cronb_time="2 * * * * ?" used="true"  
                        shouldRecover="false">  
                        <construction>  
                            <property name="hello" value="hello" />  
                        </construction>  
                    </property>  
```

bean-name="methodjob"指定组件的名称，如果方法没有参数就无需配置construction元素，对应的组件必须在quartz-task.xml文件中配置：

Xml代码

```xml
<property name="methodjob" class="org.frameworkset.task.MethodJob"/>  
```

组件的定义遵行bboss ioc的语法规范，可以在quartz-task.xml文件中配置，也可以引用其他ioc容器中的组件，我们看一个引用mvc 容器中配置的组件的实例：

Xml代码

```xml
<!--首先通过工厂模式获取mvc容器对像-->  
    <property name="webapplicationcontext" factory-class="org.frameworkset.web.servlet.support.WebApplicationContextUtils" factory-method="getWebApplicationContext"/>  
<!--逾期服务类，该组件引用了mvc容器对象中一个组件-->  
    <property name="exceedServiceImpl" factory-bean="webapplicationcontext" factory-method="getBeanObject">  
        <construction>  
            <property value="exceed.service.ExceedServiceImpl" />  
        </construction>  
    </property>  
```

六、任务的启用和停用

bboss中管理的quartz任务可以通过配置非常方便地开启和关闭：

1.全局关闭和开启quartz引擎

首先找到quartz-task.xml或者你自己的配置文件中的以下配置：

Xml代码 

```xml
<property name="taskconfig" enable="false"> 
```

然后修改enable属性值为true，表示开启quartz引擎，false表示关闭quartz引擎。enable设置为false，则执行下述方法时将不会启动quartz引擎：

Java代码 

```java
TaskService.getTaskService().startService();   
TaskService.getTaskService("quartz-custorm.xml").startService(); 
```

2.特定任务的开启和关闭

如果不想运行某个quartz任务，那么可以通过任务的used属性进行控制：

used 为true时启用任务，为false时关闭任务

以下是一个配置示例：

<property name="browserCounterStatisticDailyJob" jobid="browserCounterStatisticDailyJob"

bean-name="browserCounterStatisticDailyJob"

method="statisticBrowserCounterDailyJob"

cronb_time="0 0 0am * * ?" **used="false"**

shouldRecover="false"

**</property**>

  至此bboss quartz组件的功能就全部介绍完毕。

参考文档：

[基于quartz任务调度组件的使用](http://yin-bp.iteye.com/blog/714802)  