### bboss mvc忽略对bean属性进行参数绑定方法

bboss mvc忽略对bean属性进行参数绑定方法非常简单，通过在bean的属性上添加@IgnoreBind注解即可，参考示例如下：

Java代码

```java
import org.frameworkset.util.annotations.IgnoreBind;  
public class ExampleBean  
{  
    @IgnoreBind  
    private String name = null;  
  
      
    public String getName()  
    {  
      
        return name;  
    }  
  
      
    public void setName(String name)  
    {  
      
        this.name = name;  
    }  
  
}  
```

@IgnoreBind另外的一个作用：如果对象存在自引用关系，可以用来防止mvc参数绑定时出现死循环导致堆栈溢出的问题，例如对象结构为：

Java代码

```java
public class UserBusinessBean {  
  
    private String id;// 主键id  
  
    private String userId;// 用户id  
  
    private String userRealname;// 用户名称  
  
    private String modelId;// 设备型号id  
  
    private String equipmentModel;// 设备型号  
  
    private String geographicalPosition;// 地理位置  
  
    private String faultId;// 故障部位id  
  
    private String faultPosition;// 故障部位  
  
    @IgnoreBind  
    private List<UserBusinessBean> businessList;  
  
    public String getId() {  
        return id;  
    }  
  
    public void setId(String id) {  
        this.id = id;  
    }  
  
    public String getUserId() {  
        return userId;  
    }  
  
    public void setUserId(String userId) {  
        this.userId = userId;  
    }  
  
    public String getModelId() {  
        return modelId;  
    }  
  
    public void setModelId(String modelId) {  
        this.modelId = modelId;  
    }  
  
    public String getGeographicalPosition() {  
        return geographicalPosition;  
    }  
  
    public void setGeographicalPosition(String geographicalPosition) {  
        this.geographicalPosition = geographicalPosition;  
    }  
  
    public String getFaultId() {  
        return faultId;  
    }  
  
    public void setFaultId(String faultId) {  
        this.faultId = faultId;  
    }  
  
    public String getEquipmentModel() {  
        return equipmentModel;  
    }  
  
    public void setEquipmentModel(String equipmentModel) {  
        this.equipmentModel = equipmentModel;  
    }  
  
    public String getFaultPosition() {  
        return faultPosition;  
    }  
  
    public void setFaultPosition(String faultPosition) {  
        this.faultPosition = faultPosition;  
    }  
  
    public String getUserRealname() {  
        return userRealname;  
    }  
  
    public void setUserRealname(String userRealname) {  
        this.userRealname = userRealname;  
    }  
  
      
    public List<UserBusinessBean> getBusinessList() {  
        return businessList;  
    }  
  
      
    public void setBusinessList(List<UserBusinessBean> businessList) {  
        this.businessList = businessList;  
    }  
  
  
}  
```

UserBusinessBean对象中定义了以下属性businessList：
**@IgnoreBind**
    **private List**    < **UserBusinessBean>** **businessList;**

如果不加 @IgnoreBind注解，那么mvc在绑定UserBusinessBean类型的参数时，就导致堆栈溢出，加上就没问题，以下是可能出现的堆栈溢出错误信息以供参考：

Java代码 

```java
Java frames: (J=compiled Java code, j=interpreted, Vv=VM code)  
v  ~RuntimeStub::StackOverflowError throw_exception  
J  sun.nio.cs.ext.DoubleByte$Encoder.encodeArrayLoop(Ljava/nio/CharBuffer;Ljava/nio/ByteBuffer;)Ljava/nio/charset/CoderResult;  
J  sun.nio.cs.ext.DoubleByte$Encoder.encodeLoop(Ljava/nio/CharBuffer;Ljava/nio/ByteBuffer;)Ljava/nio/charset/CoderResult;  
J  java.nio.charset.CharsetEncoder.encode(Ljava/nio/CharBuffer;Ljava/nio/ByteBuffer;Z)Ljava/nio/charset/CoderResult;  
J  sun.nio.cs.StreamEncoder.implWrite([CII)V  
J  sun.nio.cs.StreamEncoder.write([CII)V  
J  sun.nio.cs.StreamEncoder.write(Ljava/lang/String;II)V  
J  java.io.OutputStreamWriter.write(Ljava/lang/String;II)V  
J  java.io.Writer.write(Ljava/lang/String;)V  
J  org.apache.log4j.helpers.CountingQuietWriter.write(Ljava/lang/String;)V  
J  org.apache.log4j.WriterAppender.subAppend(Lorg/apache/log4j/spi/LoggingEvent;)V  
J  org.apache.log4j.RollingFileAppender.subAppend(Lorg/apache/log4j/spi/LoggingEvent;)V  
J  org.apache.log4j.WriterAppender.append(Lorg/apache/log4j/spi/LoggingEvent;)V  
J  org.apache.log4j.AppenderSkeleton.doAppend(Lorg/apache/log4j/spi/LoggingEvent;)V  
J  org.apache.log4j.helpers.AppenderAttachableImpl.appendLoopOnAppenders(Lorg/apache/log4j/spi/LoggingEvent;)I  
J  org.apache.log4j.Category.callAppenders(Lorg/apache/log4j/spi/LoggingEvent;)V  
J  org.apache.log4j.Category.debug(Ljava/lang/Object;)V  
J  org.frameworkset.web.servlet.handler.HandlerUtils.newCommandObject(Ljava/lang/Class;)Ljava/lang/Object; 
```

