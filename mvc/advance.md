### bboss mvc文件上传下载实战进阶

在上一篇文章《bboss mvc文件上传下载实战演练》
http://yin-bp.iteye.com/blog/1130035
中介绍了采用bboss mvc、aop/ioc、persistent组合完成文件上传、存储到数据库、从数据库中下载文件的基本功能，我们看到了如何通过MultipartHttpServletRequest获取上传文件，如何通过SQLExecutor/ConfigSQLExecutor中操作blob字段的api存储和读取文件的基本功能：

FieldRowHandler（获取blob为文件的字段行处理器）

NullRowHandler

本文作为上篇的进阶补充，介绍直接绑定MultipartFile对象或者MultipartFile数组到控制其方法参数或者po对象属性的案例。

   对应的eclipse工程和运行demo可在本文的附件中下载：
http://dl.iteye.com/topics/download/c7393bc6-b8ee-3dd5-b4b3-e969ea63dbc0

demo工程中包含了derby数据库文件目录database，部署到tomcat中运行之前需要修改/src/poolman.xml文件中dburl路径为相应物理路径的下的database/cimdb，编译后即可。

  启动tomcat， 访问的地址还是：
http://localhost:8080/bbossupload/upload/main.page

第一部分 本文的技术要点：

1.控制器方法参数绑定机制增加MultipartFile、MultipartFile[]类型绑定支持,可以和RequestParam注解一起使用，也可以直接使用（默认获取第一个input[file]域对应的附件），使用方法如下：

public String uploadFileWithMultipartFile(@RequestParam(name="upload1")  MultipartFile file,ModelMap model)

public String uploadFileWithMultipartFiles(@RequestParam(name="upload1")  MultipartFile[] files,ModelMap model)

public String uploadFileWithMultipartFile( MultipartFile file,ModelMap model)

public String uploadFileWithMultipartFiles(MultipartFile[] files,ModelMap model)  

2.PO对象属性数据绑定机制增加MultipartFile、MultipartFile[]类型绑定支持,可以和RequestParam注解一起使用，也可以直接与属性名称直接绑定，使用方法如下：

public String uploadFileWithFileBean(FileBean files)

FileBean是一个自定义的java bean，结构如下：

Java代码

```java
public class FileBean  
{  
    private MultipartFile upload1;  
    @RequestParam(name="upload1")  
    private MultipartFile[] uploads;  
    @RequestParam(name="upload1")  
    private MultipartFile anupload;  
    //省略属性的get/set方法  
}     
```

3.改进的SQLParams api，可以直接对MultipartFile对象存入clob或者blob列。

sqlparams.addSQLParam("FILECONTENT", multipartfile,SQLParams.BLOBFILE);

对于大字段的处理建议采用以下方法：

sqlparams.addSQLParam("FILECONTENT", multipartfile,SQLParams.BLOBFILE);//直接传递MultipartFile对      象进行插入

sqlparams.addSQLParam("FILECONTENT", inputStream, size,SQLParams.BLOBFILE);//直接传递  InputStream对象以及流大小Size属性进行插入

一个完整的插入大字段的实例：

 Java代码

```java
String sql = "";  
        try {  
            sql = "INSERT INTO filetable (FILENAME,FILECONTENT,fileid,FILESIZE) VALUES(#[filename],#[FILECONTENT],#[FILEID],#[FILESIZE])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParam("filename", filename, SQLParams.STRING);  
            sqlparams.addSQLParam("FILECONTENT", inputStream, size,SQLParams.BLOBFILE);  
            sqlparams.addSQLParam("FILEID", UUID.randomUUID().toString(),SQLParams.STRING);  
            sqlparams.addSQLParam("FILESIZE", size,SQLParams.LONG);  
            SQLExecutor.insertBean(sql, sqlparams);           
              
        } catch (Exception ex) {  
            ex.printStackTrace();  
            result = false;  
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        } finally {  
            if(inputStream != null){  
                inputStream.close();  
            }  
        }  
```

或者

Java代码

```java
public void uploadClobFile(MultipartFile file) throws Exception  
    {  
  
          
        String sql = "";  
        try {  
            sql = "INSERT INTO CLOBFILE (FILENAME,FILECONTENT,fileid,FILESIZE) VALUES(#[filename],#[FILECONTENT],#[FILEID],#[FILESIZE])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParam("filename", file.getOriginalFilename(), SQLParams.STRING);  
            sqlparams.addSQLParam("FILECONTENT", file,SQLParams.CLOBFILE);  
            sqlparams.addSQLParam("FILEID", UUID.randomUUID().toString(),SQLParams.STRING);  
            sqlparams.addSQLParam("FILESIZE", file.getSize(),SQLParams.LONG);  
            SQLExecutor.insertBean(sql, sqlparams);           
              
        } catch (Exception ex) {  
          
              
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        }   
          
          
    }  
```

