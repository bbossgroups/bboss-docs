### 扩展Activiti-5.12轻松实现流程节点间自由跳转和任意驳回/撤回

由于项目需要，最近对开源工作流引擎Activiti-5.12的功能做了一下扩展，实现了以下功能：

1.自由流（流程节点间自由跳转和任意驳回/撤回）

2.流程会签任务串并行模式切换

**一、自由流**

在已有流程模型的的基础上，每个流程实例当前任务可以任意驳回/撤回或者向后续节点任意跳转而无需在相关的两个节点之间显示地画跳转路径（也就是所谓的“中国式”自由流），通过在Activiti的流程组件TaskService接口中增加以下两个方法来达到上述目的：

public void complateTask(String taskid,String destTaskKey)

public void complateTask(String taskid,Map<String,Object> vars,String destTaskKey)

这两个方法和以下原始方法相比较就是多了一个destTaskKey参数：

public void complateTask(String taskid)

public void complateTask(String taskid,Map<String,Object> vars)

destTaskKey参数告诉流程引擎在结束taskID任务后直接跳转到destTaskKey对应的任务节点，目前可以实现简单人工任务和会签人工任务的驳回/撤回或者向后连跳n个节点，暂不支持子流程任务和流程调用任务。

注意：我们只是对activiti进行了功能扩展，因此activiti原来所有功能没有受到任何影响，该怎么用还是怎么用，只是如果流程中临时有任意驳回/撤回需求或者任意跳转需求时（为了避免在流程图中画N X N条路径，同时也不想修改流程模型），就可以通过上述两个扩展接口轻松搞定。

另外在TaskService接口中增加了驳回到上个环节的默认接口：
/**

*将当前任务驳回到上一个任务处理人处，并更新流程变量参数

*@param taskId

*@param variables

*/

  void rejecttoPreTask(String taskId, Map<String, Object> variables);



 /**

*将当前任务驳回到上一个任务处理人处

*@param taskId

*/

  void rejecttoPreTask(String taskId);

**2.流程会签任务串并行模式切换**

如果流程中存在多实例任务（会签任务），那么可以通过流程变量在处理任务时或者在发起流程实例的时候设置多实例任务执行的方式为串行还是并行。

**切换方式一** 在发起流程时设置对应的会签任务的是串行还是并行模式：

Java代码 

```java
Map variables= new HashMap();  
            variables.put("usertask1_user", Arrays.asList("user1".split(",")));  
            variables.put("usertask2_user", Arrays.asList("user2,user22,user23".split(",")));  
            variables.put("usertask3_user", Arrays.asList("user3,user32".split(",")));  
            variables.put("usertask4_user", Arrays.asList("user4,user42".split(",")));  
            params.put("usertask5_user", Arrays.asList("user5".split(",")));  
            variables.put("businessType", "test");  
            /** 
             * 会签任务usertask2流程模型中被定义并行模式，这里在流程实例启动时通过流程变量将会签任务usertask2的并行模式改为串行模式 
             * 然后该流程实例的对应会签任务usertask2将以串行方式执行 
             */  
            variables.put("usertask2"+MultiInstanceActivityBehavior.multiInstanceMode_variable_const, MultiInstanceActivityBehavior.multiInstanceMode_sequential);  
ProcessInstance processInstance = runtimeService  
                .startProcessInstanceByKey(process_key, businessKey, map);  
```

**切换方式二** 在完成任务时设置后续会签任务的是串行还是并行模式：

Java代码

```java
/** 
                     * 会签任务usertask2在对应的流程实例启动时已经被切换为串行模式，这里在完成usertask1任务时通过流程变量将会签任务usertask2的串行模式再改为并行模式 
                     * 然后该流程实例的对应会签任务usertask2将以并行方式执行 
                     */  
Map variables= new HashMap();                   variables.put("usertask2"+MultiInstanceActivityBehavior.multiInstanceMode_variable_const, MultiInstanceActivityBehavior.multiInstanceMode_parallel);  
taskService.claim(taskId, username);//领取任务  
taskService.complete(taskId, variables);//完成任务并设置后续会签任务的执行模式  
```

变量的命名规范为：

*taskkey.bpmn.behavior.multiInstance.mode

*取值范围为：

*parallel 对应于常量MultiInstanceActivityBehavior.multiInstanceMode_parallel

*sequential MultiInstanceActivityBehavior.multiInstanceMode_sequential

*说明：taskkey为对应的任务的定义id，根据具体的会签任务来取值，.bpmn.behavior.multiInstance.mode部分是固定值，对应常量MultiInstanceActivityBehavior.multiInstanceMode_variable_const

  *

*会签多实例任务执行模式可以在设计流程时统一配置，在任务处理时每个实例中的会签任务都可以通过变量设置和改变串并行模式，变量可以启动流程实例时动态设置，也可以在上个活动任务完成时设置。

**3.小花絮**

为了在任务历史表act_hi_taskinst表delete_reason_字段的值更加具体和详细，特意为activiti增加了一个全局组件:org.activiti.engine.impl.identity.UserInfoMap

Java代码

```java
public interface UserInfoMap {  
    String getUserName(String userAccount);//根据账号获取用户中文名称  
    Object getUserAttribute(String userAccount,String userAttribute);//根据账号获取用户的属性值  
    Object getUserAttributeByID(String userID,String userAttribute);//根据ID获取用户的属性值  
  
}  
```

UserInfoMap用来为流程引擎中的相关功能提供获取流程处理人各种和业务相关的属性的方法。通过在流程全局配置文件activiti.cfg.xml中为流程引擎配置UserInfoMap组件：

Xml代码 

```xml
<property name="userInfoMap" class="com.frameworkset.platform.sysmgrcore.purviewmanager.PDPUserInfoMapImpl"/>  
```

  具体配置请查看案例文件：[activiti.cfg.xml](https://github.com/yin-bp/activiti-engine-5.12/blob/master/src/test/resources/activiti.cfg.xml)

[org.activiti.engine.impl.identity.UserInfoMapImpl](https://github.com/yin-bp/activiti-engine-5.12/blob/master/src/main/java/org/activiti/engine/impl/identity/UserInfoMapImpl.java)是UserInfoMap组件的默认实现。  

以下是应用UserInfoMap组件后act_hi_taskinst表delete_reason_字段的几个值内容实例：

**[提交-usertask1]任务被[张三-zhangsan]完成**

**[会签并行-usertask2]任务被[李四-lisi]转到节点[提交-usertask1]**

**[提交-usertask1]任务被[张三-zhangsan]完成**

**[会签并行-usertask2]任务被[张三-zhangsan]完成**

**[会签并行-usertask2]任务被[李四-lisi]完成**

**[会签并行-usertask2]任务被[王五-wangwu]完成**

**[驳回测试-usertask5]任务被[阿甘-agan]完成**

**[审批校验-usertask6]任务被[欧巴马-oubama]完成**

  **4.后记**

本次改造是基于[bboss 3.6.2](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.2)分支和[Activiti 5.12.0](https://github.com/yin-bp/activiti-engine-5.12)版本，都是目前两个开源项目的最新版本或者较新版本（activiti最新小版本为5.12.1）。  

相关文档：
[开源工作流引擎activiti与bboss整合使用方法浅析](http://yin-bp.iteye.com/blog/1506335)