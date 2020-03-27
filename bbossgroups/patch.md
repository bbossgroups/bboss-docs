### 发布bbossgroups-2.0-RC补丁-bboss rpc classcast and timeout exception patch.zip

o 修复严重错误，该问题表现为，对一个单实例的远程服务组件并发发起多个方法调用时会出现以下现象：

​     请求响应结果丢失，导致请求超时异常（timeout exception）.

​     一个rpc请求接收其他请求的结果，导致不可以预料的错误，比如类型转换错误.

  出现上述问题的前提是，请求的url地址带有请求参数，例如：

RPCTestInf testInf = (RPCTestInf)BaseSPIManager.getBeanObject("(rmi::172.16.17.216:1099)/rpc.test?server_uuid=app1");

​    testInf.getCount();

  修改程序为：

  /bbossaop/src/org/frameworkset/spi/remote/RPCMessage.java

  发布程序补丁bboss rpc classcast and timeout exception patch.zip

  补丁下载地址：

 https://sourceforge.net/projects/bboss/files/bbossgroups-2.0-RC/bboss%20rpc%20classcast%20and%20timeout%20exception%20patch.zip/download

  下载本程序补丁后，解压覆盖/bbossaop/src/org/frameworkset/spi/remote/RPCMessage.java程序，然后ant重新构建即可