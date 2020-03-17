### bboss mvc 控制器方法参数绑定技巧-Map类型参数绑定介绍

  本文介绍bboss mvc 控制器方法参数绑定技巧-Map类型参数绑定的使用方法，切入正题。

**1、概述**

Map类型参数绑定有两种方式：

方式一 Map<String,String>方式，直接将Request对象中的参数转储到Map对象中

方式二 Map<String,Bean>方式，这种方式用来将多条记录转换为Bean类型值对象，然后根据@MapKey中指定记录字段的值作为Map的key值，Bean对象作为value，形成一个Map对象作为控制器方法参数。

除了介绍这两种Map参数绑定方式外，我们还会介绍如何在jsp中结合map标签/mapkey标签来展示map类型的数据。

下面直接介绍这些功能

**2、功能详解**

**2.1、方式一 Map<String,String>方式**

首先看表单的写法，两个参数name和sex：

Html代码  

```html
form action="sayHelloStringMap.page" method="post">  
            <table cellspacing="0" >  
                <tbody>  
                    <tr><td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>                     <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>             </tbody>  
            </table>          </form>  
```

控制方法的写法，Map params即为request中的所有参数的转储：

Java代码

```java
public String sayHelloStringMap(Map params,  
            ModelMap model)  
    {  
//我们直接把params传递到jsp页面上，用map标签进行展示  
        model.addAttribute("serverHelloMapBean", params);  
        return "path:sayHello";  
    }  
```

jsp中用map标签进行展示params中key和value的方法：

Html代码

```html
<table>  
        <h3>map<String,String>字符串信息迭代功能</h3>  
        <pg:map requestKey="mapstrings">          <tr class="cms_data_tr">  
                <td>  
                    mapkey:<pg:mapkey/>  
                </td>   
                <td>  
                    value:<pg:cell/>  
                </td>   
                  
            </tr>  
        </pg:map> </table>  
```

**2.2、方式二 Map<String,Bean>方式**

首先看表单的写法，我们在表单里面放置多个name和sex参数，以便模拟形成多个记录的Bean对象，我们的Bean对象ExampleBean的结构也非常简单，就包含name和sex两个属性：

表单代码

Html代码

```html
<form action="sayHelloBeanMap.page" method="post">  
            <table cellspacing="0" >  
                <tbody>                       <tr>                          <td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                        <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>  
                      
                    <tr>  
                        <td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                        <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>                 <tr>                      <td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                          
                        <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>  
                      
                    <tr><td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                        <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>  
<tr>  
                        <td>  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                        <td>  
                            请输入您的性别：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>  
                                        <tr>  
                        <td><input type="submit" name="确定" value="确定"></td>                       </tr>  
                </tbody>  
            </table>          </form>  
```

ExampleBean代码：

Java代码

```java
public class ExampleBean  
{  
    private String name = null;  
    private String sex = null;  
  
      
    public String getName()  
    {  
      
        return name;  
    }  
  
      
    public void setName(String name)  
    {  
      
        this.name = name;  
    }  
  
  
      
    public String getSex()  
    {  
      
        return sex;  
    }  
  
  
      
    public void setSex(String sex)  
    {  
      
        this.sex = sex;  
    }  
  
}  
```

控制方法的写法：

Java代码 

```java
public String sayHelloBeanMap(@MapKey("name") Map<String, ExampleBean> mapBeans, ModelMap model)  
    {  
  
        model.addAttribute("serverHelloMapBean", mapBeans);  
        return "path:sayHello";  
    }  
```

我们用注解@MapKey("name") 声明了Map参数中key以name字段的值作为key，通过Map<String, ExampleBean> 中的泛型信息指定每条记录将被绑定的Bean对象的类型，这里是ExampleBean，控制方法的逻辑非常简单，直接将绑定好的参数mapBeans交给jsp页面

jsp中用map标签进行展示mapBeans中key和ExampleBean的方法：

Html代码

```html
<table>  
        <tr>          <td>  
                                <pg:map requestKey="serverHelloMapBean" >         <ul><li> mapkey: <pg:mapkey/></li>           <li>name属性值：<pg:cell colName="name"/></li>        <li>sex属性值：<pg:cell colName="sex"/></li>     </ul>  
                            </pg:map>  
                        </td>                 </tr> </table>  
```

补充说明，map标签的定义文件必须导入到jsp的头部：

Html代码

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>   
```

标签库的使用请参考以下文章：

http://yin-bp.iteye.com/blog/1136924

http://yin-bp.iteye.com/blog/1137674

bboss mvc数据绑定更全面的介绍资料：

http://yin-bp.iteye.com/blog/1070614