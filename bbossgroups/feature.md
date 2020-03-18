### bbossgroups的特色特点介绍

很多朋友都在问bbossgroups框架和其他mvc框架 、ssh 或者 ssi 比起来有那些优势？本文就先介绍一下bbossgroups的一些特点，总结了10点：

1.bbossgroups的mvc控制器方法参数绑定机制应该算是业界最好的控制器方法参数绑定机制了，支持的类型非常全：基础数据类型、日期类型、枚举类型、数组、map对象，map<key,bean>类型，list对象，list<**bean>**类型,MultipartFile(附件上传)/MultipartFile[]等等，而且使用非常简单，页面越复杂，bg的优势就体现的越明显。

控制器响应直接通过ResponseBody注解支持File（文件下载）、String，对象转json，rss/atom对象的处理。

2.bbossgroups的提供了一整套的方案：mvc，aop/ioc,持久层，标签库，rpc服务调用，webservice服务发布及调用，rmi服务发布及调用，集群事件框架、集成quartz任务调度组件，提供简单的jms开发api等等

3.bbossgroups的aop/ioc、mvc配置语法非常简洁，这是struts，spring都没法比的；而且配置语法的可扩展性也非常强，你只需要像html元素一样在bg的xml元素property上添加自己需要的属性，而且可以非常方便地通过bg的api获取这些属性。

4.bg的组件方法异步调用机制，也是一大特色，感兴趣的朋友不妨一试    

5.bg的持久层框架支持简单的数据库事务管理、也支持多个数据库之间的事务管理，控制事务的方式多样化：可编程事务管理、声明式事务管理、注解事务管理、jdbctemplate事务管理，提供有效的事务泄露检测和回收机制；持久层api非常丰富，提供完整的o/r mapping实现，提供强大分页查询、行处理器机制，查询返回结果多样化：bean，list<bean>,map,List<map>,分页对象；提供指定数据源上db操作的api；支持sql语句xml配置文件，支持动态sql， sql修改后可热部署，大大方便sql语句的管理和调试。

6.bg中包含的标签库提供了丰富的界面数据展示标签、逻辑判断标签、国际化标签、mvc数据绑定错误信息展示标签，这些标签简洁和实用

7.bg mvc框架支持xml配置方式管理的控制器，支持java注解方式的控制器，非常全面地支持restful功能

8.bg和exjs，jquery，jquery ui，jquery easyui能够组合起来形成当今主流的j2ee开发技术解决方案  

9.bg提供了高效的对象xml序列化功能，能够将所有的对象结构快速地转换为xml结构，同时也非常方便地将这些xml结构反序列化为相应的对象结构。

10.最后一点，bg是国人自主开发的综合型j2ee开发框架  

bbossgroups官方网站：
[http://www.bbossgroups.com](http://www.bbossgroups.com/)

博客：
[http://yin-bp.iteye.com](http://yin-bp.iteye.com/)

项目下载地址：
https://sourceforge.net/projects/bboss/files/

交流群：

群号：3625720 群号：154752521 群号：21220580  