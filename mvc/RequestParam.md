### bboss mvc参数绑定注解RequestParam使用说明

@RequestParam作为控制器方法参数、bean对象属性的注解，可以起到以下5个作用：

方法参数-当方法参数名称yournames和request参数名称name不一致时，则需要RequestParam注解来指定映射关系

**1.指定方法参数与request请求参数名称的映射关系**

Java代码

```java
public String sayHelloArray(  
            @RequestParam(name = "name") String[] yournames, ModelMap model)  
```

bean属性-当属性名称favovate 和request参数名称favovate_ddd不一致时，则需要RequestParam注解来指定映射关系

Java代码 

```java
@RequestParam(name="favovate_ddd")  
    private int favovate ;  
```

这个只是最简单的映射方式，bboss mvc还支持带变量的参数名称映射方式：

Java代码

```java
@RequestParam(name="favovate${id}")  
    private int favovate ;  
```

这里@RequestParam(name="favovate${id}")中的name属性的值为favovate${id}，其中的${id}部分的含义是request参数的名称由favovate加上另外一个request参数id的值组合而成，也就是是这个参数名称根据id参数的值来动态生成，这种情况适用于集合参数绑定场景（简单的参数映射也可以使用）：

表单代码，输入两个人的姓名和兴趣喜好，提交后绑定成List**<ExampleBean**> yourname参数

Java代码 

```java
<tr><td>  
                        <input name="id" type="hidden" value="0">  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                    </tr>  
                    <tr><td>喜好：  
                        <input name="favovate0" type="radio" checked="true"  value="0"> 乒乓球  
                        <input name="favovate0" type="radio" value="1"> 篮球  
                        <input name="favovate0" type="radio" value="2"> 排球  
                        </td>  
                    </tr>  
                      
                    <tr><td>  
                        <input name="id" type="hidden" value="1">  
                            请输入您的名字：  
                        <input name="name" type="text">  
                        </td>  
                    </tr>  
                    <tr><td>喜好：  
                        <input name="favovate1" type="radio" checked="true"  value="0"> 乒乓球  
                        <input name="favovate1" type="radio" value="1"> 篮球  
                        <input name="favovate1" type="radio" value="2"> 排球  
                        </td>  
                    </tr>  
```

控制器方法：

Java代码

```java
public String sayHelloBeanList(List<ExampleBean> yourname, ModelMap model)  
    {  
  
        model.addAttribute("serverHelloListBean", yourname);  
  
        return "path:sayHello";  
    }  
```

ExampleBean类的favovate属性分别对应于表单中的favovate0和favovate1，也就是说每条记录对应一组radio的名称都不一样，由favovate+记录id的值组成：

Java代码 

```java
public class ExampleBean  
{  
    private String id;  
    private String name = null;  
    private String sex = null;  
    @RequestParam(name="favovate${id}")  
    private int favovate ;  
}  
```

那么在后台就可以通过favovate${id}来指定request参数和属性favovate的映射关系

@RequestParam(name="favovate${id}")

private int favovate ;

目前变量参数名中只能指定一个变量，这个变量在参数名称中的位置可以任意有以下形式：

favovate${id}：假设请求参数id的值为1，那么最终的映射参数名称为favovate1

fav${id}ovate：假设请求参数id的值为1，那么最终的映射参数名称为fav1ovate

${id}favovate：假设请求参数id的值为1，那么最终的映射参数名称为1favovate

也可以只有变量：

${id}：假设请求参数id的值为1，那么最终的映射参数名称为1

变量参数中如果请求参数id的值为null或者""时，变量id对应的名称部分将被忽略，所以一般需要注意这一点。

在控制器方法参数上简单的使用参数变量的示例：

Java代码

```java
public String sayHelloStringVar(@RequestParam(name = "name${id}") String yourname,  
            ModelMap model)  
```

**2.指定参数的解码字符集**

@RequestParam( decodeCharset = "UTF-8")

private String name;

**3.指定日期格式**

示例如下：

@RequestParam( dateformat="yyyy-MM-dd") java.util.Date[] d12

**4.指定参数的默认值**

示例如下：

@RequestParam(defaultvalue="0") int ynum

**5.指定参数值转换器Editor**

如果请求参数和要绑定的参数类型不能直接转换时需要通过Editor来实现自定义的参数绑定和类型转换，下面是一个实例：

Java代码

```java
public String sayHelloEditors(@RequestParam(editor="org.frameworkset.mvc.ListStringArrayEditor") List<String[]> name,ModelMap model)  
```

转换器org.frameworkset.mvc.ListStringArrayEditor定义如下：

Java代码

```java
public class ListStringArrayEditor implements ArrayEditorInf<List<String[]>> {  
  
    @Override  
    public List<String[]> getValueFromObject(Object fromValue) {  
        if(fromValue == null)  
            return null;  
        if(fromValue instanceof String[])  
        {  
            String[] datas = (String[])fromValue;   
            if(datas.length<=0)  
                return null;  
            List<String[]> ret = new ArrayList<String[]>();  
              
            for(String data :datas)  
            {  
                String[] tt = data.split(",");  
                ret.add(tt);  
            }  
            return ret;  
        }  
        return null;  
          
    }  
  
    @Override  
    public List<String[]> getValueFromString(String fromValue) {  
        return null;  
    }  
}  
```

mvc框架参数绑定时，如果指定了类型为ArrayEditorInf的属性编辑器， 则要求mvc框架传入的参数为参数数组，否则只能传入单个值。这个转换器有点复杂，可以看一个简单一点的转换器：

Java代码 

```java
public class ListEditor implements EditorInf<List<String>> {  
  
    public List<String> getValueFromObject(Object fromValue) {  
        if(fromValue == null || fromValue.equals(""))  
            return null;  
              
        return getValueFromString(String.valueOf( fromValue));  
          
    }  
  
    public List<String> getValueFromString(String fromValue) {  
        List<String> ret = new ArrayList<String>();  
        String[] datas = fromValue.split(",");   
        for(String data :datas)  
        {  
              
            ret.add(data);  
        }  
        return ret;  
      
    }  
  
  
}  
```

 EditorInf类型的editor专门处理单个参数值的转换，ArrayEditorInf类型的editor专门处理数组参数转换；通过这两种类型的editor我们可以实现复杂的参数类型转换。

更多详细信息请参考bboss mvc的参数绑定测试用例：

[HelloWord.java](https://github.com/bbossgroups/bbossgroups-3.5/blob/3e4d674e1b2cc7b7c02782304a2fb4c4e2172794/bboss-mvc/test/org/frameworkset/mvc/HelloWord.java)
[hello.jsp](https://github.com/bbossgroups/bbossgroups-3.5/blob/3e4d674e1b2cc7b7c02782304a2fb4c4e2172794/bboss-mvc/WebRoot/jsp/examples/hello.jsp)  