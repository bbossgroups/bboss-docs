### bboss persistent 1.0.2中方便地实现大字段(clob,blob)的处理

bboss项目下载列表 在sourceforge访问地址为：

https://sourceforge.net/project/showfiles.php?group_id=238653

bboss-persistent 1.0.2 下载地址

http://sourceforge.net/project/platformdownload.php?group_id=238653


bboss persistent框架提供了对大字段处理的封装，本文详细介绍具体的用法：

1.获取与blob和clob字段相关的流对象

2.直接获取操作lob字段包括增删改查4种操作，其中有通用的操作（适用于所有的数据库），针对oracle的操作方法。

下面详细介绍

获取与blob和clob字段相关的流对象

​    package com.frameworkset.common;

import java.sql.SQLException;

import com.frameworkset.common.poolman.DBUtil;

import com.frameworkset.common.poolman.PreparedDBUtil;

import com.frameworkset.common.poolman.handle.ValueExchange;

/**

 \* 测试如下方法是否正常工作

 \* dbUtil.getBinaryStream

 \* dbUtil.getUnicodeStream(

 \* dbUtil.getAsciiStream(

 \* getCharacterStream(

 \* < p >Title :  TestStream.java</ p >

 *

 \* < p >Description :  </ p >

 *

 \* < p >Copyright :  Copyright (c) 2007</ p >

 \* @Date Dec 5, 2008 3:12:22 PM

 \* @author biaoping.yin

 \* @version 1.0.1

 */

