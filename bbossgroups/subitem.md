### bbossgroups-3.0发布,新增子项目bboss mvc

bbossgroups-3.0发布

release version : bbossgroups-3.0

release date: 2011/02/26

主要的功能特性：

1.新增的一套mvc框架即bboss-mvc子项目，这是bbossgroups-3.0相比bbossgroups-2.0-RC1最大的变化。
bboss mvc基于bboss aop开发，能够与bboss aop（aop框架，业务组件管理），bboss persistent（持久层框架），bboss taglib（标签库框架）协作完成j2ee项目的开发过程；

bboss mvc很好地实现了mvc 2模型，支持多方法控制器，注解控制器，支持restful功能，能非常方便地与bboss taglib结合实现分页功能；

支持多文件上传功能；

支持基本数据类型、po对象、List<**bean>**对象数据绑定,提供丰富的参数绑定注解；

控制器全部采用单例模式；

利用bboss aop，可以非常方便地将业务组件注入到mvc 控制器中；

扩展容器规范，支持web应用lib目录下的jar按目录分类存放，方便管理应用中日益增多的jar包。

2.同时为了开发mvc框架，对bboss aop，bboss persistent，bboss taglib，bboss util做了功能扩展。

3.bboss aop新增国际化功能

4.bboss aop改进了rpc远程调用协议，使得rpc更加稳定、性能更好

5.bboss aop新增了http/https rpc远程调用协议,可以直接使用应用服务器提供的http协议，很容易地实现aop 组件的远程调用

6.改进了bboss aop 中提供的集群功能

7.改进了bboss aop 中提供的quartz任务调度功能

8.修复了一系列bug

9.bboss persistent中新增了一套简洁易用的api，实现各种常用的数据库操作更加方便。

10.与bbossgroups-3.0一起发布的文档有：《BBoss AOP框架技术白皮书.doc》《bboss mvc开发手册.doc》《frameworkset开发手册.doc》

这些文档放置在发布包的文档目录下。

11.与bbossgroups-3.0一起发布的demo有

mvcdemo.rar

位于文档目录的mvcdemo子目录下

更详细的情况请浏览release note:

http://sourceforge.net/projects/bboss/files/bbossgroups-3.0/README.txt/download

bbossgroups-3.0下载地址：

http://sourceforge.net/projects/bboss/files/bbossgroups-3.0/bbossgroups-3.0.zip/download

bboss group project blog:

http://blog.csdn.net/yin_bp

http://yin-bp.iteye.com/

bbossgroups论坛地址：

[http://www.xtzy.com:800](http://www.xtzy.com:800/)

bboss group project sourceforge site url:

http://sourceforge.net/projects/bboss/files/  