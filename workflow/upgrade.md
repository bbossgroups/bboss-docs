### 扩展Activiti 5.12轻松搞定流程实例跟随流程版本一起升级

扩展Activiti 5.12轻松搞定流程实例跟随流程版本一起升级，本文详细介绍之

本功能依托于[bboss 3.6.2](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.2)分支和[Activiti 5.12.0](https://github.com/yin-bp/activiti-engine-5.12)版本。

为activiti组件org.activiti.engine.repository.DeploymentBuilder增加以下接口方法：

Deployment deploy(int deploypolicy);

参数deploypolicy为int类型，用来指定流程部署策略，有三个值：

DeploymentBuilder.Deploy_policy_default

DeploymentBuilder.Deploy_policy_upgrade

DeploymentBuilder.Deploy_policy_delete

这三个值作为常量定义在部署接口org.activiti.engine.repository.DeploymentBuilder中。他们的含义分别为：

DeploymentBuilder.Deploy_policy_default 没有执行完毕的旧版本实例任务仍然根据旧版本流程定义运行

DeploymentBuilder.Deploy_policy_upgrade 没有执行完毕的旧版本实例任务迁移到新版本流程定义运行

DeploymentBuilder.Deploy_policy_delete 直接取消没有执行完毕的旧版本实例任务

原来的部署接口方法任然保留：

Deployment deploy();

以下是两个简单的使用扩展接口部署流程示例：

Java代码

```java
public Deployment deployProcDefByZip(String deploymentName,  
            ZipInputStream processDef,int upgradepolicy) {  
        DeploymentBuilder deploymentBuilder = processEngine  
                .getRepositoryService().createDeployment().name(deploymentName);  
        deploymentBuilder.addZipInputStream(processDef);          
        /** 
         * 参数upgradepolicy可以为以下常量值： 
         *  DeploymentBuilder.Deploy_policy_default  
         *  DeploymentBuilder.Deploy_policy_upgrade  
         *  DeploymentBuilder.Deploy_policy_delete  
         * 
         */           
        return deploymentBuilder.deploy(upgradepolicy);  
    }  
```

Java代码

```java
public Deployment deployProcDefByPath(String deploymentName,  
            String xmlPath, String jpgPath,int deploypolicy) {  
        Deployment deploy = null;  
        /** 
         * 参数deploypolicy可以为以下常量值： 
         *  DeploymentBuilder.Deploy_policy_default  
         *  DeploymentBuilder.Deploy_policy_upgrade  
         *  DeploymentBuilder.Deploy_policy_delete  
         * 
         */  
        if(jpgPath != null && !jpgPath.equals(""))  
        {  
            deploy = repositoryService.createDeployment()  
                    .name(deploymentName).addClasspathResource(xmlPath)  
            .addClasspathResource(jpgPath).deploy(deploypolicy);  
        }  
        else  
        {  
            deploy = repositoryService.createDeployment()  
                .name(deploymentName).addClasspathResource(xmlPath).deploy(deploypolicy);  
        }  
  
        return deploy;  
    }  
```