public class TestStream {

​        public static void testClob()

​        {

​                DBUtil dbUtil = new DBUtil();

​                try {

​                        dbUtil.executeSelect("select clobname from test where id='0'");

​                        System.out.println("BinaryStream:" + ValueExchange.getStringFromStream(dbUtil.getBinaryStream(0, 0)));

​                        System.out.println("UnicodeStream:" + ValueExchange.getStringFromStream(dbUtil.getUnicodeStream(0, 0)));

​                        System.out.println("AsciiStream:" + ValueExchange.getStringFromStream(dbUtil.getAsciiStream(0, 0)));

​                        System.out.println("Reader:" + ValueExchange.getStringFromReader(dbUtil.getCharacterStream(0, 0)));                       

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​        }

​    public static void testBlob()

​    {

​            PreparedDBUtil pdb = new PreparedDBUtil();         

​            DBUtil dbUtil = new DBUtil();

​            try {

​                    pdb.preparedUpdate("update test set blobname = ? where id = '0'");

​                    pdb.setBlob(1, "blob".getBytes());

​                    pdb.executePrepared();

​                    dbUtil.executeSelect("select blobname from test where id='0'");

​                    System.out.println("BinaryStream:" + ValueExchange.getStringFromStream(dbUtil.getBinaryStream(0, 0)));

​                    System.out.println("UnicodeStream:" + ValueExchange.getStringFromStream(dbUtil.getUnicodeStream(0, 0)));

​                    System.out.println("AsciiStream:" + ValueExchange.getStringFromStream(dbUtil.getAsciiStream(0, 0)));

​                    /**

*blob类型的字段不能直接转换为Reader对象

​                    System.out.println("Reader:" + ValueExchange.getStringFromReader(dbUtil.getCharacterStream(0, 0)));

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​    }

​        public static void main(String[] args)
​        {
​                testBlob();
​        }

}

 

直接获取操作lob字段包括增删改查4种操作，其中有通用的操作（适用于所有的数据库）和针对oracle的操作方法


/*

 \*  Copyright 2008 biaoping.yin

 *

 \*  Licensed under the Apache License, Version 2.0 (the "License");

 \*  you may not use this file except in compliance with the License.

 \*  You may obtain a copy of the License at

 *

 \*      http://www.apache.org/licenses/LICENSE-2.0

 *

 \*  Unless required by applicable law or agreed to in writing, software

 \*  distributed under the License is distributed on an "AS IS" BASIS,

 \*  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.

 \*  See the License for the specific language governing permissions and

 \*  limitations under the License.

 */

package com.frameworkset.common;

import javax.transaction.RollbackException;

import oracle.sql.BLOB;

import oracle.sql.CLOB;

import com.frameworkset.common.poolman.DBUtil;

import com.frameworkset.common.poolman.PreparedDBUtil;

import com.frameworkset.orm.transaction.TransactionManager;

/**

 *

 \* < p >Title: TestLob.java</ p >

 *

 \* < p >

 \* Description:运行本类时必须下载 bboss persistent 框架的version 1.0.2 下载的地址如下：

 \* https://sourceforge.net/project/showfiles.php?group_id=238653

*</ p >

*

*< p >Copyright :  Copyright (c) 2007</ p >

*@Date Dec 24, 2008 10 : 05 : 04 PM

*@author biaoping.yin

*@version 1.0

*/

public class TestLob {

/**

*第一种插入blob字段的方法，通用的处理模式，适用于所有类型的数据库

*/

public static void testBlobWrite(String id)

{

​        PreparedDBUtil dbUtil = new PreparedDBUtil();

​        try {

​                dbUtil.preparedInsert( "insert into test(id,blobname) values(?,?)");               

​            dbUtil.setString(1, id);

​            dbUtil.setBlob(2, new java.io.File("d:/内容管理系统升级步骤.txt"));//直接将文件存储到大字段中

​                     dbUtil.executePrepared();

​    } catch (Exception e) {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​    }

​    finally

​    {

​    }

}

 /**

*针对oracle Blob字段的插入操作

/
    public static void testBigBlobWrite()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

TransactionManager tm = new TransactionManager();

try {

​        //启动事务

​        tm.begin();

​        //先插入一条记录,blob字段初始化为empty_lob

​        dbUtil.preparedInsert( "insert into test(id,blobname) values(?,?)");

​        String id = DBUtil.getNextStringPrimaryKey("test");

​        dbUtil.setString(1, id);

​        dbUtil.setBlob(2,BLOB.empty_lob());//先设置空的blob字段               

​                    dbUtil.executePrepared();

​                      //查找刚才的插入的记录，修改blob字段的值为一个文件

​                    dbUtil = new PreparedDBUtil();

​                    dbUtil.preparedSelect("select blobname from test where id = ?");

​                    dbUtil.setString(1, id);

​                    dbUtil.executePrepared();

​                    BLOB blob = (BLOB)dbUtil.getBlob(0, "blobname");

​                    if(blob != null)

​                    {             

​                            DBUtil.updateBLOB(blob, new java.io.File("d:/wls_docs92.chm"));

​                    }

​                    tm.commit();

​         } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​                    try {

​                            tm.rollback();

​                    } catch (RollbackException e1) {

​                            // TODO Auto-generated catch block

​                            e1.printStackTrace();

​                    }

​            }

​            finally

​            {

​                    tm = null;

​                    dbUtil = null;

​            }

​    }

 /**

*第一种update blob字段的方法，通用的处理模式，适用于所有类型的数据库

*/

public static void testBlobUpdate()

{

​        PreparedDBUtil dbUtil = new PreparedDBUtil();

​        try {

​                dbUtil.preparedUpdate("update test set blobname= ? where id= ?");

​                dbUtil.setString(2, "12");

​                dbUtil.setBlob(1, new java.io.File("d:/readme.txt"));//直接将文件存储到大字段中

​                dbUtil.executePrepared();

​        } catch (Exception e) {

​                // TODO Auto-generated catch block

​                e.printStackTrace();

​        }

​        finally

​        {

​                dbUtil = null;

​        }

}

/**

*针对oracle Blob字段的update操作

*/

public static void testBigBlobUpdate()

{

​    PreparedDBUtil dbUtil = new PreparedDBUtil();

​    TransactionManager tm = new TransactionManager();

​    try {

​            //启动事务

​                    tm.begin();

​                    //先查询id=12一条记录,以乐观锁的方式来处理这条记录，当有其他程序正在修改id=12的记录时，将抛出异常直接终止改修改操作

​                    dbUtil.preparedSelect( "select id,blobname from test where id = ? for update nowait");

​                    String id = "12";

​                    dbUtil.setString(1, id);      

​                    dbUtil.executePrepared();

​                    BLOB blob = (BLOB)dbUtil.getBlob(0, "blobname");

​                    if(blob != null)

​                    {             

​                            //更新blob字段的值

​                            DBUtil.updateBLOB(blob, new java.io.File("d:/readme.txt"));

​                    }

​                    //提交事务

​                    tm.commit();

​         } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​                    try {

​                            //回滚事务

​                            tm.rollback();

​                    } catch (RollbackException e1) {

​                            // TODO Auto-generated catch block

​                            e1.printStackTrace();

​                    }

​            }

​            finally

​            {

​                    tm = null;

​                    dbUtil = null;

​            }


​    }

   /**

*大字段的读取

*/

​    public static void testBlobRead()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

try {

​        //查询大字段内容并且将大字段存放到文件中

​        dbUtil.preparedSelect( "select id,blobname from test");

​        dbUtil.executePrepared();

​        for(int i = 0; i < dbUtil.size(); i ++)

​        {

​                dbUtil.getFile(i, "blobname", new java.io.File("d:/wls_docs921.chm"));//将blob字段的值转换为文件

//                                Blob blob = dbUtil.getBlob(i, "blobname");//获取blob字段的值到blob变量中。

​                        }
​                       

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​            finally

​            {

​            }

​    }

/**

*clob字段的写入

*/

​    public static void testClobWrite(String id)

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

try {

​        dbUtil.preparedInsert( "insert into test(id,clobname) values(?,?)");

//                        dbUtil.setString(1, DBUtil.getNextStringPrimaryKey("test"));

​                        dbUtil.setString(1, id);

​                        dbUtil.setClob(2,"clobvalue");//直接将字符串存储到clob字段中，也可以直接使用

dbUtil.setString(2,"clobvalue")来设置clob字段的值

​                        dbUtil.executePrepared();                       

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​            finally

​            {

​                }

  }
       

​    /**

*针对oracle Clob字段的插入操作

*/

​    public static void testBigClobWrite()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

TransactionManager tm = new TransactionManager();

try {

​        //启动事务

​        tm.begin();

​        //先插入一条记录,blob字段初始化为empty_lob

​        dbUtil.preparedInsert( "insert into test(id,clobname) values(?,?)");

​        String id = DBUtil.getNextStringPrimaryKey("test");

​        dbUtil.setString(1, id);

​        dbUtil.setClob(2,CLOB.empty_lob());//先设置空的blob字段                  

​                    dbUtil.executePrepared();

​                           //查找刚才的插入的记录，修改blob字段的值为一个文件

​                    dbUtil = new PreparedDBUtil();

​                    dbUtil.preparedSelect("select clobname from test where id = ?");

​                    dbUtil.setString(1, id);

​                    dbUtil.executePrepared();

​                     CLOB clob = (CLOB)dbUtil.getClob(0, "clobname");

​                    if(clob != null)

​                    {                        

​                            DBUtil.updateCLOB(clob, new java.io.File("d:\\route.txt"));

​                    }

​                   tm.commit();                       

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​                    try {

​                            tm.rollback();

​                    } catch (RollbackException e1) {

​                            // TODO Auto-generated catch block

​                            e1.printStackTrace();

​                    }

​            }

​            finally

​            {

​                    tm = null;

​                    dbUtil = null;

​            }

​    }

   /**

*clob字段的更新

*/

​    public static void testClobUpdate()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

try {

​        dbUtil.preparedUpdate( "update test set clobname=? where id='12'");                       

​                    dbUtil.setClob(1,"clobvalue11111");//直接将字符串存储到clob字段中，也可以直接使用

dbUtil.setString(2,"clobvalue")来设置clob字段的值

​                    dbUtil.executePrepared();

​            } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​            finally

​            {

​            }

​    }

/**

*针对oracle Clob字段的修改操作

*/

​    public static void testBigClobUpdate()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

TransactionManager tm = new TransactionManager();

try {

​        //启动事务

​        tm.begin();

​        //先插入一条记录,blob字段初始化为empty_lob

​        dbUtil.preparedSelect( "select id,clobname from test where id='12' for update nowait");

​      dbUtil.executePrepared();

​                CLOB clob = (CLOB)dbUtil.getClob(0, "clobname");

​                    if(clob != null)

​                    {   

​                            DBUtil.updateCLOB(clob, new java.io.File("d:\\readme.txt"));

​                    }

​                    tm.commit();

​         } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​                    try {

​                            tm.rollback();

​                    } catch (RollbackException e1) {

​                            // TODO Auto-generated catch block

​                            e1.printStackTrace();

​                    }

​            }

​            finally

​            {

​                    tm = null;

​                    dbUtil = null;

​            }


​    }

  /**

*clob字段的读取

*/
    public static void testClobRead()

