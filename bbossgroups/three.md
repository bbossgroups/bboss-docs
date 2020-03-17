### bbossgroups 3.3 发布

bbossgroups 3.3 发布，该版本在3.2的基础上做了非常多的功能增强和功能改进，具体情况参考后面的功能点清单。

项目博客:

http://yin-bp.iteye.com/

项目sourceforge下载地址:

http://sourceforge.net/projects/bboss/files/

项目官网：

[http://www.bbossgroups.com](http://www.bbossgroups.com/)

release version : bbossgroups-3.3

release date: 2011/08/14

********************************************************
release futures:

************************************************bbossgroups-3.3******************************

1.aop/ioc  

◆ 增加netty协议消息大小配置参数，解决发送/接收的数据报文超过默认1M时传输失败的问题

   Xml代码

```xml
<!-- 能够解码的最大数据size，超过时，将抛异常，默认20M -->  
<property name="maxFramgeLength_" value="20971520" />  
  
<!-- 编码块大小 -->  
<property name="estimatedLength_" value="1024" />  
```

◆ 修改默认服务发布时，serviceport带了ws:前缀

◆ 修复注入属性缺陷：

当属性值注入以后没有立即退出注入循环，而是把循环跑完，对性能有一定的影响。

修改程序：

/bbossaop/src/org/frameworkset/spi/assemble/BeanAccembleHelper.java

◆ 修复配置文件sql不能安照特定数据库类型或者到指定数据库sql语句的缺陷

2.mvc

◆ 控制器方法参数绑定机制增加MultipartFile、MultipartFile[]类型绑定支持,必须和RequestParam注解一起使用，使用方法如下：

Java代码

```java
public String uploadFileWithMultipartFile(@RequestParam(name="upload1")  MultipartFile file,    
            ModelMap model)    
public String uploadFileWithMultipartFiles(@RequestParam(name="upload1")  MultipartFile[] files,    
            ModelMap model)  
```

◆ PO对象属性数据绑定机制增加MultipartFile、MultipartFile[]类型绑定支持,可以和RequestParam注解一起使用，也可以直接与属性名称直接绑定，使用方法如下：

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

◆ 完善@ResponseBody注解，增加直接对文件下载功能的支持，只要控制器方法返回File对象即可

◆ 完善认证拦截器功能，增加认证失败后跳转页面的方式为redirect和forward两种，可以在拦截器上配置directtype属性

来实现具体的跳转方式：

Xml代码

```xml
<property class="org.frameworkset.web.interceptor.MyFirstInterceptor">    
                    <!-- 配置认证检查拦截器拦截url模式规则 -->    
                    <property name="patternsInclude">    
                        <list componentType="string">    
                            <property value="/**/*.htm"/>    
                        </list>    
                    </property>    
                    <!-- 配置认证检查拦截器不拦截url模式规则 -->    
                    <property name="patternsExclude">    
                        <list componentType="string">    
                            <property value="/*.html"/>    
                        </list>    
                    </property>    
                    <property name="redirecturl" value="/login.jsp"/>    
                    <property name="directtype" value="forward"/>    
                </property>    
```

◆ 修复mvc分页跳转页码为负数时，不能正常分页的问题

◆ 修复ResponseBody指定数据返回类型和字符集不生效的问题

3.persistent

◆ 处理日期和时间类型时转换为字符串时，如果值为空时抛出空指针异常的问题修复

◆ 解决sql server元数据获取为空的问题

◆ 改进SQLParams api，可以直接对MultipartFile对象存入clob或者blob列。

sqlparams.addSQLParam("FILECONTENT", multipartfile,SQLParams.BLOBFILE);  

对于大字段的处理建议采用以下方法：

Java代码 

```java
sqlparams.addSQLParam("FILECONTENT", multipartfile,SQLParams.BLOBFILE);//直接传递MultipartFile对象进行插入    
    sqlparams.addSQLParam("FILECONTENT", inputStream, size,SQLParams.BLOBFILE);//直接传递InputStream对象以及流大小Size属性进行插入   
```

◆ 增加FieldRowHandler处理器，以便实现从blob/clob中获取单个字段文件对象的处理,其他类似类型数据也可以使用FieldRowHandler，使用示例如下：

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

◆ 增加对文件上传入库和从db下载功能的支持,使用实例

上传

Java代码

```java
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
```

下载

Java代码

```java
SQLExecutor.queryByNullRowHandler(new NullRowHandler(){    
                @Override    
                public void handleRow(Record record)    
                        throws Exception    
                {    
                    record.getBlob("filecontent").getBinaryStream();    
                    StringUtil.sendFile(request, response, record.getString("filename"),record.getBlob("filecontent"));    
                }}, "select * from filetable where fileid=?",fileid);   
```

◆ 如果没有指定一条sql语句，PreparedDBUtil.executePreparedBatch将报出异常，这个不是很合理
直接改为info方式。

4.taglib

◆ 增加map和mapkey两个标签，用来循环迭代展示map中的value对象值或者value对象中的数据值以及mapkey值

使用方法如下：

Html代码

```html
<table>    
        <h3>map<String,po>对象信息迭代功能</h3>    
        <pg:map requestKey="mapbeans">    
            
            <tr class="cms_data_tr">    
                <td>    
                    mapkey:<pg:mapkey/>    
                </td>     
                <td>    
                    id:<pg:cell colName="id" />    
                </td>     
                <td>    
                    name:<pg:cell colName="name" />    
                </td>     
            </tr>    
        </pg:map>    
            
            
    </table>    
        
    <table>    
        <h3>map<String,String>字符串信息迭代功能</h3>    
        <pg:map requestKey="mapstrings">    
            
            <tr class="cms_data_tr">    
                <td>    
                    mapkey:<pg:mapkey/>    
                </td>     
                <td>    
                    value:<pg:cell/>    
                </td>     
                    
            </tr>    
        </pg:map>    
            
            
    </table>    
```

◆ cell标签提供actual属性，可以直接输出改属性设定的值，值可以为el表达式的值

◆ 修改empty和notempty两个逻辑标签增加对Collection和Map对象的为empty判断支持

◆ 修改rowcount标签，去除多余的空格

◆ 完善标签排序功能补丁

增加相应的指示箭头，标识升序和降序

相关文件

/bboss-mvc/WebRoot/include/pager.css

WebRoot\WEB-INF\lib\frameworkset.jar

5.util

◆ StringUtil类中增加文件下载方法：

Java代码 

```java
StringUtil.sendFile(request, response, record    
                            .getString("filename"), record    
                            .getBlob("filecontent"));    
StringUtil.sendFile(request, response, file);   
```

◆ 支持数字向BigDecimal转换、数字数组向BigDecimal数组转换功能