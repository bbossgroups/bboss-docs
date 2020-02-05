# bboss最佳实践gradle工程清单及其作用介绍

## **基于bboss开发项目说明**

要做简单的demo，请参考文档
http://yin-bp.iteye.com/blog/1026261

正儿八经的做项目，参考文档搭bboss平台开发环境：
http://yin-bp.iteye.com/blog/2230399

bboss自动代码生成工具使用指南：
http://yin-bp.iteye.com/blog/2256948
    
如需使用bboss中某个模块，那么这个地方可以找到各模块最小依赖gradle工程，你可以直接在此基础上开启bboss框架开发之旅：
[bboss最佳实践案例](https://github.com/bbossgroups/bestpractice)

在github上可以找到bboss各模块的eclise最佳实践工程，它们的github存放地址为：
https://github.com/bbossgroups/bestpractice
其中的demoproject是比较完整的bboss demo gradle项目，实际项目可以参考该工程搭建自己的项目开发环境。

bestpractice目录下gradle工程说明：
**bboss-gencode**
---bboss自动代码生成框架模块
**demoproject**
----bboss持久层，标签库，mvc，ioc最完整的web gradle工程，实践项目可以参考这个项目搭建bboss的开发环境
**bboss-clientproxy**
----bboss rpc框架demo gradle工程 
**bbossupload**
----bboss mvc文件上传下载web demo gradle工程
**easyuidatagrid**
----bboss整合easy ui datagrid的webdemo gradle工程
**i18n**
--bboss国际化最小依赖的web demo gradle工程
**mvc**
----bboss mvc最小依赖web demo gradle工程
**persistent**
---bboss持久层最小依赖gradle工程
**session**
--bboss会话共享最小依赖web demo gradle工程
**sessionmonitor**
---bboss会话共享带监控管理最小依赖webdemo工程
**xmlrequest**
----bboss mvc xml请求响应最小依赖web demo gradle 工程
**xmlserializable**
---bboss 序列化demo gradle工程

**testredis**
---bboss redis 组件demo gradle工程

**testmongo**
---bboss mongodb 组件demo gradle工程

**websocket**
---bboss websocket服务demo gradle工程

**taglib**
---bboss 标签库demo gradle工程

这些最佳实践工程中都已经而且仅仅包含了bboss相应模块的最小依赖jar包和资源文件，开发人员可以根据实际项目需要参考这些demo gradle工程，将bboss的相应模块引入到自己的项目中。

bboss项目下载地址，参考文档：http://yin-bp.iteye.com/blog/1080824