### bboss标签库cell标签展示嵌套对象属性方法实例

**带嵌套对象PO类定义**

Java代码

```java
package test;  
  
public class TestBean {  
    private String id;  
    private String name;  
    private TestBean inner;//嵌套对象  
    public String getId() {  
        return id;  
    }  
    public void setId(String id) {  
        this.id = id;  
    }  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
    public TestBean getInner() {  
        return inner;  
    }  
    public void setInner(TestBean inner) {  
        this.inner = inner;  
    }  
  
}  
```

**初始化TestBean及内部的inner对象：**

Java代码

```java
TestBean bean = new TestBean();  
    bean.setId("uuid2");  
    bean.setName("多多2");  
TestBean ibean = new TestBean();  
    ibean.setId("uuid2inner");  
    ibean.setName("uuid2inner");  
    bean.setInner(ibean);  
request.setAttribute("testbean",bean);  
```

标签中展示对象属性和内置对象属性：
Xml代码

```xml
<pg:beaninfo actual="${testbean }">  
        <tr >  
                <td>  
                    testbean:  
                </td>   
                <td>  
                    id:<pg:cell colName="id" />  
                </td>   
                <td>  
                    name:<pg:cell colName="name" />  
                </td>   
                  
                <td>  
<!--cell标签的colName和property两个属性结合实现嵌套对象的数据展示-->  
                    innerid:<pg:cell colName="inner" property="id" />  
                </td>   
                <td>  
                    innername:<pg:cell colName="inner"  property="name" />  
                </td>   
            </tr>  
            </pg:beaninfo>  
```

cell标签的colName和property两个属性结合实现嵌套对象的数据展示功能：

<pg:cell colName="inner" property="id" />
el表达式的等价写法:
${testbean.inner.id }

采用cell标签时，可以指定dateformat属性对日期对象进行格式化展示，可以指定numformat属性对数字进行格式化展示，可以指定为null时的默认值，可以指定htmlEncode进行html特殊标签转义等，例如：

<pg:cell colName="inner" property="birthday" dateformat="yyyy-MM-hh"/>
<pg:cell colName="inner" property="sellMoney" numerformat="##，##.##"/>
<pg:cell colName="inner" property="sex" defaultValue="男"/>
<pg:cell colName="inner" property="content" htmlEncode="true"/>

更过bboss标签库的功能，可以浏览文档：
http://yin-bp.iteye.com/category/69334

  