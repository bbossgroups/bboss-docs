### 发布紧急补丁-cglib stackoverflow-patch for bbossgroups-2.0-RC

本补丁修改了以下问题：

最近发布的bbossgroups-2.0-RC版本中采用cglib ioc机制时，执行组件方法调用时将报堆栈溢出错误。

修改相关的程序：

/bbossaop/src/org/frameworkset/spi/cglib/BaseCGLibProxy.java

/bbossaop/src/org/frameworkset/spi/cglib/CGLibProxy.java

/bbossaop/src/org/frameworkset/spi/cglib/SynCGLibProxy.java

/bbossaop/src/org/frameworkset/spi/cglib/SynTXCGLibProxy.java

/bbossaop/src/org/frameworkset/spi/ApplicationContext.java

/bbossaop/src/org/frameworkset/spi/cglib/AopProxyFilter.java(新增)

升级方法：

将补丁包中的程序覆盖bbossaop包中的相关程序即可。

bbossgroups-2.0-RC下载地址：

https://sourceforge.net/projects/bboss/files/bbossgroups-2.0-RC/bbossgroups-2.0-RC.zip/download

补丁下载地址：

https://sourceforge.net/projects/bboss/files/bbossgroups-2.0-RC/cglib%20stackoverflow-patch.zip/download