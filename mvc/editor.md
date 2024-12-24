### bboss 属性编辑器在mvc中的应用

bboss 中提供了EditorInf属性编辑器接口，一直被应用于ioc组件属性注入时用来处理和转换属性的值，本文着重介绍它在mvc中的应用。

**1.属性编辑器接口**

两个接口如下：

com.frameworkset.util.EditorInf -用于mvc RequestParam注解时，只会接收到一个String类型参数，然后通过接口方法进行相应处理和值转换

com.frameworkset.util.ArrayEditorInf-这个接口作为EditorInf的子类，只用于mvc RequestParam注解中并且接收String[]类型参数，然后通过接口方法进行相应处理和值转换

EditorInf 和ArrayEditorInf只提供了以下两个接口方法：

Java代码 

```java
public interface EditorInf<T>  
{      
    T getValueFromObject(Object fromValue) ;      
    T getValueFromString(String fromValue);    
}  
```

EditorInf 和ArrayEditorInf结合@RequestParam注解既可以直接应用于控制方法的参数，也可以应用于控制器参数为po对象时，对对象的属性值进行转换，这里以第一种情况为例进行说明（第二种情况类似）。

**2.EditorInf 使用示例**

定义一个具体的属性编辑，接收逗号分隔的参数，并将参数转换用逗号分割为一个List**<String**>并返回：

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

在控制方法参数中使用这个编辑器：

Java代码

```java
public String sayHelloListEditor(@RequestParam(editor="org.frameworkset.mvc.ListEditor") List<String> name,ModelMap model)  
    {  
  
        if (name != null && name.size() > 0)  
            model.addAttribute("serverHelloBean", name);  
        else  
            ;  
  
        return "path:sayHello";  
    }  
```

说明：

@RequestParam(editor="org.frameworkset.mvc.ListEditor") List**<String**> name

name参数对应表单中的元素**<input type="text" name="name" value="aa,bb"**>，name参数前定义了注解@RequestParam(editor="org.frameworkset.mvc.ListEditor")，通过RequestParam的editor属性指定了上面定义的属性编辑器ListEditor，这样bboss mvc框架就会自动将表单中的name元素的值aa,bb转换成一个List对象，list中的第一个元素为aa，第二个元素为bb。

**3.ArrayEditorInf 使用示例**

定义一个ArrayEditorInf接口实现，接收一个name数组对象String[]，并将这个数组转换为List<String[]>,数组的每个元素值是带逗号的字符串转换而成，例如{"aa,bb","cc,dd"}。

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

在控制方法参数中使用这个编辑器：

Java代码

```java
public String sayHelloEditors(@RequestParam(editor="org.frameworkset.mvc.ListStringArrayEditor") List<String[]> name,ModelMap model)  
    {  
        if(name != null && name.size() > 0)  
        {  
            StringBuffer ret = new StringBuffer();  
            for(String[] yourname:name)  
            {  
                if (yourname != null && yourname.length > 0)  
                    ret.append(StringUtil.arrayToDelimitedString(yourname, ",")).append("<br/>");           
                  
            }  
            model.addAttribute("serverHelloBean", ret.toString());  
        }  
  
        return "path:sayHello";  
    }  
```

说明：