4.FieldRowHandler处理器，实现从blob/clob中获取单个字段文件对象的处理,其他类似类型数据也可以使用FieldRowHandler，使用示例如下：

Java代码

```java
public File getDownloadClobFile(String fileid) throws Exception  
    {  
        try  
        {  
            return SQLExecutor.queryTField(  
                                            File.class,  
                                            new FieldRowHandler<File>() {  
  
                                                @Override  
                                                public File handleField(  
                                                        Record record)  
                                                        throws Exception  
                                                {  
  
                                                    // 定义文件对象  
                                                    File f = new File("d:/",record.getString("filename"));  
                                                    // 如果文件已经存在则直接返回f  
                                                    if (f.exists())  
                                                        return f;  
                                                    // 将blob中的文件内容存储到文件中  
                                                    record.getFile("filecontent",f);  
                                                    return f;  
                                                }  
                                            },  
                                            "select * from CLOBFILE where fileid=?",  
                                            fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
    }  
```

第二部分 相关的实现细节

由于上篇文章已经详细介绍了上传下载的代码详细情况，本文中只介绍MultipartFile控制器方法绑定和存储到数据库中部分实现以及下载方法实例，完整的代码请下载附件中的eclipse工程直接运行demo就可以了。

1.控制器方法

Java代码 

