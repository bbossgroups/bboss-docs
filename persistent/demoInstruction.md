### bboss持久层demo使用说明

持久层案例可以用svn客户端下载eclipse工程，导入eclise即可
https://github.com/bbossgroups/bbossgroups-3.5/tree/master/bestpractice/persistent

环境准备，建好数据库，然后再数据库上执行以下脚本(不同的数据库需要做些微调)：

Java代码

```java
drop table TABLEINFO cascade constraints;  
  
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
必需从  
com.frameworkset.common.poolman.sql.PrimaryKey集成';  
  
COMMENT ON COLUMN TABLEINFO.TABLE_ID_TYPE IS '主键类型（string,int）';  
  
COMMENT ON COLUMN TABLEINFO.TABLE_ID_PREFIX IS '类型为string的主键前缀，可指定可不指定,缺省值为""';  
  
  
CREATE UNIQUE INDEX PK_TABLEINFO0 ON TABLEINFO(TABLE_NAME);  
  
  
  
ALTER TABLE TABLEINFO ADD   CONSTRAINT PK_TABLEINFO0 PRIMARY KEY (TABLE_NAME);  
  
CREATE  
    TABLE LISTBEAN  
    (  
        ID INTEGER NOT NULL,  
        FIELDNAME VARCHAR(300),  
        FIELDLABLE VARCHAR(300),  
        FIELDTYPE VARCHAR(300),  
        SORTORDER VARCHAR(300),  
        ISPRIMARYKEY INTEGER,  
        REQUIRED INTEGER,  
        FIELDLENGTH INTEGER,  
        ISVALIDATED INTEGER,  
        CONSTRAINT LISTBEANKEY PRIMARY KEY (ID)  
    );  
insert into TABLEINFO (TABLE_NAME, TABLE_ID_NAME, TABLE_ID_INCREMENT, TABLE_ID_VALUE, TABLE_ID_GENERATOR, TABLE_ID_TYPE, TABLE_ID_PREFIX) values ('LISTBEAN', 'id', 1, 0, null, 'int', null);  
commit;  
```

修改src/poolman.xml中的数据库驱动、链接地址、账号和口令，即可运行工程下的测试用例：
src/com/frameworkset/sqlexecutor/ConfigSQLExecutorTest.java

poolman.xml中需要修改的属性,只要将其中的值改为特定数据库配置即可：

Xml代码 

```xml
<jndiName>jdbc/derby-ds</jndiName>  
    <driver>org.apache.derby.jdbc.EmbeddedDriver</driver>  
     <url>jdbc:derby:D:/workspace/bbossgroups-3.2/bboss-mvc/database/cimdb</url>   
    <username></username>     
    <password></password>   
    <validationQuery></validationQuery>  
```

更多持久层资料可以浏览：
http://yin-bp.iteye.com/category/55607