### bboss持久层行处理器使用介绍

**1.概述**

行处理主要用来提供灵活高效的查询结果处理方法，下面着重介绍一下三种主要行处理器:空行处理器、返回值行处理器以及字段行处理器。

1.1 空行处理器-灵活度最高的行处理器，结果集全部自行封装，框架干预少

com.frameworkset.common.poolman.handle.NullRowHandler

空行处理器适用于

1.2.返回值行处理器-框架负责返回数据集，记录字段转换为值对象自行处理

com.frameworkset.common.poolman.handle.RowHandler

1.3.字段行处理器-对单个字段进行行级处理，并将处理值发挥给调用程序，字段行处理器主要用来处理blob和clob大字段类型的单字段查询功能，消除不同的数据库对大字段处理的差异性

com.frameworkset.common.poolman.handle.FieldRowHandler

**2.使用方法**

**2.1 空行处理器**

map方式

Java代码

```java
public Map<String,String> getUserDeskMapMenus(DeskTopMenuBean bean)  
    throws Exception {  
        String sql = "SELECT menupath,subsystem,userid FROM TD_SM_DESKMENU WHERE userid=#[userid] and subsystem=#[subsystem]";  
        final Map<String,String> datas = new HashMap<String,String>();  
        SQLExecutor.queryBeanWithDBNameByNullRowHandler(new NullRowHandler(){  
  
            @Override  
            public void handleRow(Record origine) throws Exception  
            {  
                datas.put(origine.getString("menupath"), origine.getString("subsystem"));  
                  
                  
            }}, dbname,sql , bean);  
        return datas;  
    }  
```

list方式

Java代码

```java
final List<ListBean> lbs = new ArrayList<ListBean>();  
SQLExecutor.queryWithDBNameByNullRowHandler(new NullRowHandler(){  
  
            @Override  
            public void handleRow(Record record) throws Exception {  
                  
                ListBean lb = new ListBean();  
                lb.setId(record.getInt("id"));  
                lb.setFieldName(record.getString("fieldName"));  
                lbs.add(lb);  
            }}, "mysql", "select * from LISTBEAN where id>?", 80);  
```

直接操作ResultSet的空行处理器使用实例：

Java代码 

```java
SQLExecutor.queryWithDBNameByNullRowHandler(  
                    new ResultSetNullRowHandler() {  
  
                        @Override  
                        public void handleRow(ResultSet rs) throws Exception {  
Person person = (Person)super.buildValueObject(  rs,  
                Person.class);//进行o/r mapping绑定  
                            System.out.println("name = " + rs.getString("name"));  
                            System.out.println("id = " + rs.getInt("id"));  
  
                        }  
                    }, "test", "select * from person");  
```

**2.2 返回值行处理器**

Java代码

```java
String sql = "select * from LISTBEAN where ID>?";  
        List<ListBean> beans = (List<ListBean>) SQLExecutor.queryListByRowHandler(new RowHandler<ListBean>(){  
  
            @Override  
            public void handleRow(ListBean lb, Record record)  
                    throws Exception {  
                System.out.println("queryListByRowHandler test Result**:"+record.getString("fieldName"));  
                  
                lb.setId(record.getInt("id"));  
                lb.setFieldName(record.getString("fieldName"));  
                  
            }}, ListBean.class, sql, 80);  
```

**2.3 字段行处理器**

字段行处理器实现从blob/clob中获取单个字段文件对象的处理,其他类似类型数据也可以使用FieldRowHandler，使用示例如下：

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
                                            "select filename,filecontent from CLOBFILE where fileid=?",  
                                            fileid);  
        }  
        catch (Exception e)  
        {  
            throw e;  
        }  
    }  
```

几种常用的使用方法就介绍完了，同时我们还可以结合行处理器完成分页查询，这里就不多说，更进一步的内容请关注博客动态。