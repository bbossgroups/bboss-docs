# bboss mvc控制器方法跳转地址设置方法介绍

### 1.直接指定跳转地址

Java代码 

```java
public String showlistjsp(ModelMap model) {  
        List<ListBean> beans = null;  
        try {  
            beans = (List<ListBean>) SQLExecutor.queryList(ListBean.class,  
                    "select * from LISTBEAN");  
            model.addAttribute("datas", beans);  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
  
        return "/databind/table.jsp";  
  
    }  
```



### **2.指定跳转地址别名**

地址别名以path:前缀开头，别名对应的地址在mvc控制器配置文件中指定
Java代码  

```
public String showlist(ModelMap model) {  
        List<ListBean> beans = null;  
        try {  
            beans = (List<ListBean>) SQLExecutor.queryList(ListBean.class,  
                    "select * from LISTBEAN");  
            model.addAttribute("datas", beans);  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
  
//      return "/databind/table.jsp";  
        return "path:showlist-ok";//返回地址别名  
    }  
```

在mvc控制器中配置别名path:showlist-ok对应的实际页面地址：
Xml代码

```xml
<property name="/pathalias/*.htm"  
          
        path:showlist-ok="/databind/table.jsp"                    
        class="org.frameworkset.spi.mvc.PathController"/> 
```

### **3.设置地址跳转的方式-forward和redirect**

可以在跳转地址中指定跳转的两种模式：forward 直接指向到目标页面，forward是默认方式，与来源请求是一个请求redirect 重定向到目标页面，重新发出http请求两种方式的使用示例：redirect:

Java代码 

```java
public String showlistjsp(ModelMap model) {  
        List<ListBean> beans = null;  
        try {  
            beans = (List<ListBean>) SQLExecutor.queryList(ListBean.class,  
                    "select * from LISTBEAN");  
            model.addAttribute("datas", beans);  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
  
        return "redirect:/databind/table.jsp";  
  
    }  
```

forward:
Java代码

```java
public String showlistjsp(ModelMap model) {  
        List<ListBean> beans = null;  
        try {  
            beans = (List<ListBean>) SQLExecutor.queryList(ListBean.class,  
                    "select * from LISTBEAN");  
            model.addAttribute("datas", beans);  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
  
        return "forward:/databind/table.jsp";  
  
    }  
```

在地址别名中设置forward和redirect
Xml代码

```xml
path:showlist-ok="forward:/databind/table.jsp"  
path:showlist-ok="redirect:/databind/table.jsp"  
```

### **4.从一个地址别名跳转到其他地址别名**

可以从一个地址别名跳转到其他地址别名，设置方法：

Xml代码 

```xml
<property name="/pathalias/*.htm"  
        path:showlist-ok="/databind/table.jsp"   
        path:delete-ok="path:showlist-ok"  
        path:deletebatch-ok="path:showlist-ok"  
        path:update-ok="path:showlist-ok"  
        path:updatebatch-ok="path:showlist-ok"  
        path:listbean-ok="path:showlist-ok"           
        class="org.frameworkset.spi.mvc.PathController"/>  

```

