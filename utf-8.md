### bboss框架eclipse工程编码utf-8改造以及框架模块之间依赖关系改造优化完成

经过一段时间的紧张工作，bboss框架eclipse工程编码由gbk编码改造为utf-8编码、框架模块之间依赖关系改造优化终于完成，对应的github分支版本为[3.7.5](https://github.com/bbossgroups/bbossgroups-3.5)。本次改造后的框架功能和api完全兼容以前的版本。

eclipse工程gbk编码转换为utf-8编码的转换方法请参考文章：

[bboss 如何批量将gbk编码的文件转换为utf编码](http://www.iteye.com/topic/1122001)

为了解除bboss标签库对基于框架开发的平台提供的cms.jar(内容管理组件)的依赖，对cms组件进行了接口和实现的分离，将接口部分放入bboss标签库frameworkset.jar包中，实现部分仍然保留在平台cms.jar中；对应安全部分功能，为了解决标签库对平台安全组件的依赖，同样将AccessControl组件剥离为接口和实现两部分，将接口部分放入bboss框架包frameworkset-util.jar中，实现部分仍然放在平台中。这样bboss框架的编译和运行完全就不会依赖于平台的cms.jar组件和安全组件，架构上显得更加优雅和清爽了。

因此基于平台开发的应用系统在升级bboss到[3.7.5](https://github.com/bbossgroups/bbossgroups-3.5)版本的同时也需要同步升级平台最新版本（具体升级方法可随时联系bboss开发团队）

备注：依托bboss开发的平台和内容管理系统提供完备的url权限控制、功能权限和数据权限、安全认证、模块菜单管理、站点管理和发布、文档采编发、数据字典管理、工作流管理等功能。


  