(@RequestParam(editor="org.frameworkset.mvc.ListStringArrayEditor") List<String[]> name
name参数对于前端表单中的两个name元素：

**<input type="text" name="name" value="aa,bb"**>

**<input type="text" name="name" value="cc,dd"**>

同样我们在name参数的前面定义了注解

@RequestParam(editor="org.frameworkset.mvc.ListStringArrayEditor")，通过RequestParam的editor属性指定了上面定义的属性编辑器ListStringArrayEditor，这样表单提交时，name参数就会以数组的方式进行提交，并交给专门接收数组的ListStringArrayEditor编辑器进行转换并生成List<String[]> 类型的值返回，List<String[]>中会包含两个元素，第一个元素是aa,bb转换成的String[]{aa,bb},第二个元素是cc,dd转换生成的String[]{cc,dd}。

**4.举个简单的案例**

持久层使用mvc控制方法结合EditorInf属性编辑器转换得到的List<String>数据来作为动态sql语句中的in操作条件的示例代码：

PO对象BillCondition ,里面包含了三个List<String> 类型变量bukrs、prctr、belnr，他们分别和前端表单元素对应：

**<input type="text" name="bukrs" value="aa,bb"**/>

**<input type="text" name="prctr" value="cc,dd"/**>

**<input type="text" name="belnr" value="ee,ff"/**>

表单提交后，经过mvc框架的Editor插件转换为List**<String**> 类型的数据,并分别设置到PO对象BillCondition 的这三个属性中。

注意：@RequestParam(editor="org.frameworkset.mvc.ListEditor")
中的editor对于每个属性只有一个实例，而不是每次请求都会创建一个实例，以便提升系统性能。

Java代码

```java
import java.util.Date;  
import java.util.List;  
  
import org.frameworkset.util.annotations.RequestParam;  
  
  
public class BillCondition {  
    private String budat;    //凭证中的记帐日期   
    @RequestParam(editor="org.frameworkset.mvc.ListEditor")   
    private List<String> bukrs;  //公司代码  
    @RequestParam(editor="org.frameworkset.mvc.ListEditor")   
    private List<String>  prctr;  //利润中心  
    @RequestParam(editor="org.frameworkset.mvc.ListEditor")   
    private List<String>  belnr; //会计凭证号码  
  
  
    @RequestParam(dateformat="yyyy-MM-dd")  
    private Date beginDate;  
    @RequestParam(dateformat="yyyy-MM-dd")  
    private Date endDate;  
      
    private String sortKey;  
    private boolean sortDESC;  
    private Integer excelType;  
      
      
          
}  
```

 使用list**<String**>进行IN查询的sql语句：

Xml代码

```xml
<property name="queryBillList">  
    <![CDATA[ 
        select * from TD_SFA_BILL_ZFIT0408 where 1=1  
        #if($bukrs && $bukrs.size() > 0) 
            and BUKRS in ( 
               #foreach($group in $bukrs)   
                      #if($foreach.index == 0)   
                          #[bukrs[$foreach.index]]   
                      #else   
                           ,#[bukrs[$foreach.index]]   
                       #end   
                  #end   
               ) 
        #end 
        #if($prctr && $prctr.size() > 0) 
            and PRCTR in (  
                #foreach($group in $prctr)   
                          #if($foreach.index == 0)   
                              #[prctr[$foreach.index]]   
                          #else   
                               ,#[prctr[$foreach.index]]   
                           #end   
                   #end   
                ) 
        #end 
        #if($belnr && $belnr.size() > 0) 
            and BELNR in (  
                #foreach($group in $belnr)   
                          #if($foreach.index == 0)   
                              #[belnr[$foreach.index]]   
                          #else   
                               ,#[belnr[$foreach.index]]   
                           #end   
                   #end    
                   ) 
        #end 
        #if($beginDate && !$beginDate.equals("")) 
            and BUDAT >= #[beginDate] 
        #end 
        #if($endDate && !$endDate.equals("")) 
            and BUDAT <= #[endDate] 
        #end 
        #if($sortKey && !$sortKey.equals("")) 
            order by $sortKey  
            #if($sortDESC ) 
                desc 
            #else 
                asc 
            #end     
        #else 
            order by GJAHR desc 
        #end 
         
    ]]>  
</property>  
```

特别说明一下sql语句中的这段代码：

#if($bukrs && $bukrs.size() > 0)

and BUKRS in (

   #foreach($group in $bukrs) 

​                       #if($foreach.index == 0) 

​                           #[bukrs[$foreach.index]] 

​                       #else 

​                            ,#[bukrs[$foreach.index]] 

​                        #end 

​                   #end 

首先通过if语句判断bukrs 存在并且里面有元素（size>0）,如果条件成立则拼接in条件，采用foreach语句来循环设置每个元素到in条件#[bukrs[$foreach.index]]  ，其中foreach.index是循环变量，如果不是第一个在元素前面添加逗号，这样bboss持久层框架在执行的时候会将这些变量元素转换为预编译sql来执行。

最后看看整个代码流程：表单提交->控制方法参数绑定->控制方法调用ConfigSQLExecutor来执行这sql语句

Java代码

```java
public String queryListLrzxBean( BillCondition appcondition ,ModelMap model) {  
        List<BillBean> beans = null;  
        try{  
            beans=configSQLExecutor.queryListBean(BillBean.class,"queryBillList", appcondition);  
model.addAttribute("beans",beans);  
  
        }catch(SQLException e){  
            model.addAttribute("errormsg",e.getMessage());  
        }  
        return "path:billlist";  
    }  
```

**5.总结**

综上所述，当bboss mvc提供的默认参数绑定机制无法满足您的项目中实际参数绑定需要时，可以通过bboss中的两个EditorInf 和ArrayEditorInf结合@RequestParam注解来实现你想要的参数转换功能，非常方便快捷地提供自己的参数转换插件，从而实现各种复杂的参数绑定功能。