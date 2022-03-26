### bboss ioc配置文件中使用外部属性文件介绍

与spring ioc一样，在bboss ioc中也可以非常方便地引用外部属性文件(5.0.1及后续版本)，本文介绍使用方法。
在工程中引入bboss ioc：

maven坐标：
Xml代码 

```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-core</artifactId>  
    <version>5.8.9</version>  
</dependency>  
```

  **gradle坐标：**

compile group: 'com.bbossgroups', name: 'bboss-core', version: '5.8.9'

运行测试用例junit gradle坐标：
testCompile group: 'junit', name: 'junit', version: '4.+'

下载本文演示gradle工程：[下载](http://dl.iteye.com/topics/download/091574dc-54aa-3979-aadb-254bb86bff37)

参考文档将gradle工程导入eclipse：[bboss gradle工程导入eclipse介绍](http://yin-bp.iteye.com/blog/2313145)

#### **定义和导入外部属性文件**

属性文件必须包含在classpath环境中

例如：  

![](../images/bboss/c0db3fd8-b86d-3e1d-96b6-f69e2288ae63.png)

可以定义多个属性文件
文件定义好后需要在ioc配置文件的最开始通过config元素导入，如果有多个配置文件，可以在ioc根文件中导入属性文件（可以同时导入多个）：

  config file="org/frameworkset/spi/variable/ioc-var.properties"/
config file="org/frameworkset/spi/variable/ioc-var1.properties"/

config file="file:F:/workspace/bboss/bboss-core/test/org/frameworkset/spi/variable/ioc-var.properties"/

通过file:前缀指定物理路径，默认是classpath目录下的路径
属性文件内容：

Java代码

```java
varValue1=hello varValue1!  
varValue2=hello varValue2!  
```

#### **使用外部属性文件**

导入后就可以在注入的属性、扩展属性中引用属性文件中定义的变量：

引用变量语法：${xxxxx}

指定默认值语法：${varValue2:99}

在依赖注入的属性值中引用外部属性完整的示例

Xml代码

```java
<properties>  
    <config file="org/frameworkset/spi/variable/ioc-var.properties"/>  
    <config file="org/frameworkset/spi/variable/ioc-var1.properties"/>  
    <property name="test.beans"  
        f:varValue="aaa${varValue}aaa"   
        f:intValue="2"  
        long="1" int="1" boolean="true" string="${varValue1}string" object="object"  
        class="org.frameworkset.spi.variable.VariableBean">  
        <construction>  
            <property ><![CDATA[${varValue1}ccc]]></property>  
            <property value="ddd${varValue2}"/>  
        </construction>  
        <property name="varValue1" ><![CDATA[${varValue1}ccc]]></property>  
        <property name="varValue2" value="ddd${varValue2:99}"/>  
    </property>  
      
       
</properties>  

```

获取使用了外部属性文件的组件实例：
Java代码 

```java
@Test  
    public void test()  
    {  
        BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/variable/ioc-var.xml");//定义一个ioc容器  
        VariableBean variableBean = context.getTBeanObject("test.beans", VariableBean.class);//获取组件实例  
        System.out.println(variableBean.getExteral("string"));//获取组件中配置的扩展属性string  
    }  
```

VariableBean类源码：
Java代码 

```java
package org.frameworkset.spi.variable;  
  
import org.frameworkset.spi.BeanInfoAware;  
  
public class VariableBean extends BeanInfoAware{  
    private String varValue;  
    private String varValue1;  
    private String varValue2;  
    private int intValue;  
    public VariableBean(String varValue1,String varValue2)  
    {  
        System.out.println("varValue1:"+varValue1);  
        System.out.println("varValue2:"+varValue2);  
    }  
      
    public String getExteral(String attr)  
    {  
        return super.beaninfo.getStringExtendAttribute(attr);  
    }  
  
}  
```

#### **通过ioc容器直接获取外部属性api方法实例**

Java代码 

```java
BaseApplicationContext context = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/variable/parent-var.xml");  
        System.out.println(context.getExternalProperty("varValue"));  
        System.out.println(context.getExternalProperty("varValue1"));  
        System.out.println(context.getExternalProperty("varValue2"));  
```

#### **直接加载配置文件并获取属性值方法**

Java代码 

```java
PropertiesContainer propertiesContainer = new PropertiesContainer();  
        propertiesContainer.addConfigPropertiesFile("application.properties");  
        String dbName  = propertiesContainer.getProperty("db.name");  
        String dbUser  = propertiesContainer.getProperty("db.user");  
        String dbPassword  = propertiesContainer.getProperty("db.password");  
        String dbDriver  = propertiesContainer.getProperty("db.driver");  
        String dbUrl  = propertiesContainer.getProperty("db.url");  
  
  
        String validateSQL  = propertiesContainer.getProperty("db.validateSQL");  
  
  
        String _jdbcFetchSize = propertiesContainer.getProperty("db.jdbcFetchSize");  
        Integer jdbcFetchSize = null;  
        if(_jdbcFetchSize != null && !_jdbcFetchSize.equals(""))  
            jdbcFetchSize  = Integer.parseInt(_jdbcFetchSize);  

```

####   **外部属性有效范围**

1.根容器配置文件中导入的外部属性文件中的属性值对根文件中导入（managerimport）子文件可见
2.子文件中导入的外部属性文件中的属性只对本身及其下级子文件可见，以此类推
3.mvc容器对应的根文件是bboss-mvc.xml文件，在其中引入的外部属性配置文件对所有其他mvc配置文件可见，其他mvc配置文件导入的外部属性文件只对本身及其下级子文件可见
