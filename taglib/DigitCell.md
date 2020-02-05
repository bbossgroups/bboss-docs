### bboss标签库cell标签格式化数字实例

带double类型的po对象定义：
Java代码

```java
package test;  
  
public class TestBean {  
    private String id;  
    private String name;  
    private TestBean inner;  
    private long sellMonery = 1000l;  
    private double selldoubleMonery = 100000.00d;  
      
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
    public long getSellMonery() {  
        return sellMonery;  
    }  
    public void setSellMonery(long sellMonery) {  
        this.sellMonery = sellMonery;  
    }  
    public double getSelldoubleMonery() {  
        return selldoubleMonery;  
    }  
    public void setSelldoubleMonery(double selldoubleMonery) {  
        this.selldoubleMonery = selldoubleMonery;  
    }  
  
}  
```

初始化一个TestBean对象并设置到request中：
Java代码

```java
//构建TestBean对象  
    TestBean bean = new TestBean();  
    bean.setId("uuid");  
    bean.setName("多多");  
    bean.setSellMonery(10000000);  
    bean.setSelldoubleMonery(10000000.334d);//只有double类型的数字小数位，###,###.##才能正确输出后面的小数  
    TestBean ibean = new TestBean();  
    ibean.setId("uuidinner");  
    ibean.setName("多多uuidinner");  
    ibean.setSellMonery(10000000);  
    bean.setInner(ibean);  
    request.setAttribute("testbean",bean);  

```

通过cell标签输出各个属性，并且利用numformat属性格式化SellMonery和selldoubleMonery两个属性：
Html代码

```html
<pg:beaninfo actual="${testbean }">  
        <tr class="cms_data_tr">  
                <td>  
                    testbean:  
                </td>   
                <td>  
                    selldoubleMonery:<pg:cell colName="selldoubleMonery" numerformat="###,###.###"/>  
                </td>  
                <td>  
                    sellMonery:<pg:cell colName="sellMonery" numerformat="###,##"/>  
                </td>  
                <td>  
                    id:<pg:cell colName="id" />  
                </td>   
                <td>  
                    name:<pg:cell colName="name" />  
                </td>   
                <td>  
                    innerid:<pg:cell colName="inner" property="id" />  
                </td>   
                <td>  
                    innername:<pg:cell colName="inner"  property="name" />  
                </td>   
            </tr>  
            </pg:beaninfo>  
```