```java
public class UploadController  
{  
  
    ...............  
  
    /** 
         * 直接使用MultipartHttpServletRequest 获取附件信息的方法 
     * @param request 
     * @param model 
     * @param idNum 
     * @param type 
     * @param des 
     * @param byid 
     * @return 
     */  
    public String uploadFile(MultipartHttpServletRequest request,  
            ModelMap model)  
    {  
  
        Iterator<String> fileNames = request.getFileNames();  
        // 根据服务器的文件保存地址和原文件名创建目录文件全路径  
          
        try  
        {  
            while (fileNames.hasNext())  
            {  
                String name = fileNames.next();  
                MultipartFile[] files = request.getFiles(name);                
//              file.transferTo(dest)  
                for(MultipartFile file:files)  
                {  
                    String filename = file.getOriginalFilename();  
                    if (filename != null && filename.trim().length() > 0)  
                    {  
                        uploadService.uploadFile(file.getInputStream(), file  
                                .getSize(), filename);  
  
                    }  
                }  
            }  
              
        }  
        catch (Exception ex)  
        {  
            ex.printStackTrace();  
        }  
        return "path:ok";  
    }  
      
      
    /** 
         * RequestParam注解和MultipartFile结合绑定参数方法，将参数upload1对应的附件直接以MultipartFile对象传入控制方法，简单又便捷,附件将被存储到derby数据库的blob字段中。 
     * @param request 
     * @param model 
     * @param idNum 
     * @param type 
     * @param des 
     * @param byid 
     * @return 
     */  
    public String uploadFileWithMultipartFile(@RequestParam(name="upload1")  MultipartFile file,  
            ModelMap model)  
    {  
  
          
        // 根据服务器的文件保存地址和原文件名创建目录文件全路径  
          
        try  
        {  
            String filename = file.getOriginalFilename();  
            if (filename != null && filename.trim().length() > 0)  
            {  
                uploadService.uploadFile(file.getInputStream(), file  
                        .getSize(), filename);  
  
            }             
              
        }  
        catch (Exception ex)  
        {  
            ex.printStackTrace();  
        }  
        return "path:ok";  
    }  
      
    /** 
         * RequestParam注解和MultipartFile结合绑定参数方法，将参数upload1对应的附件直接以MultipartFile对象传入控制方法，简单又便捷,附件将被存储到derby数据库的clob字段中。 
     * @param request 
     * @param model 
     * @param idNum 
     * @param type 
     * @param des 
     * @param byid 
     * @return 
     */  
    public String uploadFileClobWithMultipartFile(@RequestParam(name="upload1")  MultipartFile file,  
            ModelMap model)  
    {  
  
          
        // 根据服务器的文件保存地址和原文件名创建目录文件全路径  
          
        try  
        {  
          
            uploadService.uploadClobFile(file);  
        }  
        catch (Exception ex)  
        {  
            ex.printStackTrace();  
        }  
        return "path:ok";  
    }  
      
    /** 
         * RequestParam注解和MultipartFile[]结合绑定参数方法，将参数upload1(页面上有多个upload1 file input元素)对应的附件直接以MultipartFile[]对象传入控制方法，简单又便捷,附件将被存储到derby数据库的blob字段中。 
     * @param request 
     * @param model 
     * @param idNum 
     * @param type 
     * @param des 
     * @param byid 
     * @return 
     */  
    public String uploadFileWithMultipartFiles(@RequestParam(name="upload1")  MultipartFile[] files,  
            ModelMap model)  
    {  
  
          
        // 根据服务器的文件保存地址和原文件名创建目录文件全路径  
          
        try  
        {  
              
            {  
                   
//              file.transferTo(dest)  
                for(MultipartFile file:files)  
                {  
                    String filename = file.getOriginalFilename();  
                    if (filename != null && filename.trim().length() > 0)  
                    {  
                        uploadService.uploadFile(file.getInputStream(), file  
                                .getSize(), filename);  
  
                    }  
                }  
            }  
              
        }  
        catch (Exception ex)  
        {  
            ex.printStackTrace();  
        }  
        return "path:ok";  
    }  
      
    /** 
         * 本方法演示了po对象中绑定MultipartFile属性的功能。FileBean的结构如下： 
         private MultipartFile upload1; 
    @RequestParam(name="upload1") 
    private MultipartFile[] uploads; 
    @RequestParam(name="upload1") 
    private MultipartFile anupload; 
        上面的结构展示了不同属性绑定方式。 
     * @param request 
     * @param model 
     * @param idNum 
     * @param type 
     * @param des 
     * @param byid 
     * @return 
     */  
    public String uploadFileWithFileBean(FileBean files,  
            ModelMap model)  
    {  
  
          
          
          
        try  
        {  
              
            //进行相应的处理  
              
        }  
        catch (Exception ex)  
        {  
            ex.printStackTrace();  
        }  
        return "path:ok";  
    }  
  
      
  
    /** 
     * 查询数据库的操作也只好放在控制器中处理 
     * @param fileid 
     * @param request 
     * @param response 
     * @throws Exception 
     */  
    public void downloadFileFromBlob(  
            @RequestParam(name = "fileid") String fileid,  
             HttpServletRequest request, HttpServletResponse response)  
            throws Exception  
    {  
  
        uploadService.downloadFileFromBlob(fileid, request, response);  
  
    }  
      
    /** 
     * 直接将blob对应的文件内容以相应的文件名响应到客户端，需要提供request和response对象 
     * 这个方法比较特殊，因为derby数据库的blob字段必须在statement有效范围内才能使用，所以采用了空行处理器，来进行处理 
     * 查询数据库的操作也只好放在控制器中处理 
     * @param fileid 
     * @param request 
     * @param response 
     * @throws Exception 
     */  
    public void downloadFileFromClob(  
            @RequestParam(name = "fileid") String fileid,  
             HttpServletRequest request, HttpServletResponse response)  
            throws Exception  
    {  
  
        uploadService.downloadFileFromClob(fileid, request, response);  
  
    }  
  
    /** 
     * 将数据库中存储的文件内容转储到应用服务器文件目录中，然后将转储的文件下载，无需提供response和request对象 
     *  
     * @param fileid 
     * @return 
     * @throws Exception 
     */  
    public @ResponseBody File downloadFileFromFile(@RequestParam(name = "fileid") String fileid)  
            throws Exception  
    {  
  
        return uploadService.getDownloadFile(fileid);  
    }  
    public @ResponseBody File downloadFileFromClobFile(@RequestParam(name = "fileid") String fileid)  
    throws Exception  
    {  
      
        return uploadService.getDownloadClobFile(fileid);  
    }  
      
  
    ......  
```

2.持久层dao实现部分：

Java代码

