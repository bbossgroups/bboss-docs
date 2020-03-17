### bboss mvc 对象自动转换为json响应请求浅析

**bbossgroups mvc 对象自动转换为json响应请求有两种使用方式**

方式一 服务端指定响应datatype为json，将返回对象直接转换为json数据返回到客户端

方式二 客户端请求中的datatype为json，则将返回对象直接转换为json数据返回到客户端

如果要使用对象转json数据功能，必须在bboss-mvc.xml文件中的httpMessageConverters节点中配置以下jackson转换器：

Xml代码

```xml
<property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter"/>  
```

下面分别讲述两种方法的使用方法。

**方式一 服务端指定响应datatype为json，将返回对象直接转换为json数据返回到客户端**

自bbossgroups 3.2版本以来，我们可以直接在服务端的控制方法的ResponseBody注解指定返回数据类型（目前支持两种方式：json和String），我们来看服务端控制器方法的编写方式：

Java代码

```java
public @ResponseBody(datatype = "json")  
    AjaxResponseBean deleteRequester(  
            @RequestParam(name = "service_requester_id") String service_requester_id,  
            HttpServletResponse response) throws Exception {  
        AjaxResponseBean ajaxResponseBean = new AjaxResponseBean();  
        try {  
            requesterService.deleteRequester(service_requester_id);  
            ajaxResponseBean.setStatus("success");  
  
        } catch (Exception e) {  
                        ajaxResponseBean.setStatus("error");  
            if (e.getMessage() != null  
                    && e.getMessage().indexOf("constraint") > 0) {  
                                ajaxResponseBean.setData("存在关联数据，不能删除！");  
            } else {  
                ajaxResponseBean.setE(e.getMessage());  
            }  
        }  
        return ajaxResponseBean;  
    }  
```

我们再看一下客户端如果通过请求与该控制器方法相对应的url来得到相对应的json对象：

Js代码

```js
var service_requester_id = cks.eq(0).val();                               
                            $.post(  
                                'deleteRequester.page',  
                                {  
                                    "service_requester_id" : service_requester_id  
                                },    
                                function(responseText, textStatus) {      
                                    alert(responseText.data);                   alert(responseText.status);                                             });  
```

我们在看一下将被转换为json数据的java对象AjaxResponseBean ：

Java代码 

```java
package org.ffameworkset.mvc;  
  
import java.io.Serializable;  
  
public class AjaxResponseBean implements Serializable {  
    //状态,success表示成功，error表示失败  
    private String status;  
      
    //信息  
    private String data;  
      
    private String e;  
      
    public String getStatus() {  
        return status;  
    }  
    public void setStatus(String status) {  
        this.status = status;  
    }  
    public String getData() {  
        return data;  
    }  
    public void setData(String data) {  
        this.data = data;  
    }  
    public String getE() {  
        return e;  
    }  
    public void setE(String e) {  
        this.e = e;  
    }  
}  
```

**方式二 客户端请求中的datatype为json，则将返回对象直接转换为json数据返回到客户端**

这种方式是由客服端提交请求时，指定数据类型dataType为json，先看客户端代码：

Js代码

```js
//缓存二级菜单  
var moduleItems = new Array();  
$.ajax({  
            type : "post",  
            dataType : "json",  
            data : {  
                "moduleId" : moduleId  
            },  
            url : "menu/queryMenu.page",  
            cache : false,  
            async : false,// ajax同步标志修改  
            error : function() {  
                window.parent.locaction="logout.jsp";                 
            },  
            success : function(data) {  
                //将返回的数据放入js缓存  
                moduleItems[moduleId] = data;                 
            }  
        });  
```

遍历json list对象及访问对象属性：

Js代码

```js
//从js缓存中读取二级菜单信息  
    var data = moduleItems[moduleId];  
    if (data.length > 0) {  
        $.each(data, function(i) {  
              
            var span = $("<span style=\"cursor:pointer;\" class=\"func\" id=\""  
                    + this.id  
                    + "Node"  
                    + "\" title=\""  
                    + this.name  
                    + "\" dataType='iframe' dataLink='"  
                    + this.pathU  
                    + "' iconImg='images/msn.gif'>"  
                    + this.name  
                    + "</span>").click(function() {         
                                              
                        //如果二级菜单项被点击，则触发主体内容窗口的addTab函数               
                        var name = $(this).attr('title');  
                        var dataLink = $(this).attr('dataLink');  
                        addTab(name, dataLink);  
                    });  
                  
                    var currentdiv = $("<div style=\"width:100px;float:left;margin-left:5px;\"><div style=\"text-align:center;\"><img src=\""  
                            + this.imageUrl  
                            + "\" width=\"25\" height=\"25\" style=\"cursor:pointer;\" on></div><div style=\"margin-top:1px;text-align:center;color:#000;\" id=\""  
                            + this.id + "\"></div></div>");  
                    currentdiv.find("img").click(function() {  
                        currentdiv.find("span").click();                              
                    });  
                    $(".bj").append(currentdiv);  
                    $("#" + this.id).append(span);  
                      
        });  
    }     
```

url : "menu/queryMenu.page"指定了要请求的控制器方法，下面我们就看一下该控制器方法的实现代码：

Java代码

```java
public @ResponseBody  
    List<MenuItemU> queryMenu(@RequestParam(name = "moduleId") String moduleId,  
            HttpServletRequest request) {  
                try {  
            AccessControl control = AccessControl.getAccessControl();  
              
            String modulePath = control.getCurrentSystemID()  
                    + "::menu://sysmenu$root/" + moduleId + "$module";  
            MenuHelper menuHelper = new MenuHelper(  
                    control.getCurrentSystemID(), control);  
            ItemQueue itemQueue = menuHelper.getSubItems(modulePath);  
                        List<MenuItemU> list = new ArrayList<MenuItemU>();  
            for (int i = 0; itemQueue != null && i < itemQueue.size(); i++) {  
                Item item = itemQueue.getItem(i);  
                if (!item.isUsed()) {  
                    continue;  
                }  
                String contextPath = request.getContextPath();  
                String url = MenuHelper.getMainUrl(contextPath, item,  
                        (java.util.Map) null);  
                MenuItemU menuItemU = new MenuItemU();  
                menuItemU.setId(item.getId());  
                menuItemU.setName(item.getName());  
                menuItemU.setImageUrl(item.getMouseclickimg());  
                menuItemU.setPathU(url);  
                menuItemU.setType("item");  
                list.add(menuItemU);  
            }  
  
                return list;  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        return null;  
    }  
```

该方法通过@ResponseBody注解声明告诉mvc框架直接将类型为 List<MenuItemU>的返回值转换为json数据（因为客户端指定响应数据类型为json）响应到客户端。

MenuItemU对象的结构如下：

Java代码

```java
public class MenuItemU {  
    private String id;  
    private String name;  
    private String imageUrl;  
    private String pathU;  
    private String type;  
      
    private boolean hasSon;  
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
    public String getImageUrl() {  
        return imageUrl;  
    }  
    public void setImageUrl(String imageUrl) {  
        this.imageUrl = imageUrl;  
    }  
    public String getPathU() {  
        return pathU;  
    }  
    public void setPathU(String pathU) {  
        this.pathU = pathU;  
    }  
    public String getType() {  
        return type;  
    }  
    public void setType(String type) {  
        this.type = type;  
    }  
    public boolean getHasSon() {  
        return hasSon;  
    }  
    public void setHasSon(boolean hasSon) {  
        this.hasSon = hasSon;  
    }  
}  
```

到此bboss mvc中的控制器对对象转换为json数据的自动处理功能就介绍完了。