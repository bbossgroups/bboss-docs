### bboss 序列化功能详解

bboss 序列化功能详解，bboss序列化组件是bbossgroups框架体系中的又一个非常给力的功能构件，可以非常方便地实现对象到xml的相互转换功能，本文详细介绍bboss序列化功能的特点及使用方法。

##### **1.Bboss 序列化功能组件及依赖的jar包**

组件

[org.frameworkset.soa.ObjectSerializable](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/src/org/frameworkset/soa/ObjectSerializable.java)

###### 1.1 主要组件和注解

ObjectSerializable组件提供了序列化和反序列化的主要api：

序列化api

Java代码

```java
//将对象obj序列化成xml串并返回该串  
public final static String toXML(Object obj)  
//将对象obj序列化成xml串,并将该串写入到Writer对象out中  
public final static void toXML(Object obj, Writer out)  
```

反序列化api

Java代码

```java
//将beanxml参数对应的xml串转换为beantype类型的对象并返回该对象，采用泛型方式  
public static <T> T toBean(String beanxml, Class<T> beantype)  
//将instream参数对应的xml字符流转换为beantype类型的对象并返回该对象，采用泛型方式  
public static <T> T toBean(InputStream instream, Class<T> beantype)  
```

注解

[org.frameworkset.soa.annotation.ExcludeField](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-util/src/org/frameworkset/soa/annotation/ExcludeField.java)

ExcludeField注解主要的作用就是用来过滤对象中不需要序列化的属性，目前只对对象属性起作用，后续可以加到get方法。ExcludeField注解的作用类似于java保留字transient的作用，bboss序列化组件同样也支持transient关键字，只要属性前面加了transient关键字，序列化时也会被忽略。ExcludeField用法如下：

Java代码 

```java
@ExcludeField  
private String excludeField  
```

###### **1.2 依赖的jar包，可点击链接下载,如果地址无法访问，也可以点击每个jar文件上的连接从github下载**

[bboss-core.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bigdata/lib/bboss-core.jar?raw=true)

[cglib-nodep-2.2.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/lib/cglib-nodep-2.2.jar?raw=true)

[frameworkset-util.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/lib/frameworkset-util.jar?raw=true)

[log4j-1.2.16.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/lib/log4j-1.2.16.jar?raw=true)

[bboss-soa.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/lib/bboss-soa.jar?raw=true)

[jakarta-oro-2.0.8.jar](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-core/lib/jakarta-oro-2.0.8.jar?raw=true)

##### **2.Bboss 序列化功能特点**

(1).支持高效的xml-bean相互转换（序列化和反序列化）

(2).支持各种数据类型的序列化和反序列化，支持文件的序列化和反序列化

(3).支持对象间各种关系的序列化和反序列化（对象恢复后能够恢复对象间的引用关系以及循环递归引用关系，例如树结构的对象关系，单向父子对象关系，双向父子对象关系）

(4).序列化时可以过滤以下类型属性：  

transient

static

final

@Exclude注解过的属性

##### **3.指定序列化过程中可以忽略的异常信息，以换行符分割**

在配置文件/resources/org/frameworkset/soa/serialconf.xml的ignoreExceptions节点中进行设置：

Xml代码

```xml
<property name="ignoreExceptions">  
        <![CDATA[ 
        org.frameworkset.soa.IgnoreException1 
        org.frameworkset.soa.IgnoreException2 
        ]]>  
    </property>  
```

默认忽略org.hibernate.LazyInitializationException异常

##### **4.Bboss 序列化功能使用**

本小节以一个简单的实例来说明如何通过bboss来实现组件序列化功能.  

###### **4.1 首先看简单对象序列化和反序列化操作**

Java代码

```java
//构建和初始化要序列化的简单对象实例  
TransientFieldBean transientFieldBean = new TransientFieldBean("onlyField");  
        transientFieldBean.setExcludeField("exccc");  
        transientFieldBean.setStaticFiled("staticFiled");  
        transientFieldBean.setTransientField("transientField");  
//对象序列化  
        String xml = ObjectSerializable.toXML(transientFieldBean);  
//反序列化  
        TransientFieldBean transientFieldBean_new = ObjectSerializable.toBean(xml, TransientFieldBean.class);  
```

###### **4.2 再看一个复杂对象数据结构的序列化和反序列化操作**

Java代码

