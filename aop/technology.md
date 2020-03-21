### 浅谈 bbossgroups 对象xml序列化技术

本文介绍bbossgroups 中的对象xml序列化技术，涵盖基础数据类型、复杂对象、异常对象、文件对象、二进制数组、容器对象（List，Map，Set，Array）以及各种类型的组合结构，其特点是api简单，转换效率高，生成的xml简洁易懂，可读性好，可以通过aop框架组件管理容器直接加载xml串获取其中的对象。切入正题。

目 录 [ - ]

​    1.对象xml序列化技术实战策略

​    2.对象xml序列化组件及接口

​    3.对象xml序列化技术实例

1.对象xml序列化技术实战策略

对象xml序列化技术实战策略

1.1.从附件中下载本文涉及的实例eclipse工程xmlserializable.zip

http://dl.iteye.com/topics/download/d534eae3-c4c2-3afd-8405-30befd4acfd8

1.2.将xmlserializable.zip 解压后将工程导入eclipse开发环境

1.3.执行测试用例，即可查看本文中涉及功能的实际效果

/xmlserializable/test/org/frameworkset/soa/SOAApplicationContextTest.java

2.对象xml序列化组件及接口

2.1.对象xml序列化组件

org.frameworkset.soa.ObjectSerializable

2.2.对象xml序列化接口

ArrayBean bean1 = new ArrayBean();

String xmlcontent = ObjectSerializable.convertBeanObjectToXML("beanname",beanObject ，ArrayBean.class);

2.3.xml反序列化接口

ArrayBean bean1 = ObjectSerializable.convertXMLToBeanObject("beanname",xmlcontent,ArrayBean.class);

3.对象xml序列化技术实例

对象xml序列化技术非常简单，本实例展示了以下功能：

a.如何将包含文件和异常的对象序列化成xml串，然后又从xml串中恢复这些对象

b.如何将List对像及内部的对象序列化成xml串，然后又从xml串中恢复这些对象

需要特别说明一点的是，文件对象的序列化，我们直接将文件对象对应的文件内容以二进制流的方式序列化到xml串中，反序列化时将文件的二进制流保存在一个临时文件对象中，用户可以很方便地访问文件的内容（后续可以可虑通过注解指定要存放的临时文件的目录，现在直接采用了os默认的临时目录）。

依赖的bboss框架包请及时更新新版本  