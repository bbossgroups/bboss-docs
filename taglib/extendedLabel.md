### 扩展bboss标签库编写自己的标签

 实际使用bboss标签库的过程中，有时需要对扩展定制bboss标签，比如bboss的内容管理模板标签库、字典标签库都是基于bboss标签扩展开发出来的。本文介绍基于cell标签的定制开发方法。

cell标签的作用就是用来获取并输出bean的属性值、map中对应key的value值、数据库查询字段值。因此我们可以直接从cell标签继承获取bean的属性值、map中对应key的value值、数据库查询字段值的功能，然后拿到这个值在自己定制的标签中做相应的处理。  

下面介绍定制开发过程：

实现自己的标签处理类

Java代码

```java
package test;  
  
import java.io.IOException;  
  
import javax.servlet.jsp.JspException;  
  
import com.frameworkset.common.tag.pager.tags.CellTag;  
import com.frameworkset.util.StringUtil;  
  
public class TestCellTag extends CellTag{  
    private String customAttribute;//自定义自己的标签属性  
    public int doStartTag() throws JspException {  
        init();//初始化cell标签，必须实现  
        Object actualValue =  super.getObjectValue();//获取到当前cell标签运算出来的值  
          
        //接下来就可以做自己的事情了,我们只是输出自定义属性的值和cell对应的值到jsp页面上。  
        if(StringUtil.isNotEmpty(customAttribute))  
        {  
            try {  
                out.print("Custom Attribute value is "+this.customAttribute+",cell value is "+actualValue);  
            } catch (IOException e) {  
                throw new JspException(e);  
            }  
        }  
        return SKIP_BODY;//返回控制标签事件生命周期的常量  
    }  
    /** 
     * 必须定义标签属性的get/set方法 
     */  
    public String getCustomAttribute() {  
        return customAttribute;  
    }  
    public void setCustomAttribute(String customAttribute) {  
        this.customAttribute = customAttribute;  
    }  
    /** 
     * 必须在doFinally方法中释放属性的值 
     */  
    @Override  
    public void doFinally() {  
        this.customAttribute = null;  
        super.doFinally();  
    }  
      
}  
```

定义标签描述文件-test-taglib.tld

文件存放路径：/WEB-INF/test-taglib.tld
定义一个名称为testcell的标签，testcell标签除了customAttribute属性外，其他属性都是从cell标签继承。
标签的实现类：test.TestCellTag

以下是tld文件的完整内容：

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE taglib PUBLIC "-//Sun Microsystems, Inc.//DTD JSP Tag Library 1.1//EN"  
                        "http://java.sun.com/j2ee/dtds/web-jsptaglibrary_1_2.dtd">  
<taglib>  
    <tlibversion>1.0</tlibversion>  
    <jspversion>1.1</jspversion>  
    <shortname>pg</shortname>  
    <uri>test-taglib</uri>  
    <info>test taglib.</info>  
    <tag>  
        <name>testcell</name>  
        <tagclass>test.TestCellTag</tagclass>  
        <bodycontent>jsp</bodycontent>    
        <!-- 定义自己的属性 开始，这些属性必须在自定义标签类中定义，并且有get和set方法-->    
        <attribute>  
            <name>customAttribute</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <!-- 定义自己的属性 结束-->  
          
        <!--  
        ***************************************************************  
        *   从cell标签继承属性开始                                         *  
        ***************************************************************   
        -->  
        <attribute>  
            <name>colName</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>property</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>content</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>colid</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <!-- 
            查找祖先(list)索引 
        -->  
        <attribute>  
            <name>index</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
           
        <!--  
            指定单元格输出的默认值  
            当前的值为null时，输出指定的默认值，  
            如果不指定默认值将直接输出null。  
        -->  
        <attribute>  
            <name>defaultValue</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
           
        <!--  
            求值表达式:如果设置表达式，将通过表达式求解cell标签的输出 表达式支持的基本操作有：冥，乘法，除法，求余，加/减，字符串的加操作  
            表达式支持的聚合操作有：求和，求平均值，计数 操作数：可以为变量，整数，小数，字符串  
            其中变量是指列表/分页/页面详细信息中的对象属性名称，为如下形式： {variable name} 表达式中用"{"和"}"来标识变量  
            例如： 2 * 2 2 + 2 * 3 2 - 2 / 3 2 - 3 % 2 2* {var1} / {var2}  
            sum({var1}) avg({var1}) count({var1}) 上述操作可以任意组合  
        -->  
        <attribute>  
            <name>expression</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
           
        <attribute>  
            <name>actual</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <!-- trim:true false default false -->  
       
        <attribute>  
            <name>requestKey</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>sessionKey</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>pageContextKey</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <attribute>  
            <name>parameter</name>  
            <rtexprvalue>true</rtexprvalue>  
        </attribute>  
        <!--  
        ***************************************************************  
        *   从cell标签继承属性结束                                             *  
        ***************************************************************   
        -->  
    </tag>  
</taglib>   
```

在jsp头部导入自定义的标签tld文件：

Html代码 

```html
<%@ taglib uri="/WEB-INF/test-taglib.tld" prefix="test"%>  
```

万事具备，可以在list，beaninfo，map这些标签中嵌套使用这个testcell标签了：

Xml代码

```xml
<pg:beaninfo requestKey="userinfo">  
   <test:testcell customAttribute="anyvalue" colName="userCNName"/>  
</pg:beaninfo> 
```

指定的customAttribute属性的值为anyvalue，指定了userinfo对象的userCNName属性（用户的中文名称，值为张三），上述代码段运行的结果为：

Java代码

```java
Custom Attribute value is anyvalue,cell value is 张三  
```

到此为止，通过扩展cell，终于得到了我们想要的testcell标签了。


  