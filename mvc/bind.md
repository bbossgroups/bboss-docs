### bboss mvc 控制器方法参数绑定技巧-List类型参数绑定介绍

本文介绍bboss mvc 控制器方法参数绑定技巧-List类型参数绑定的使用方法，切入正题。

**1、概述**

List类型参数绑定有三种方式：

方式一 List方式，以@RequestParam(name="ids")注解标注的参数ids的值（可以是单个值，也可以是多个值）转换为List对象，然后将这个List<基础类型>作为控制器方法参数。这种方式适用于控制器参数和bean中的属性

方式二 List**<Bean**>方式，这种方式用来将多条记录转换为List<Bean>对象集合，其中的bean对应一条记录，然后将这个List**<Bean**>对象作为控制器方法参数。

方式三 List 方式无需任何注解，这种方式直接将List参数名称或者属性名称对于的参数数组转换为List对象，元素类型为字符串类型，这种方式适用于控制器参数和bean中的属性

方式四 List<基础数据类型> 方式无需任何注解，这种方式直接将List参数名称或者属性名称对于的参数数组转换为List对象，元素类型为泛型对应的类型，这种方式适用于控制器参数和bean中的属性

除了介绍这两种List参数绑定方式外，我们还会介绍如何在jsp中结合list标签/cell标签来展示list集合的数据信息。

下面直接介绍这些功能  

**2、功能详解**

**2.1、方式一 List方式**

首先看表单的写法，两个参数name和sex：

Html代码

```html
<form action="sayHelloStringList.page" method="post">  
            <table cellspacing="0" >  
                <tbody>  
                    <tr><td>  
                            请输入您的名字1：  
                        <input name="name" type="text">  
                        </td>                     <td>  
                            请输入您的性别1：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>  
  
<tr><td>  
                            请输入您的名字2：  
                        <input name="name" type="text">  
                        </td>                     <td>  
                            请输入您的性别2：  
                        <input name="sex" type="text">  
                        </td>  
                    </tr>             </tbody>  
            </table>          </form>  
```

控制方法的写法，names和sexs即为request中的分别为参数name和sex对应的值（多个值）的转换而形成的list对象：

Java代码

```java
public String sayHelloStringList(@RequestParam(name="name") List names,@RequestParam(name="sex") List sexs,  
            ModelMap model)  
    {  
//我们直接把两个List传递到jsp页面上，用list标签进行展示  
        model.addAttribute("names", names);  
                  model.addAttribute("sexs", sexs);  
  
        return "path:sayHello";  
    }  
```

jsp中用list标签进行展示names和sexs中的值方法：

Html代码

```html
<table>  
        <h3>names字符串信息迭代功能</h3>  
        <pg:list requestKey="names">          <tr >        
                <td>  
                    name:<pg:cell/>  
                </td>             </tr>  
        </pg:list>    </table>  
<table>  
        <h3>sexs字符串信息迭代功能</h3>  
        <pg:list requestKey="sexs">           <tr >        
                <td>  
                    sex:<pg:cell/>  
                </td>             </tr>  
        </pg:list>    </table>  
```

**2.2、方式二 List<**Bean>**方式**

首先看表单的写法，我们在表单里面放置多个name和sex参数，以便模拟形成多个记录的Bean对象，我们的Bean对象ExampleBean的结构也非常简单，就包含name和sex两个属性：

表单代码

Html代码

```html
<form action="sayHelloBeanList.page" method="post">  
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
public String sayHelloBeanList(List<ExampleBean> listBeans, ModelMap model)  
    {  
  
        model.addAttribute("listBeans", listBeans);  
        return "path:sayHello";  
    }  
```

通过List<**ExampleBean>** 中的泛型信息指定每条记录将被绑定的Bean对象的类型，这里是ExampleBean，控制方法的逻辑非常简单，直接将绑定好的参数listBeans交给jsp页面展示。

jsp中用list标签进行展示listBeans中ExampleBean信息的方法：

Html代码

```html
<table>  
        <tr>          <td>  
                                <pg:list requestKey="listBeans" >         <ul</li>            <li>name属性值：<pg:cell colName="name"/></li>        <li>sex属性值：<pg:cell colName="sex"/></li>     </ul>  
                            </pg:list>  
                        </td>                 </tr> </table>  
```

篇幅关系，其他几种方式不再描述。

补充说明，list标签和cell的定义文件必须导入到jsp的头部：

Html代码

```html
<%@ taglib uri="/WEB-INF/pager-taglib.tld" prefix="pg"%>    
```

标签库的使用请参考以下文章：

http://yin-bp.iteye.com/blog/1136924

http://yin-bp.iteye.com/blog/1137674

bboss mvc数据绑定更全面的介绍资料：

http://yin-bp.iteye.com/blog/1070614

bboss mvc 控制器方法参数绑定技巧-Map类型参数绑定介绍

http://yin-bp.iteye.com/blog/1170087

bbossgroups 开发系列文章之-最佳实践

 http://bbossgroups.group.iteye.com/group/wiki/3092-mvc-bboss-config

  





  