```java
public class UpLoadDaoImpl implements UpLoadDao {  
      
    @Override  
    /** 
     * CREATE 
    TABLE CLOBFILE 
    ( 
        FILEID VARCHAR(100), 
        FILENAME VARCHAR(100), 
        FILESIZE BIGINT, 
        FILECONTENT CLOB(2147483647) 
    ) 
     */  
    public void uploadClobFile(MultipartFile file) throws Exception  
    {  
  
          
        String sql = "";  
        try {  
            sql = "INSERT INTO CLOBFILE (FILENAME,FILECONTENT,fileid,FILESIZE) VALUES(#[filename],#[FILECONTENT],#[FILEID],#[FILESIZE])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParam("filename", file.getOriginalFilename(), SQLParams.STRING);  
            sqlparams.addSQLParam("FILECONTENT", file,SQLParams.CLOBFILE);  
            sqlparams.addSQLParam("FILEID", UUID.randomUUID().toString(),SQLParams.STRING);  
            sqlparams.addSQLParam("FILESIZE", file.getSize(),SQLParams.LONG);  
            SQLExecutor.insertBean(sql, sqlparams);           
              
        } catch (Exception ex) {  
          
              
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        }   
          
          
    }  
      
    /** 
     * 上传附件 
     * @param inputStream 
     * @param filename 
     * @return 
     * @throws Exception 
     */  
    public boolean uploadFile(InputStream inputStream,long size, String filename) throws Exception {  
        boolean result = true;  
        String sql = "";  
        try {  
            sql = "INSERT INTO filetable (FILENAME,FILECONTENT,fileid,FILESIZE) VALUES(#[filename],#[FILECONTENT],#[FILEID],#[FILESIZE])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParam("filename", filename, SQLParams.STRING);  
            sqlparams.addSQLParam("FILECONTENT", inputStream, size,SQLParams.BLOBFILE);  
            sqlparams.addSQLParam("FILEID", UUID.randomUUID().toString(),SQLParams.STRING);  
            sqlparams.addSQLParam("FILESIZE", size,SQLParams.LONG);  
            SQLExecutor.insertBean(sql, sqlparams);           
              
        } catch (Exception ex) {  
            ex.printStackTrace();  
            result = false;  
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        } finally {  
            if(inputStream != null){  
                inputStream.close();  
            }  
        }  
        return result;  
    }  
      
    public File getDownloadFile(String fileid) throws Exception  
    {  
        try  
        {  
            return SQLExecutor.queryTField(  
                                            File.class,  
                                            new FieldRowHandler<File>() {  
  
                                                @Override  
                                                public File handleField(  
                                                        Record record)  
                                                        throws Exception  
                                                {  
  
                                                    // 定义文件对象  
                                                    File f = new File("d:/",record.getString("filename"));  
                                                    // 如果文件已经存在则直接返回f  
                                                    if (f.exists())  
                                                        return f;  
                                                    // 将blob中的文件内容存储到文件中  
                                                    record.getFile("filecontent",f);  
                                                    return f;  
                                                }  
                                            },  
                                            "select * from filetable where fileid=?",  
                                            fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
    }  
      
    public File getDownloadClobFile(String fileid) throws Exception  
    {  
        try  
        {  
            return SQLExecutor.queryTField(  
                                            File.class,  
                                            new FieldRowHandler<File>() {  
  
                                                @Override  
                                                public File handleField(  
                                                        Record record)  
                                                        throws Exception  
                                                {  
  
                                                    // 定义文件对象  
                                                    File f = new File("d:/",record.getString("filename"));  
                                                    // 如果文件已经存在则直接返回f  
                                                    if (f.exists())  
                                                        return f;  
                                                    // 将blob中的文件内容存储到文件中  
                                                    record.getFile("filecontent",f);  
                                                    return f;  
                                                }  
                                            },  
                                            "select * from CLOBFILE where fileid=?",  
                                            fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
    }  
  
    @Override  
    public void deletefiles() throws Exception  
    {  
  
        SQLExecutor.delete("delete from filetable ");     
        SQLExecutor.delete("delete from CLOBFILE ");      
    }  
  
    @Override  
    public List<HashMap> queryfiles() throws Exception  
    {  
  
        return SQLExecutor.queryList(HashMap.class, "select FILENAME,fileid,FILESIZE from filetable");  
          
    }  
    @Override  
    public List<HashMap> queryclobfiles()throws Exception  
    {  
  
        return SQLExecutor.queryList(HashMap.class, "select FILENAME,fileid,FILESIZE from CLOBFILE");  
          
    }  
  
    @Override  
    public void downloadFileFromBlob(String fileid, final HttpServletRequest request,  
            final HttpServletResponse response) throws Exception  
    {  
  
        try  
        {  
            SQLExecutor.queryByNullRowHandler(new NullRowHandler() {  
                @Override  
                public void handleRow(Record record) throws Exception  
                {  
  
                    StringUtil.sendFile(request, response, record  
                            .getString("filename"), record  
                            .getBlob("filecontent"));  
                }  
            }, "select * from filetable where fileid=?", fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
          
    }  
      
    @Override  
    public void downloadFileFromClob(String fileid, final HttpServletRequest request,  
            final HttpServletResponse response) throws Exception  
    {  
  
        try  
        {  
            SQLExecutor.queryByNullRowHandler(new NullRowHandler() {  
                @Override  
                public void handleRow(Record record) throws Exception  
                {  
  
                    StringUtil.sendFile(request, response, record  
                            .getString("filename"), record  
                            .getClob("filecontent"));  
                }  
            }, "select * from CLOBFILE where fileid=?", fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
          
    }  
  
      
}  
```

代码都很简单，也非常容易理解，这里不做过多的解释。有问题可以留言讨论,也可以加入群组：

21220580

3625720

154752521

官方网站：
http://www.bbossgroups.com/