### 平台日志组件使用介绍

需要导入的类：

Java代码

```java
import com.frameworkset.platform.sysmgrcore.entity.Organization;  
import com.frameworkset.platform.sysmgrcore.manager.LogManager;  
import com.frameworkset.platform.sysmgrcore.manager.SecurityDatabase;  
```

如果是登录用户，记录日志的方法为：

Java代码 

```java
 try {  
              
            LogManager logMgr = SecurityDatabase.getLogManager();  
            //以下是以一个quartz任务执行日志记录为实例说明日志组件的使用方法  
            AccessControl control = AccessControl.getAccessControl();  
            String userAccount = "";//操作账号  
            String operContent = "";//操作内容  
            String machineID = "";//操作主机标识  
            String orgID = "";//操作员所属部门id  
            userAccount = control.getUserAccount();  
            String userName = control.getUserName();//操作员中文名称  
            String subsystem = control.getCurrentSystemName();//操作系统名称  
            machineID = control.getMachinedID();//客户端ip信息  
            Organization org = control.getChargeOrg();//获取当前用户所属机构对象  
            if(org != null)  
            {  
                orgID = org.getOrgId();  
            }  
            operContent = userAccount + "(" + userName + ") 从[" + subsystem + "]同步用户数据开始";            
            String operModle = "主数据同步";//日志所属模块  
            logMgr.log(userAccount,orgID,operModle,  machineID,  
                    operContent ,"", Log.INSERT_OPER_TYPE);       
              
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
//日志记录结束  
```

如果记录日志时，用户可能登录系统，也可能没有登录系统，记录日志时需判断用户身份（是否登录）。以下是以一个quartz任务执行日志记录为实例说明日志组件的这种使用方法：

Java代码

```java
try {  
              
            LogManager logMgr = SecurityDatabase.getLogManager();  
            //以下是以一个quartz任务执行日志记录为实例说明日志组件的使用方法  
            AccessControl control = AccessControl.getAccessControl();  
            String userAccount = "";//操作账号  
            String operContent = "";//操作内容  
            String machineID = "";//操作主机标识  
            String orgID = "";//操作员所属部门id  
            if(control == AccessControl.getGuest())//匿名用户-guest，登录用户直接忽略这个条件进入下一个环节  
            {  
                  
                machineID = SimpleStringUtil.getHostIP();  
                userAccount = "Quartz定时任务";  
                operContent = userAccount + "同步用户数据开始";  
            }  
            else //登录用户  
            {  
                userAccount = control.getUserAccount();  
                String userName = control.getUserName();//操作员中文名称  
                String subsystem = control.getCurrentSystemName();//操作系统名称  
                machineID = control.getMachinedID();//客户端ip信息  
                Organization org = control.getChargeOrg();//获取当前用户所属机构  
                if(org != null)  
                {  
                    orgID = org.getOrgId();  
                }  
                operContent = userAccount + "(" + userName + ") 从[" + subsystem + "]同步用户数据开始";  
            }             
            String operModle = "主数据同步";//日志所属模块  
            logMgr.log(userAccount,orgID,operModle,  machineID,  
                    operContent ,"", Log.INSERT_OPER_TYPE);           
              
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
```