​    {

PreparedDBUtil dbUtil = new PreparedDBUtil();

try {

​        //查询大字段内容并且将大字段存放到文件中

​        dbUtil.preparedSelect( "select id,clobname from test");

​        dbUtil.executePrepared();

​        for(int i = 0; i < dbUtil.size(); i ++)

​        {
​                dbUtil.getFile(i, "clobname", new java.io.File("d:/route" + i + ".txt")); //读取clob字段到文件中

//                                String clobvalue = dbUtil.getString(i, "clobname");//获取clob字段到字符串变量中

//                                Clob clob = dbUtil.getClob(i, "clobname");//获取clob字段值到clob类型变量中

​                        }

​        } catch (Exception e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

​            finally

​            {

​                    dbUtil = null;

​            }

​    }

​    public static void main(String[] args)

​    {

​            System.out.print("start.........");

​            DBUtil.debugMemory();

​            for(int i = 0; i < 1; i ++)

​            {

//                        String id = DBUtil.getNextStringPrimaryKey("test");

​                        String id = "12";

//                        testBlobWrite(id);

​                        testClobWrite( id);

//                        testBigBlobWrite();

//                        testClobWrite();

//                        testBigClobWrite();                       

​            }

//                TestLob.testClobUpdate();

​                testBigClobUpdate();

//                TestLob.testBlobUpdate();

//                TestLob.testBigBlobUpdate();

//                testBlobWrite();

//                testBigBlobWrite();

//                DBUtil.debugMemory();

//                testBlobRead();

//                testClobRead();

​                System.out.println("end.........");

​                DBUtil.debugMemory();

​        }     

}

