### bbossgroups 对象xml序列化/反序列化性能测试

本文探讨开源项目bbossgroups 中对象xml序列化/反序列化性能测试。

##### **1.测试和源码工程下载地址**

bboss soa工程源码下载(解压后子目录bboss-soa工程)
[bbossgroups soa](http://www.bbossgroups.com/file/download.htm?fileName=bbossgroups-3.5.zip)

测试用例java代码下载（包含在bboss soa的test目录下）
[TestSerializable.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-soa/test/org/frameworkset/soa/xblink/TestSerializable.java)

##### **2.用例使用代码**

###### **2.1 定义两个类，Person与PhoneNumber**

Java代码  

```java
public class Person {     
    private String firstname;     
    private String lastname;     
    private PhoneNumber phone;     
    private PhoneNumber fax;     
    // ... constructors and methods     
}     
    
public class PhoneNumber {     
    private int code;     
    private String number;     
    // ... constructors and methods     
}    
  
public class Person {  
    private String firstname;  
    private String lastname;  
    private PhoneNumber phone;  
    private PhoneNumber fax;  
    // ... constructors and methods  
}  
  
public class PhoneNumber {  
    private int code;  
    private String number;  
    // ... constructors and methods  
}   
```

###### **2.2 实例化一个Person对象**

Java代码

```java
PhoneNumber phone = new PhoneNumber();     
phone.setCode(123);     
phone.setNumber("1234-456");     
    
PhoneNumber fax = new PhoneNumber();     
fax.setCode(123);     
fax.setNumber("9999-999");     
    
Person joe = new Person();     
joe.setFirstname("Joe");     
joe.setLastname("Walnes");     
joe.setPhone(phone);     
joe.setFax(fax);    
```

###### **2.3 序列化接口调用**

Java代码

```java
String xml = ObjectSerializable.convertBeanObjectToXML("person",joe,Person.class);  
```

###### **2.4 反序列化接口调用**

Java代码

```java
Person person = ObjectSerializable.convertXMLToBeanObject("person", xml, Person.class);  
```

##### **3.测试用例执行结果**

###### **3.1 序列化耗时统计**  

执行bboss beantoxml 1次，耗时:0毫秒

执行bboss beantoxml 10次，耗时:0毫秒

执行bboss beantoxml 100次，耗时:16毫秒

执行bboss beantoxml 1000次，耗时:62毫秒

执行bboss beantoxml 10000次，耗时:266毫秒

和xstream的序列化耗时对比（基于同样的测试数据和测试环境）

执行XStream beantoxml 1次，耗时:0毫秒

执行xtream beantoxml 10次，耗时:0毫秒

执行xtream beantoxml 100次，耗时:63毫秒

执行xtream beantoxml 1000次，耗时:140毫秒

执行xtream beantoxml 10000次，耗时:891毫秒

###### **3.2 反序列化耗时统计**

执行bboss xmltobean 1次，耗时:0豪秒

执行bboss xmltobean 10次，耗时:31毫秒

执行bboss xmltobean 100次，耗时:250毫秒

执行bboss xmltobean 1000次，耗时:1203毫秒

执行bboss xmltobean 10000次，耗时:8438毫秒

和Xstream反序列化耗时对比（基于同样的测试数据和测试环境）

执行XStream xmltobean 1次，耗时:0豪秒

执行xStream xmltobean 10次，耗时:0毫秒

执行xStream xmltobean 100次，耗时:62毫秒

执行xStream xmltobean 1000次，耗时:188毫秒

执行xStream xmltobean 10000次，耗时:1344毫秒

从运行测试用例的效果来看，bboss序列化耗时情况还可以，效率比XStream要高出很多；但是bboss反序列化比xStream的反序列化要慢一些，还需进一步优化。
    

##### **4.Person对象序列化输出的xml串**

Xml代码

```xml
<?xml version="1.0" encoding="gbk"?>  
<properties>  
    <property name="person" class="org.frameworkset.soa.xblink.Person">  
        <property name="fax" class="org.frameworkset.soa.xblink.PhoneNumber">  
            <property name="code" soa:type="int" value="123" />  
            <property name="number" soa:type="String"><![CDATA[9999-999]]></property>  
        </property>  
        <property name="firstname" soa:type="String"><![CDATA[Joe]]></property>  
        <property name="lastname" soa:type="String"><![CDATA[Walnes]]></property>  
        <property name="phone" class="org.frameworkset.soa.xblink.PhoneNumber">  
            <property name="code" soa:type="int" value="123" />  
            <property name="number" soa:type="String"><![CDATA[1234-456]]></property>  
        </property>  
    </property>  
</properties>  
```

##### **5.测试环境**

联想thinkpad sl400

OS 32位windows xp professional sp3

内存2G

cpu:

型号 Intel(R) Core(TM)2 Duo T5870

主频 2GHz

用例运行工具：myeclipse 8.0

jdk 1.5.0_06  