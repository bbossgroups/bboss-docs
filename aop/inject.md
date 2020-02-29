### bboss aop/ioc依赖注入功能介绍

[bboss aop](https://github.com/bbossgroups/bbossgroups-3.5/tree/master/bbossaop) 3.5及后续版本中改进的[ioc依赖注入](http://yin-bp.iteye.com/blog/1153798)功能介绍。

bboss依赖注入支持复杂关系的依赖注入：组件直接自引用(a->a),组件间接自引用(a->b->a)，组件间循环依赖引用(a->b->c->d->b)

举一个简单的自引用的列子：

<property name="test" class="org.frameworkset.spi.remote.RPCTest" f:selfvar="attr:test"

/>

**一、新的ioc功能特点**

改进后的ioc依赖注入机制支持完整的循环依赖注入功能，并且支持任何层级的对象及对象属性引用，为了对照refid和直接配置的组件的区别，我们先看一个普通组件的配置方法：

Xml代码

```xml
<properties>  
    <property name="rpc.test" class="org.frameworkset.spi.remote.RPCTest"/>  
</properties>  
```

refid属性值的格式实例如下：

<property name="test2" refid="attr:test1->test2" 

/>

在介绍refid属性的各种引用格式及含义之前，我们先看一个完整的实例：

Java代码

```java
<?xml version="1.0" encoding="gbk"?>  
<properties>  
    <property name="test1" class="org.frameworkset.soa.xblink.Test1">  
        <property name="test2" class="org.frameworkset.soa.xblink.Test2">  
<!--内部组件test2的test1属性引用外层组件test1-->  
            <property name="test1" refid="attr:test1" />  
        </property>  
   
        <property name="test3" class="org.frameworkset.soa.xblink.Test3">  
<!--内部组件test3的test2属性引用外部组件test1的属性test2-->  
            <property name="test2" refid="attr:test1->test2" />  
        </property>  
<!--test4属性引用外部组件test1，这是一个对象自我引用配置方式-->  
        <property name="test4" refid="attr:test1"/>  
    </property>  
</properties>  
```

refid属性的所有格式及含义说明如下：

refid格式                                                                                       含义

1.attr:serviceid                             根据服务标识引用容器服务组件

2.attr:serviceid[0]                          根据服务标识及数组下标引用容器服务组件对应的下标为0对应的元素（容器服务组件类型为list，set，array三种类型）

3.attr:serviceid[key]                        根据服务标识及map key引用容器服务组件对应的索引为key对应的元素（容器服务组件类型为map类型）

4.attr:serviceid{0}                          根据服务标识及构造函数参数位置下标引用容器服务组件对应的下标为0对应的构造函数参数（容器服务组件为构造函数注入）

5.attr:serviceid->innerattributename         根据服务标识及服务组件的属性名称引用容器服务组件属性值

6.attr:serviceid->innerattributename[0]      根据服务标识及服务属性名称以及属性数组下标引用容器服务组件属性中对应的下标为0对应的元素（容器服务组件类型为list，set，array三种类型）

7.attr:serviceid->innerattributename[key]    根据服务标识及服务属性名称以及map key引用容器服务组件属性对应的索引为key对应的元素（容器服务组件类型为map类型）

8.attr:serviceid->innerattributename{0}      根据服务标识及服务属性名称对应属性的造函数参数位置下标引用容器服务组件对应的下标为0对应的构造函数参数（容器服务组件为构造函数注入）

其中属性的引用是不限制层级的。

下面举例来说明上述每种情况的使用方法。

**二、引用使用详解**

通过在xml配置文件中配置一个的复杂对象Test1组件来说明IOC的循环依赖功能以及局部属性引用功能。涉及的三个对象Test1、Test2、Test3定义如下：

Test1对象：

Java代码

```java
package org.frameworkset.soa.xblink;  
  
import java.util.List;  
import java.util.Map;  
  
public class Test1 {  
    Test2 test2;  
    Test3 test3;  
    Test1 test4;  
    Test1 test5;  
    Test2 test6;  
    Test2 test7;  
    Test2 test8;  
    Test1 test9;  
    Test1 test10;  
    Test1 test11;  
    Test2 test12;  
    Map testmap;  
    List testlist;  
    Test1[] testarray;  
}  
```

Test2对象：

Java代码

```java
package org.frameworkset.soa.xblink;  
  
public class Test2 {  
    Test1 test1;  
    Test3 test3;  
}
```

Test3对象：

Java代码 

```java
package org.frameworkset.soa.xblink;  
  
public class Test3  implements java.io.Serializable{  
    Test2 test2;  
}  
```

看以看出几个对象之间的引用是错综复杂的，基于此我们再来看看test1组件的配置：包括了Test1所有属性的配置，每个属性的配置都包含了详细的含义说明，这些属性的配置基本涵盖了bboss ioc依赖注入的所有功能特性，本文中暂不介绍针对构造函数参数的引用功能：

案例一

Xml代码

```xml
<?xml version="1.0" encoding="gbk"?>  
<properties>  
    <!-- 通过test1对应的复杂对象Test1来说明IOC的循环依赖功能以及局部属性引用功能 -->  
    <property name="test1" class="org.frameworkset.soa.xblink.Test1">  
        <property name="test2" class="org.frameworkset.soa.xblink.Test2">  
            <!--内部组件test2的test1属性引用外层组件test1-->         
            <property name="test1" refid="attr:test1" />  
            <!--内部组件test2的test3属性引用外层组件test1的test3属性-->  
            <property name="test3" refid="attr:test1->test3" />  
        </property>  
        <!--内部组件test3的test2属性引用外层组件test1的test2属性-->  
        <property name="test3" class="org.frameworkset.soa.xblink.Test3"  
            f:test2="attr:test1->test2"/>  
        <!--test4属性直接引用外层组件test1-->  
        <property name="test4" refid="attr:test1"/>  
        <property name="testmap" >  
            <map componentType="bean">  
                <!--Map类型属性testmap的test4索引对应的值是对外层组件test1引用-->  
                <property name="test4" refid="attr:test1"/>  
            </map>  
        </property>  
        <property name="testlist" >  
            <list componentType="bean">  
             <!--List类型属性testlist的第一个值是对外层组件test1引用-->  
                <property refid="attr:test1"/>  
            </list>  
        </property>  
        <property name="testarray" >  
            <array componentType="org.frameworkset.soa.xblink.Test1">  
                <!--数组类型属性testlist的第一个值是对外层组件test1引用-->  
                <property refid="attr:test1"/>  
            </array>  
        </property>  
        <!--test5属性直接引用外层test1组件的属性test4-->  
        <property name="test5" refid="attr:test1->test4"/>  
        <!--test6属性直接引用外层test1组件的数组属性testarray的第一个元素对象的test2属性-->  
        <property name="test6" refid="attr:test1->testarray[0]->test2"/>  
        <!--test7属性直接引用外层test1组件的List属性testlist的第一个元素对象的test2属性-->  
        <property name="test7" refid="attr:test1->testlist[0]->test2"/>  
        <!--test8属性直接引用外层test1组件的Map属性testmap的key为test4对应的值对象的test2属性-->  
        <property name="test8" refid="attr:test1->testmap[test4]->test2"/>  
        <!--test9属性直接引用外层test1组件的数组属性testarray的第一个元素对象-->  
        <property name="test9" refid="attr:test1->testarray[0]"/>  
        <!--test10属性直接引用外层test1组件的List属性testlist的第一个元素对象-->  
        <property name="test10" refid="attr:test1->testlist[0]"/>  
        <!--test11属性直接引用外层test1组件的Map属性testmap的key为test4对应的值对象-->  
        <property name="test11" refid="attr:test1->testmap[test4]"/>  
        <!--test12属性直接引用外层test1组件的属性test3对象中的test2属性-->  
        <property name="test12" refid="attr:test1->test3->test2"/>  
    </property>  
</properties>  
```

下面看看如何加载上述[配置](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-soa/test/org/frameworkset/soa/xblink/testcontainref.xml)并获取test1对象，然后看看xml-bean相互转换的[过程](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-soa/test/org/frameworkset/soa/xblink/TestSerializable.java)：

Java代码

```java
//加载配置文件，构建一个组件容器对象  
        BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/soa/xblink/testcontainref.xml");  
        //获取组件test1  
        Test1 test1 = context.getTBeanObject("test1",  Test1.class);  
        //重新将组件序列化为xml串  
        String ss = ObjectSerializable.toXML(test1);  
        //将xml串ss转换为对象test_  
        Test1 test1_ =  (Test1)ObjectSerializable.toBean(ss,Test1.class);  
```

再看看一个包含二维元素引用的示例配置：

Xml代码

```xml
<?xml version="1.0" encoding="gbk"?>  
<properties>  
    <!-- 通过test1对应的复杂对象Test1来说明IOC的循环依赖功能以及局部属性引用功能 -->  
    <property name="test1" class="org.frameworkset.soa.xblink.Test1">  
        <property name="test2" class="org.frameworkset.soa.xblink.Test2">  
            <!--内部组件test2的test1属性引用外层组件test1-->         
            <property name="test1" refid="attr:test1" />  
            <!--内部组件test2的test3属性引用外层组件test1的test3属性-->  
            <property name="test3" refid="attr:test1->test3" />  
        </property>  
        <!--内部组件test3的test2属性引用外层组件test1的test2属性-->  
        <property name="test3" class="org.frameworkset.soa.xblink.Test3"  
            f:test2="attr:test1->test2"/>  
        <!--test4属性直接引用外层组件test1-->  
        <property name="test4" refid="attr:test1"/>  
        <property name="testmap" >  
            <map componentType="bean">  
                <!--Map类型属性testmap的test4索引对应的值是对外层组件test1引用-->  
                <property name="test4" refid="attr:test1"/>  
            </map>  
        </property>  
        <property name="testlist" >  
            <list componentType="bean">  
             <!--List类型属性testlist的第一个值是对外层组件test1引用-->  
                <property refid="attr:test1"/>  
             <!--List类型属性testlist的第二个值是对外层组件test1的数组属性testarraybasic的引用-->  
                <property refid="attr:test1->testarraybasic"/>  
            </list>  
        </property>  
        <property name="testarray" >  
            <array componentType="org.frameworkset.soa.xblink.Test1">  
                <!--数组类型属性testlist的第一个值是对外层组件test1引用-->  
                <property refid="attr:test1"/>  
                <!--数组类型属性testlist的第二个值是对外层组件test1的数组属性testarraybasic的第二个值得引用-->  
                <property refid="attr:test1->testarraybasic[1]"/>  
            </array>  
        </property>  
        <!--数组类型属性testarraybasic用来验证属性多维引用功能-->  
        <property name="testarraybasic" >  
            <array componentType="org.frameworkset.soa.xblink.Test1">  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
            </array>  
        </property>  
        <!--属性innerelement用来验证属性二维引用功能-->  
        <property name="innerelement" refid="attr:test1->testlist[1][0]"/>  
        <!--test5属性直接引用外层test1组件的属性test4-->  
        <property name="test5" refid="attr:test1->test4"/>  
        <!--test6属性直接引用外层test1组件的数组属性testarray的第一个元素对象的test2属性-->  
        <property name="test6" refid="attr:test1->testarray[0]->test2"/>  
        <!--test7属性直接引用外层test1组件的List属性testlist的第一个元素对象的test2属性-->  
        <property name="test7" refid="attr:test1->testlist[0]->test2"/>  
        <!--test8属性直接引用外层test1组件的Map属性testmap的key为test4对应的值对象的test2属性-->  
        <property name="test8" refid="attr:test1->testmap[test4]->test2"/>  
        <!--test9属性直接引用外层test1组件的数组属性testarray的第一个元素对象-->  
        <property name="test9" refid="attr:test1->testarray[0]"/>  
        <!--test10属性直接引用外层test1组件的List属性testlist的第一个元素对象-->  
        <property name="test10" refid="attr:test1->testlist[0]"/>  
        <!--test11属性直接引用外层test1组件的Map属性testmap的key为test4对应的值对象-->  
        <property name="test11" refid="attr:test1->testmap[test4]"/>  
        <!--test12属性直接引用外层test1组件的属性test3对象中的test2属性-->  
        <property name="test12" refid="attr:test1->test3->test2"/>  
    </property>  
</properties>  
```

test1中的属性innerelement引用了list属性testlist的第二个元素对应的数组中的第一个数据：

<property name="innerelement" refid="attr:**test1->testlist[1][0]**"

/>

testlist属性的定义如下：

Xml代码

```xml
<property name="testlist" >  
            <list componentType="bean">  
             <!--List类型属性testlist的第一个值是对外层组件test1引用-->  
                <property refid="attr:test1"/>  
             <!--List类型属性testlist的第二个值是对外层组件test1的数组属性testarraybasic的引用-->  
                <property refid="attr:test1->testarraybasic"/>  
            </list>  
        </property>  
```

其中的第二个元素，又是对test1中的一个数组属性testarraybasic的引用：

<property refid="attr:**test1->testarraybasic**"

/>

testarraybasic属性的定义如下：

Xml代码 

```xml
<property name="testarraybasic" >  
            <array componentType="org.frameworkset.soa.xblink.Test1">  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
                <property class="org.frameworkset.soa.xblink.Test1"/>  
            </array>  
        </property>  
```

