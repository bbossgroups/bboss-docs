### bboss自定义类对象序列化机制介绍

**1.概述**

bboss提供了自定义类对象的序列化/反序列化插件机制,这个机制与[bboss序列化功能](http://yin-bp.iteye.com/blog/1375834)相辅相成，为应用程序提供了简单高效的对象序列化功能。

采用bboss自定义类对象序列化机制可以方便地实现无法通过bboss本身序列化机制进行序列化的类对象的序列化功能，例如：

没有定义默认构造函数(不带参数构造函数)的类对象

兼容jvm自带的序列化功能（某些遗留系统库类、已有第三方组件库类、jdk类库中的类，这些类利用jvm序列化机制高度定制他们的序列化行为）。

  本质上讲，[bboss在序列化和反序列化类对象](http://yin-bp.iteye.com/blog/1375834)时，如果检测到特定的类提供了自定义序列化和反序列化插件，就会利用找到的插件来完成对应类对象的序列化和反序列化操作，同时如果配置了对象预处理类，那么首先会对对象进行预处理再序列化。

序列化插件机制作为序列化功能的辅助功能，通过编写自定义序列化插件机制，可以达到以下目标：

1.自定义对象的序列化和反序列化行为

2.对序列化对象进行预处理

3.为类路径定义magic数字别名，降低序列化报文大小  

**2.实现自定义序列化插件**

所有的自定义列化插件都必须继承抽象类

org.frameworkset.soa.BaseSerial<T>

并实现BaseSerial中定义的两个抽象方法：

//序列化方法，将对象转换为String ，如何转换取决于具体方法实现

public String serialize(T object);

//反序列化方法，将String 转换为对象，如何转换取决于具体方法实现

public T deserialize(String object);

所有的预处理类都必须实现接口：

org.frameworkset.soa.PreSerial<T>

Java代码

```java
public interface PreSerial<T> {  
    public T prehandle(T object);  
} 
```

prehandle方法接收预处理的对象，处理完毕再返回相同类型的对象。

以下是bboss默认提供的jvm序列化插件：

org.frameworkset.soa.JDKSerial

Java代码

```java
package org.frameworkset.soa;  
  
import java.io.Serializable;  
  
import org.frameworkset.util.ObjectUtils;  
  
/** 
 * <p>Title: LocalSerial.java</p>  
 * <p>Description: </p> 
 * <p>bboss workgroup</p> 
 * <p>Copyright (c) 2007</p> 
 * @Date 2014年5月23日 上午9:06:42 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class JDKSerial extends BaseSerial<java.io.Serializable> {  
  
    public String serialize(Serializable object) {  
        // TODO Auto-generated method stub  
          
        return ValueObjectUtil  
                .byteArrayEncoder(ObjectUtils.toBytes(object));  
          
    }  
  
      
    public Serializable deserialize(String object) {  
        try  
        {  
            byte[] bytes = ValueObjectUtil.byteArrayDecoder(object);  
            return (Serializable)ObjectUtils.toObject(bytes);  
        }  
        catch(RuntimeException e)  
        {  
            throw e;  
        }  
        catch(Exception d)  
        {  
            throw new SerialException(d);  
        }  
    }     
      
  
}  
```

JDKSerial插件可用于那些必须采用jvm序列化机制才能序列化的类对象，比如

net.sf.jasperreports.engine.JasperPrint

下面是专门序列化java.util.Locale对象的插件

org.frameworkset.soa.LocaleSerial

Java代码 

```java
public class LocaleSerial extends BaseSerial<Locale>{  
  
    public LocaleSerial() {  
        // TODO Auto-generated constructor stub  
    }  
  
    public String serialize(Locale object) {  
        // TODO Auto-generated method stub  
        return object.toString();  
    }  
  
    public Locale deserialize(String object) {  
        // TODO Auto-generated method stub  
        return new Locale(object);  
    }  
  
}  
```

**3.自定义类型序列化插件配置**

序列化插件定义完毕，即可以将其配置到序列化插件配置文件serialconf.xml中，文件存放路径为：

org/frameworkset/soa/serialconf.xmlbboss

默认提供java.util.Locale和net.sf.jasperreports.engine.JasperPrint两个类对象的序列化插件JDKSerial：

Xml代码

```xml
 <!--   
自定义对象序列化插件，对于bboss无法序列化的对象，可以自行定义序列化插件，只要将对象序列化和反序列化机制定义好即可  
改造序列化插件，增加preseial机制，以便支持用户自行对通过hibernate查询得到代理po对象进行预处理（比如值复制）再进行序列化  
解决hibernate查询对象无法序列化问题  
 -->  
 <properties>  
     <property name="java.util.Locale" magic="1" serial="org.frameworkset.soa.LocaleSerial"/>  
     <property name="net.sf.jasperreports.engine.JasperPrint" magic="2" serial="org.frameworkset.soa.JDKSerial"/>  
<!--      <property name="com.xxx.xxx.POXxxxx" magic="3" preserial="org.frameworkset.soa.POXxxxxSerial"/> -->  
 </properties>  
```

插件配置采用bboss ioc配置语法，property 元素包含四个属性，含义分别说明如下：

**name代表需要序列化的class**

**magic属性代表类编号（类序列化编号不能重复）**

**serial指定序列化的插件，插件必须继承抽象类*****org.frameworkset.soa.BaseSerialpreserial-指定po对象进行预处理处理类，以便在序列化对象时先进行预处理，再对预处理后的对象进行序列化，预处理类必须实现接口**

**org.frameworkset.soa.PreSerial**

**serial和preserial两个属性可以指定其中的一个，可以同时都指定，也可以都不指定；如果都指定，首先用preserial对应的预处理类对序列化对象进行预处理，然后在用serial对应的插件来序列化和反序列化预处理后的对象，如果都不指定则表示定义了name对应的类的一个简单别名以便降低报文大小。**

**serial和preserial都是单例的。**

bboss序列化对象时，只要碰到java.util.Locale类型的对象或者对象属性就会采用LocaleSerial插件来实现序列化和反序列化，只要碰到net.sf.jasperreports.engine.JasperPrint类型的对象或者对象属性就会采用JDKSerial插件来实现序列化和反序列化。

**4.自定义类型序列化插件使用场景**

**简单的实例：序列化java.util.Locale对象**

Java代码

```java
try {  
            java.util.Locale locale = java.util.Locale.CHINESE;  
            String xml = ObjectSerializable.toXML(locale);  
            locale = ObjectSerializable.toBean(xml, java.util.Locale.class);  
            System.out.println(locale);  
        } catch (Exception e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }   
```

**稍复杂的实例：改造session共享场景下JasperPrint的存储和读取**

利用bboss自定义序列化插件机制，文章[《bboss session共享时序列化存储jasperreports报表对象JasperPrint方法》](http://yin-bp.iteye.com/blog/2064927)中将JasperPrint对象设置到共享session中和从[共享session](http://yin-bp.iteye.com/blog/2064662)中获取JasperPrint方法可以改写为：

Java代码

```java
public static void toSession(HttpServletRequest request,String key,JasperPrint jasperPrint) throws IOException  
    {  
        request.getSession().setAttribute(key, jasperPrint);          
    }  
      
    public static JasperPrint getJasperPrint(HttpServletRequest request,String key)  
    {  
        return (JasperPrint)request.getSession().getAttribute(key);  
  
    }  
```

改造后代码显得更加简洁，充分展示出采用bboss序列化机制向[共享session](http://yin-bp.iteye.com/blog/2064662)中存储和获取各种复杂对象的能力。