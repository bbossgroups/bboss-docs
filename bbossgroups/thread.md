### bbossgroups 线程池使用

  1.1    线程池配置

可以在任意的aop xml配置文件中配置线程池，只需将相关的xml配置文件直接或者间接导入manager-provider.xml文件既可，这里以一个thread.xml文件为列来说明线程池的配置：

< ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​    < properties >

​       < property name="test.threadpool" >

​           < map >

​              < property name="corePoolSize" value="5" />

​              < property name="maximumPoolSize" value="10" />

​              <!--

​                  TimeUnit.SECONDS TimeUnit.MICROSECONDS TimeUnit.MILLISECONDS

​                  TimeUnit.NANOSECONDS 时间单位适用于以下参数： keepAliveTime waitTime

​                  delayTime（当delayTime为整数时间而不是百分比时有效）

​              -->

​              < property name="timeUnit" value="TimeUnit.SECONDS" />

​              < property name="keepAliveTime" value="40" />

​              <!--

​                  /** * LinkedBlockingQueue * PriorityBlockingQueue *

​                  ArrayBlockingQueue * SynchronousQueue */

​              -->

​              < property name="blockingQueueType" value="ArrayBlockingQueue" />

​              < property name="blockingQueue" value="10" />  

​         <!--

​                  RejectedExecutionHandler

​                  必须实现java.util.concurrent.RejectedExecutionHandler接口 目前系统提供以下缺省实现：

​                  org.frameworkset.thread.WaitPolicy

​                  循环等待event.threadpool.waitTime指定的时间，单位为秒

​                  java.util.concurrent.ThreadPoolExecutor.DiscardPolicy 直接丢弃任务，不抛出异常

​                  java.util.concurrent.ThreadPoolExecutor.AbortPolicy

​                  直接丢弃任务，抛出异常RejectedExecutionException

​                  java.util.concurrent.ThreadPoolExecutor.CallerRunsPolicy 直接运行

​                  java.util.concurrent.ThreadPoolExecutor.DiscardOldestPolicy

​                  放入队列，将最老的任务删除

​              -->

​              < property name="rejectedExecutionHandler" value="org.frameworkset.thread.WaitPolicy" />

​              <!--  

​              以下参数只有在配置的org.frameworkset.thread.WaitPolicy策略时才需要配置

​               -->

​              < property name="waitTime" value="1" />

​              < property name="delayTime" value="10%" />

​              < property name="maxWaits" value="2" />

​              <property name="waitFailHandler"

​                  value="org.frameworkset.thread.TestThread$WaitFailHandlerTest" />  

​             </ map>

​       </ property>

​    </ properties>

</ manager-config>

将thread.xml文件存储在classes目录下，在manager-provider.xml文件中导入既可：

< managerimport file="thread.xml" />  

可以配置多个线程池，只要< property name="test.threadpool">指定不同的name（线程池的名称）即可。

1.2    获取线程池实例

获取线程池实例的方法如下：

ThreadPoolExecutor

executer =

ThreadPoolManagerFactory.getThreadPoolExecutor("test.threadpool");

获取名称test.threadpool时不会每次都创建线程池的实例，只有第一次获取时才会创建实例，因此是单实例的。如果在获取test.threadpool的线程池是，对应的配置文件中并没有配置test.threadpool那么将采用默认的线程池参数来创建线程池实例。  

  默认的配置信息如下：

Pro pro = new Pro();

​        pro.setName("corePoolSize");pro.setValue("5");

​        defaultPoolparams.put("corePoolSize", pro);

​        pro = new Pro();

​        pro.setName("maximumPoolSize");pro.setValue("10");

​        defaultPoolparams.put("maximumPoolSize", pro);

​        pro = new Pro();

​        pro.setName("keepAliveTime");pro.setValue("30");

​        defaultPoolparams.put("keepAliveTime", pro);

​        pro = new Pro();

​        pro.setName("timeUnit");pro.setValue("TimeUnit.SECONDS");

​        defaultPoolparams.put("timeUnit", pro);

​        pro = new Pro();

​        pro.setName("blockingQueue");pro.setValue("10");

​        defaultPoolparams.put("blockingQueue", pro);  

​       /**

​         \* DelayQueue LinkedBlockingQueue PriorityBlockingQueue

​         \* ArrayBlockingQueue SynchronousQueue

​         */

​        pro = new Pro();

​        pro.setName("blockingQueueType");pro.setValue("ArrayBlockingQueue");

​        defaultPoolparams.put("blockingQueueType", pro);

​        pro = new Pro();  

  pro.setName("rejectedExecutionHandler");pro.setValue("org.frameworkset.thread.RejectRequeuePoliecy");

​        defaultPoolparams.put("rejectedExecutionHandler", pro);

​        pro = new Pro();

​        pro.setName("waitTime");pro.setValue("1");

​        defaultPoolparams.put("waitTime", pro);

​        pro = new Pro();

​        pro.setName("delayTime");pro.setValue("20%");

​        defaultPoolparams.put("delayTime", pro);

​        pro = new Pro();

​        pro.setName("maxWaits");pro.setValue("-1");

​        defaultPoolparams.put("maxWaits", pro);

​        pro = new Pro();

​        pro.setName("maxdelayTime");pro.setValue("4");

​        defaultPoolparams.put("maxdelayTime", pro);  

  1.3    使用线程池来执行任务

1.3.1   定义一个任务

public static class Run implements Runnable

​    {

​        public void run()

​        {

​            if (true)

​            {

​                i ++;

​                System.out.println("run:"+ i);

​                try

​                {

​                    synchronized(this)

​                    {

​                        this.wait(4000);

​                    }

​                }

​                catch (InterruptedException e)

​                {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​                }

​            }

​        }

}

1.3.2   利用线程池来执行任务

一般的使用方法：

ThreadPoolExecutor executer = ThreadPoolManagerFactory.getThreadPoolExecutor("test.threadpool");

for(int i = 0; i < 50 ; i ++)

​       executer.execute(new Run());

如果线程池趋于繁忙时，外部可以通过执行 InnerThreadPoolExecutor类的以下方法来缓解系统的压力（采用等待阻塞延时方法）：

public boolean busy(RejectCallback rejectcallback, BaseLogger log)

例如：    

  InnerThreadPoolExecutor executer = （InnerThreadPoolExecutor）

ThreadPoolManagerFactory.getThreadPoolExecutor("test.threadpool");

for(int i = 0; i < 50 ; i ++)

{

executor.busy(rejectcallback, baselog);

executer.execute(new Run());
}



rejectcallback为org.frameworkset.thread. RejectCallback的子类，

baselog为org.frameworkset.log.BaseLog的类实例。  

1.3.3   线程池的关闭

所有的线程池在jvm关闭时自动关闭。  