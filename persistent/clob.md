### bboss持久层操作Clob和Blob字段示例

bboss持久层操作Clob和Blob非常方便，基于bboss我们可以非常方便插入、修改和读取clob和blob字段，我们直接看示例：

Java代码

```java
package com.frameworkset.common;  
  
import java.io.File;  
import java.io.InputStream;  
import java.sql.SQLException;  
import java.util.HashMap;  
import java.util.List;  
import java.util.UUID;  
  
import javax.servlet.http.HttpServletRequest;  
import javax.servlet.http.HttpServletResponse;  
import javax.transaction.RollbackException;  
  
import org.junit.Test;  
  
import com.frameworkset.common.poolman.CallableDBUtil;  
import com.frameworkset.common.poolman.DBUtil;  
import com.frameworkset.common.poolman.PreparedDBUtil;  
import com.frameworkset.common.poolman.Record;  
import com.frameworkset.common.poolman.SQLExecutor;  
import com.frameworkset.common.poolman.SQLParams;  
import com.frameworkset.common.poolman.handle.FieldRowHandler;  
import com.frameworkset.common.poolman.handle.NullRowHandler;  
import com.frameworkset.orm.annotation.Column;  
import com.frameworkset.orm.transaction.TransactionManager;  
import com.frameworkset.util.StringUtil;  
/** 
 *  
 * <p>Title: TestLob.java</p> 
 * 
 * <p>Description:  
 * CREATE 
    TABLE TEST 
    ( 
        BLOBNAME BLOB, 
        CLOBNAME CLOB, 
        ID VARCHAR(100) 
    ) 
 * </p> 
 * 
 * <p>Copyright: Copyright (c) 2007</p> 
 * @Date 2011-12-27 下午2:56:03 
 * @author biaoping.yin 
 * @version 1.0 
 */  
public class TestLob {  
      
    public static class LobBean  
    {  
        private String id;        
        @Column(type="blob")//指示属性的值按blob类型写入或者读取  
        private String blobname;  
        @Column(type="clob")//指示属性的值按clob类型写入或者读取  
        private String clobname;   
  
        @Column(name="name_")//指示属性名称与表字段名称映射关系，name属性对应于表中的name_字段  
        private String name;   
        @Column(dataformat="yyyy-mm-dd")//指示日期类型属性值的存储和读取转换日期格式  
        private String regdate;   
          
//        。。。。。。  
  
  
    }  
    @Test  
    public void testNewSQLParamInsert() throws Exception  
    {  
        SQLParams params = new SQLParams();  
        params.addSQLParam("id", "1", SQLParams.STRING);  
        // ID,HOST_ID,PLUGIN_ID,CATEGORY_ID,NAME,DESCRIPTION,DATASOURCE_NAME,DRIVER,JDBC_URL,USERNAME,PASSWORD,VALIDATION_QUERY  
        params.addSQLParam("blobname", "abcdblob",  
                SQLParams.BLOB);  
        params.addSQLParam("clobname", "abcdclob",  
                SQLParams.CLOB);  
        SQLExecutor.insertBean("insert into test(id,blobname,clobname) values(#[id],#[blobname],#[clobname])", params);  
    }  
    @Test  
    public void testNewBeanInsert() throws Exception  
    {  
        LobBean bean = new LobBean();  
        bean.id = "2";  
        bean.blobname = "abcdblob";  
        bean.clobname = "abcdclob";  
        SQLExecutor.insertBean("insert into test(id,blobname,clobname) values(#[id],#[blobname],#[clobname])", bean);  
    }  
      
    @Test  
    public void testNewOrMappingQuery() throws Exception  
    {  
//      SQLParams params = new SQLParams();  
//      params.addSQLParam("id", "1", SQLParams.STRING);  
//      // ID,HOST_ID,PLUGIN_ID,CATEGORY_ID,NAME,DESCRIPTION,DATASOURCE_NAME,DRIVER,JDBC_URL,USERNAME,PASSWORD,VALIDATION_QUERY  
//      params.addSQLParam("blobname", "abcdblob",  
//              SQLParams.BLOB);  
//      params.addSQLParam("clobname", "abcdclob",  
//              SQLParams.CLOB);  
        LobBean bean = SQLExecutor.queryObject(LobBean.class,"select * from test");  
        System.out.println();  
    }  
      
      
    @Test  
    public void testNewOrMappingsQuery() throws Exception  
    {  
//      SQLParams params = new SQLParams();  
//      params.addSQLParam("id", "1", SQLParams.STRING);  
//      // ID,HOST_ID,PLUGIN_ID,CATEGORY_ID,NAME,DESCRIPTION,DATASOURCE_NAME,DRIVER,JDBC_URL,USERNAME,PASSWORD,VALIDATION_QUERY  
//      params.addSQLParam("blobname", "abcdblob",  
//              SQLParams.BLOB);  
//      params.addSQLParam("clobname", "abcdclob",  
//              SQLParams.CLOB);  
        List<LobBean> bean = SQLExecutor.queryList(LobBean.class,"select * from test");  
        System.out.println();  
    }  
      
      
      
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
    public @Test void uploadClobFile() throws Exception  
    {  
        File file = new File("D:\\bbossgroups-3.5.1\\bboss-taglib\\readme.txt");  
          
        String sql = "";  
        try {  
            sql = "INSERT INTO CLOBFILE (FILENAME,FILECONTENT,fileid,FILESIZE) VALUES(#[filename],#[FILECONTENT],#[FILEID],#[FILESIZE])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParam("filename", file.getName(), SQLParams.STRING);  
            sqlparams.addSQLParamWithCharset("FILECONTENT", file,SQLParams.CLOBFILE,"GBK");  
            sqlparams.addSQLParam("FILEID", UUID.randomUUID().toString(),SQLParams.STRING);  
            sqlparams.addSQLParam("FILESIZE", file.length(),SQLParams.LONG);  
            SQLExecutor.insertBean(sql, sqlparams);           
              
        } catch (Exception ex) {  
          
              
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        }   
          
          
    }  
      
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
    public @Test void updateClobFile() throws Exception  
    {  
        File file = new File("D:\\bbossgroups-3.5.1\\bboss-taglib\\readme.txt");  
        String sql = "";  
        TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin();  
            SQLExecutor.queryField("select 1 as ss from CLOBFILE where fieldid=? for update nowait","11");//锁定记录  
            sql = "update CLOBFILE set FILECONTENT=#[FILECONTENT]) where fileid = #[FILEID])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParamWithCharset("FILECONTENT", file,SQLParams.CLOBFILE,"GBK");  
            sqlparams.addSQLParam("FILEID", "11",SQLParams.STRING);  
            SQLExecutor.updateBean(sql, sqlparams);           
            tm.commit();  
        } catch (Exception ex) {  
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        }   
        finally  
        {  
            tm.release();  
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
  
      
    public void deletefiles() throws Exception  
    {  
  
        SQLExecutor.delete("delete from filetable ");     
        SQLExecutor.delete("delete from CLOBFILE ");      
    }  
  
      
    public List<HashMap> queryfiles() throws Exception  
    {  
  
        return SQLExecutor.queryList(HashMap.class, "select FILENAME,fileid,FILESIZE from filetable");  
          
    }  
      
    public List<HashMap> queryclobfiles()throws Exception  
    {  
  
        return SQLExecutor.queryList(HashMap.class, "select FILENAME,fileid,FILESIZE from CLOBFILE");  
          
    }  
  
      
    public void downloadFileFromBlob(String fileid, final HttpServletRequest request,  
            final HttpServletResponse response) throws Exception  
    {  
        try  
        {  
            SQLExecutor.queryByNullRowHandler(new NullRowHandler() {  
                  
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
      
//    
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
```

示例中包含了大数据的增加、删除和修改操作，补充说明一下修改操作：

Java代码

```java
TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin();  
            SQLExecutor.queryField("select 1 as ss from CLOBFILE where fieldid=? for update nowait","11");//锁定记录  
            sql = "update CLOBFILE set FILECONTENT=#[FILECONTENT]) where fileid = #[FILEID])";  
            SQLParams sqlparams = new SQLParams();  
            sqlparams.addSQLParamWithCharset("FILECONTENT", file,SQLParams.CLOBFILE,"GBK");  
            sqlparams.addSQLParam("FILEID", "11",SQLParams.STRING);  
            SQLExecutor.updateBean(sql, sqlparams);           
            tm.commit();//提交修改并释放记录锁  
        } catch (Exception ex) {  
            throw new Exception("上传附件关联临控指令布控信息附件失败：" + ex);  
        }   
        finally  
        {  
            tm.release();//如果有异常，回滚事务，同时释放记录锁  
        }  
```

修改大字段一般在一个事务上下文中先锁定需要修改的记录（采用nowait非阻塞方式锁定），修改完毕后commit事务的同时释放记录的锁，如果有异常，通过finally块中的release方法回滚事务并释放记录锁。