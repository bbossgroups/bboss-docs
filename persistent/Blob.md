### 持久层框架中通过Record对象获取Blob对象值导致java堆栈溢出的问题

从com\frameworkset\common\poolman\Record.java

  获取blob对象堆栈溢出的问题：

  java.lang.StackOverflowError

 at com.frameworkset.common.poolman.Record.getBlob(Record.java:1630)

  原因分析：getBlob 方法陷入死循环

  public Blob getBlob (String parameterName) throws SQLException

{

 return (Blob)this.getBlob(parameterName);

}

修改为：

public Blob getBlob (String parameterName) throws SQLException

​    {

​     return (Blob)this.getObject(parameterName);

​    }

  即可。

bboss 项目下载地址：

https://sourceforge.net/projects/bboss/files