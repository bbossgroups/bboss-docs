### bboss mvc大文件上传及or mapping存储大文件机制详解

继续上两篇文章：

bboss mvc文件上传下载实战演练-http://yin-bp.iteye.com/blog/1130035

bboss mvc文件上传下载实战进阶-http://yin-bp.iteye.com/blog/1131637

本文着重介绍bboss mvc大文件上传和利用持久层的or mapping机制存储大文件功能介绍，切入正题

**功能点说明：**

1.jsp form附件上传表单

2.jquery easyui 表单提交

3.mvc控制附件处理方法

4.绑定附件值对象

5.利用or mapping技术持久化附件信息和附件内容  

大致流程如下：
通过将表单提交的附件信息传递给控制方法，mvc将表单数据以及附件信息封装到DeskTopBackGround对象中，利用持久层组件将该附件信息存储到数据库中。

下面逐一说明：  

**首先介绍各个层面都要用到的附件值对象**

Java代码

```java
public class DeskTopBackGround {      
    private String cn_name;  
    private Timestamp creatdate;  
         private String filename;  
    @Column(type="blobfile")  
    private MultipartFile picture;  
         。。。//省略get/set方法  
}  
```

DeskTopBackGround 中的属性MultipartFile picture包含了附件信息和附件内容，需要注意的是，必须通过@Column(type="blobfile")注解说明附件类型，即是一个blob二进制附件还是一个clob文本附件，分别对应为：@Column(type="blobfile")和@Column(type="clobfile")，这样ormapping机制就能清楚地进行相应的处理。

**jsp 附件上传表单**

Html代码 

```html
<form action=""  method="POST" id="addbackground" enctype="multipart/form-data">  
   <table>  
   <tr>  
      <td>  
    名称：<input type="text" name="cn_name" id="cn_name"/>  
      </td>  
   </tr>  
    <tr>  
        <td>  
         附件：<input type="file" name="picture" id="file" />  
        </td>  
     </tr>  
   <tr>  
    <td>  
   <input type="button" class="button" value="添加背景图片" onclick="addBackGround(this)" />  
    </td>  
    </tr>                                   
</table>                                    
</form>     
```

jquery-easyui form表单提交操作：

Js代码

```js
$("#addbackground").form('submit', {  
                "url": "uploadBackGround.page",//控制器请求url  
                onSubmit:function(){              
                    //显示遮罩                            
                    blockUI();    
                },  
                success:function(responseText){   
                    //去掉遮罩    
                    unblockUI();  
                    if(responseText == "success"){  
                        $.messager.alert("提示对话框" , "附件上传成功!");  
                        queryList();  
                    }  
                    else  
                    $.messager.alert("提示对话框" , "附件上传失败:"+responseText);   
                }  
            });   
```

**mvc控制附件处理方法**

Java代码

```java
public  @ResponseBody String uploadBackGround( DeskTopBackGround bean)  
    {  
        try  
        {  
            bean.setFilename(bean.getPicture().getOriginalFilename());//设置附件名称到filename属性  
bean.setCreatdate(new Timestamp(new Date().getTime()));//设置创建时间  
            String sql = "insert into td_sm_desktopstylecustom(filename,creatdate,cn_name,picture) values(#[filename],#[creatdate],#[cn_name],#[picture])";  
            SQLExecutor.insertBean(sql, bean);  
            return "success";  
        }  
        catch (Exception e)  
        {  
            return "fail:"+e.getMessage();  
        }  
          
    }  
```

控制方法包含一个DeskTopBackGround bean参数，mvc框架根据表单的信息生成DeskTopBackGround 对象实例，uploadBackGround方法对该实例进行相应的处理后，通过持久层的or mapping接口存储到数据库表中：
SQLExecutor.insertBean(sql, bean);

然后将处理结果信息相应到客户端，客户端进行相应的提示：
$.messager.alert("提示对话框" , "附件上传成功!");

**后记**

到此本文的正文内容就介绍完了，至于mvc配置文件和持久层数据源配置请参考博客中相关文章：

bboss mvc基础配置介绍-http://yin-bp.iteye.com/blog/1139608

bbossgroups 开发系列文章之-最佳实践-http://bbossgroups.group.iteye.com/group/wiki/3092-mvc-bboss-config

bboss persistent框架数据库连接池配置介-http://yin-bp.iteye.com/blog/352599  