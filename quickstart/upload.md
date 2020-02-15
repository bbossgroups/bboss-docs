### bboss mvc文件上传下载新增功能详解

**1.概述**

最近对bboss mvc的文件上传和下载功能做了以下改造：

a.文件上传插件增加对html5文件上传功能的支持-application/octet-stream请求的处理

b.文件上传插件增加IgnoreFieldNameMultipartFile接口

c.文件下载

org.frameworkset.http.converter.FileMessageConvertor插件支持下载Resource接口对应的资源

下面详细介绍每一部分。  

**2.html5文件上传功能**

html5文件上传时对应的mime类型可以为application/octet-stream，之前的文件上传插件是不支持application/octet-stream类型的附件的处理的，因此对上传插件进行修改从而支持这种类型的附件的处理，目前支持单次一个附件的处理，服务端的处理方式没有改变，请参考另外两篇附件处理的文章。为了测试该功能，在火狐浏览器下使用xheditor-1.1.13在线编辑器的文件上传实例demo08.html功来演示该功能，xheditor-1.1.13已经集成到bbossgroups的最佳实践工程[demoproject](https://github.com/bbossgroups/bbossgroups-3.5/tree/7761cc3e2ec04839374f0e0d6750613113347862/bestpractice/demoproject)中，对应的jsp文件为：

/demoproject/WebRoot/xheditor/demos/demo08.jsp

下载该demoproject导入eclipse并部署到tomcat在火狐浏览器中输入（xheditor自动将上传类型定义为application/octet-stream，ie还是以传统的form multidata方式提交）
http://localhost:8080/WebRoot/xheditor/demos/demo08.jsp即可点击里面的图片上传功能查看效果，这里我们只看看一下demo08.jsp中和文件上传相关的代码：

Js代码

```js
<link rel="stylesheet" href="common.css" type="text/css" media="screen" />  
<script type="text/javascript" src="../jquery/jquery-1.4.4.min.js"></script>  
<script type="text/javascript" src="../xheditor-1.1.13-zh-cn.min.js"></script>  
<script type="text/javascript">  
$(pageInit);  
function pageInit()  
{  
    $.extend(xheditor.settings,{shortcuts:{'ctrl+enter':submitForm}});  
    $('#elm1').xheditor({upLinkUrl:"<%=request.getContextPath()%>/file/upload.page",upLinkExt:"zip,rar,txt",upImgUrl:"<%=request.getContextPath()%>/file/uploadwithbean.page?testparam=中文",upImgExt:"jpg,jpeg,gif,png",upFlashUrl:"<%=request.getContextPath()%>/file/upload.page",upFlashExt:"swf",upMediaUrl:"<%=request.getContextPath()%>/file/upload.page",upMediaExt:"wmv,avi,wma,mp3,mid"});  
    $('#elm2').xheditor({upLinkUrl:"<%=request.getContextPath()%>/file/upload.page?immediate=1",upLinkExt:"zip,rar,txt",upImgUrl:"<%=request.getContextPath()%>/file/upload.page?testparam=test&immediate=1",upImgExt:"jpg,jpeg,gif,png",upFlashUrl:"<%=request.getContextPath()%>/file/upload.page?immediate=1",upFlashExt:"swf",upMediaUrl:"<%=request.getContextPath()%>/file/upload.page?immediate=1",upMediaExt:"wmv,avi,wma,mp3,mid"});  
    $('#elm3').xheditor({upLinkUrl:"<%=request.getContextPath()%>/file/upload.page",upLinkExt:"zip,rar,txt"});  
    $('#elm4').xheditor({upImgUrl:"<%=request.getContextPath()%>/file/upload.page",upImgExt:"jpg,jpeg,gif,png"});  
    $('#elm5').xheditor({upFlashUrl:"<%=request.getContextPath()%>/file/upload.page",upFlashExt:"swf",upMediaUrl:"<%=request.getContextPath()%>/file/upload.page",upMediaExt:"wmv,avi,wma,mp3,mid"});  
    $('#elm6').xheditor({upLinkUrl:"<%=request.getContextPath()%>/file/upload.page",upLinkExt:"zip,rar,txt",upImgUrl:"<%=request.getContextPath()%>/file/upload.page",upImgExt:"jpg,jpeg,gif,png",onUpload:insertUpload});  
}  
```

服务端处理方法为(\bestpractice\demoproject\src\org\frameworkset\mvc\FileController.java)：

Java代码

```java
public @ResponseBody String upload(IgnoreFieldNameMultipartFile filedata,String testparam) throws IllegalStateException, IOException  
    {  
        if(filedata != null)  
        {  
//将附件存入d盘下的文件tst.png  
            filedata.transferTo(new File("d:/tst.png"));  
        }  
        return "{\"err\":\"\",\"msg\":\"tst.png\"}";  
    }  
```

upload方法可以接收html5上传的附件也可以接收html4 传统表单方式上传的附件，因此屏蔽了html版本的差异性。upload方法两个参数：

IgnoreFieldNameMultipartFile filedata 包含上传的附件正文和附加信息，IgnoreFieldNameMultipartFile 类型为新加的接口类型，和MultipartFile类型的区别下节详细介绍。

String testparam 上传时指定的一个额外参数用来展示html4和html5中，随同附件一起提交的普通参数都能被bboss 的文件上传插件正常处理。

**3.IgnoreFieldNameMultipartFile接口**

IgnoreFieldNameMultipartFile 类型为新加的接口类型，它和之前的MultipartFile接口类型的区别在这里做详细介绍。

IgnoreFieldNameMultipartFile接口是MultipartFile接口的子类，IgnoreFieldNameMultipartFile没有新加任何方法，之所以添加IgnoreFieldNameMultipartFile接口的原因为：MultipartFile接口类型对应的控制器方法参数名称或者bean属性名称必须和对应的表单input[file]元素的name保持一致（或者必须通过@RequestParam注解来指定控制器方法参数或者bean属性和input[file]元素的name的映射关系），这对应一般的表单问题不大，因为可以明确地知道表单中file input元素的名称。但是对于第三方文件上传控件（例如swfupload，或者xheditor之类的插件）我们无法明确知道file input的name属性的值，因此无法通过input的名称和控制器方法参数或者bean属性进行值注入绑定。对于这种情况有两种处理办法，一种是采用比较原始的MultipartHttpServletRequest 对象来获取并处理所有的附件信息：

Java代码

```java
public void uploadFile(MultipartHttpServletRequest request,  
            ModelMap model, @RequestParam(name = "upload_")  
            String upload_) {  
        Iterator<String> fileNames = request.getFileNames();  
        List<UpFile> files = new ArrayList<UpFile>();  
        File dir = new File("d:/mutifiles/");  
        if(!dir.exists())  
            dir.mkdirs();  
        while (fileNames.hasNext()) {  
            String name = fileNames.next();  
            MultipartFile file = request.getFile(name);  
            String temp = file.getOriginalFilename();  
  
            try {  
                file.transferTo(new File("d:/mutifiles/" + temp));  
            } catch (Exception e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
            UpFile uf = new UpFile();  
            uf.setFileName(temp);  
            uf.setFileType(file.getContentType());  
            files.add(uf);  
        }  
        model.addAttribute("files", files);  
    }  
```

第二种方式就是本文介绍的方式，借助于新的IgnoreFieldNameMultipartFile接口来处理：

Java代码

```java
public @ResponseBody String upload(IgnoreFieldNameMultipartFile[] files,String testparam) throws IllegalStateException, IOException  
    {  
        if(filedata != null)  
        {  
            filedata[0].transferTo(new File("d:/tst.png"));  
        }  
        return "{\"err\":\"\",\"msg\":\"tst.png\"}";  
    }  
```

我们将附件参数files的类型指定为IgnoreFieldNameMultipartFile或者IgnoreFieldNameMultipartFile[]，明确地告诉mvc忽略控制器方法参数或者bean属性与input file元素的name值进行名称映射绑定，直接将上传的附件对象作为控制器方法参数或bean属性的值注入，不管表单提交了多少个input file元素，会将所有input file元素的对应的附件或者附件数组注入到控制器方法参数或者bean属性中，这就是引入IgnoreFieldNameMultipartFile类型的目的，只要控制器方法参数或者bean属性的类型为IgnoreFieldNameMultipartFile，那么就按这个规则来进行控制器方法参数值或者bean属性值绑定。

**4.org.frameworkset.http.converter.FileMessageConvertor插件支持下载Resource接口对应的资源**

FileMessageConvertor插件除了任然支持File对象和Blob对象的下载外，新增了Resource类型资源的下载，包括以下类型：

ClassPathResource -- 适用于应classpath下面的各种资源

ServletContextResource --适用于web应用根目录及子目录下的各种资源

FileSystemResource --适用于文件系统中各种文件资源

UrlResource --适用于url连接对应各种资源

ByteArrayResource--适用于二进制资源

我们以ClassPathResource 为例来说明具体的使用方法如下：

Java代码

```java
public @ResponseBody  
    Resource exportExeclTemplate() throws Exception {  
          
        String fileName = "com/sany/mms/background/action/exceldata.xls";  
          
        ClassPathResource classpath = new ClassPathResource(fileName);  
        return classpath;  
    }  
```

**5.FileBlob的使用**

FileBlob对象主要是用来直接下载Blob对象和InputStream流对象，同时可以指定一个下载文件名，实例如下：

Java代码

```java
ublic @ResponseBody  
    FileBlob exportExeclTemplate() throws Exception {  
          
        String fileName = "com/sany/mms/background/action/exceldata.xls";  
        FileBlob fb = new FileBlob ("exceldata.xls",new FileInputstream(new File(fileName)))//下载文件流  
                FileBlob fb = new FileBlob ("exceldata.xls",Blob对象) //下载blob流  
                FileBlob fb = new FileBlob ("test.xml",new URL("http://localhost:8080/bboss/test.xml"));//下载url地址对应的资源  
        return fb;  
    }  
```

到此，各个部分介绍完毕，如果有不妥之处，欢迎大家批评指正。