运行上述的测试示例，需要在数据库中执行以下脚本，脚本已经在oracle 10上测试通过：
 

drop table test;

CREATE TABLE TEST

(

  NAME  VARCHAR2(4000),

  ID    VARCHAR2(100)

);

drop table test1;

CREATE TABLE TEST1

(

  A  VARCHAR2(12)

);

DROP SEQUENCE SEQ_TEST;

CREATE SEQUENCE SEQ_TEST

  START WITH 160

  MAXVALUE 999999999999999999999999999

  MINVALUE 0

  NOCYCLE

  CACHE 20

  NOORDER;

drop table TABLEINFO cascade constraints

/

CREATE TABLE TABLEINFO

(

  TABLE_NAME          VARCHAR2(255)        NOT NULL,

  TABLE_ID_NAME       VARCHAR2(255),

  TABLE_ID_INCREMENT  NUMBER(5)                 DEFAULT 1,

  TABLE_ID_VALUE      NUMBER(20)                DEFAULT 0,

  TABLE_ID_GENERATOR  VARCHAR2(255),

  TABLE_ID_TYPE       VARCHAR2(255),

  TABLE_ID_PREFIX     VARCHAR2(255)

);

COMMENT ON TABLE TABLEINFO IS '表信息维护对象';

COMMENT ON COLUMN TABLEINFO.TABLE_NAME IS '表名称';


COMMENT ON COLUMN TABLEINFO.TABLE_ID_NAME IS '表的主键名称';

COMMENT ON COLUMN TABLEINFO.TABLE_ID_INCREMENT IS '表的主键递增量
缺省为1';

COMMENT ON COLUMN TABLEINFO.TABLE_ID_VALUE IS '主键当前值：缺省为0';

COMMENT ON COLUMN TABLEINFO.TABLE_ID_GENERATOR IS '自定义表主键生成机制
必需从com.frameworkset.common.poolman.sql.PrimaryKey集成';

由于脚本太长，请下载附件：

[TestTransaction_sql.zip](http://dl.iteye.com/topics/download/d2fa6a61-953b-3e78-ba6f-744f8eee539d) (960 Bytes)