```java
//构建和初始化要序列化的复杂对象实例，对象test1引用对象test2和test3，test2引  
//用test1，对象test3引用对象test2，这样就构造了一个具有相互引用的关系网，  
//bboss序列化组件可以非常方便地对这种结构的对象进行序列化和反序列化，而且能够  
//很好地保持这种复杂的引用关系  
Test1 test1 = new Test1();  
        Test2 test2 = new Test2();  
        Test3 test3 = new Test3();  
        test2.setTest1(test1);  
        test1.setTest2(test2);  
        test1.setTest3(test3);  
        test3.setTest2(test2);  
//序列化test1对象  
        String ss = ObjectSerializable.toXML(test1);  
//反序列化  
        Test1 test1_ =  (Test1)ObjectSerializable.toBean(ss,Test1.class);  
```

###### **4.3 再看一个文件对象的序列化和反序列化操作**

Java代码

```java
//构造一个带文件属性的对象joe  
File fileData = new File("D:\\workspace\\bbossgroups-3.2\\bboss-soa\\test\\org\\frameworkset\\soa\\testxstream.xml");  
FilePerson joe = new FilePerson();  
        joe.setFileData(fileData);  
//序列化对象joe  
String xml = ObjectSerializable.toXML(joe);  
//反序列化  
FilePerson person = ObjectSerializable.toBean( xml, FilePerson.class);  
```

更详细的实例请参考[测试用例](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-soa/test/org/frameworkset/soa/xblink/TestSerializable.java)。

##### **5.Bboss 序列化功能应用**

bboss序列化功能目前已经被广泛应用于基于http/netty/mina/webservice/jms 等通讯协议的rpc框架中，性能表现良好。  

参考文档：

[bbossgroups 对象xml序列化/反序列化性能测试](http://yin-bp.iteye.com/blog/1189141)

[bboss 序列化机制重大改进-支持复杂对象及对象之间关系序列化和恢复功能](http://yin-bp.iteye.com/blog/1339082)

为了验证hessian和bboss序列化功能的差异性特意做了个对比测试。

分别用bboss和hessian序列化和反序列化带有47K数据的对象实例，测试结果如下：

bboss:序列化生成数据大小48274byte,耗时:47毫秒

hessian:序列化生成数据大小47801byte,耗时:62毫秒

bboss 反序列化耗时:125毫秒

hessian 反序列化耗时:32毫秒  

从测试结果来看，bboss序列化速度比hessian稍快，反序化比hessian慢4倍，序列化产生的数据大小比hessian略大一些。

hessian的版本是4.0.7，bboss 的版本是4.0.7。

测试代码附下：

Java代码

```java
@Test  
    public void testHessianSerializable() throws Exception  
    {  
        Test1 test1 = new Test1();  
        Test2 test2 = new Test2();  
        Test3 test3 = new Test3();  
        test2.setTest1(test1);  
        test1.setTest2(test2);  
        test1.setTest3(test3);  
        test3.setTest2(test2);  
        try  
        {  
            String bigcontent = FileUtil.getFileContent(new File("D:\\workspace\\bbossgroups-3.5\\bboss-soa\\test\\org\\frameworkset\\soa\\testxstream.xml"), "GBK");//testxstream.xml是一个47K大小的xml文件  
            test1.setXmlvalue(bigcontent);  
            long s = System.currentTimeMillis();  
            String xml = ObjectSerializable.toXML(test1);//bboss 序列化  
            long e = System.currentTimeMillis();  
            System.out.println("bboss:"+xml.getBytes().length + ",times:" + (e - s));  
            s = System.currentTimeMillis();  
            Test1 test1_ =  (Test1)ObjectSerializable.toBean(xml,Test1.class);//bboss 反序列化  
            e = System.currentTimeMillis();  
            System.out.println("bboss de times:" + (e - s));  
            s = System.currentTimeMillis();//hessian序列化  
            ByteArrayOutputStream os = new ByteArrayOutputStream();     
            HessianOutput ho = new HessianOutput(os);     
            ho.writeObject(test1);     
            byte[] cs = os.toByteArray();     
            e = System.currentTimeMillis();  
  
            System.out.println("hessian:"+cs.length+ ",times:" + (e - s));  
            s = System.currentTimeMillis();//hessian反序列化  
            ByteArrayInputStream is = new ByteArrayInputStream(cs);     
             HessianInput hi = new HessianInput(is);     
             test1_ =  (Test1) hi.readObject();     
             e = System.currentTimeMillis();  
                System.out.println("hessian de times:" + (e - s));  
              
            //测试用例结束  
              
              
        }  
        catch (Exception e)  
        {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
```

