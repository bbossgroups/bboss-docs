### bbossgroups持久层框架查询返回Map对象和Map列表对象api

bbossgroups持久层框架查询返回Map对象和Map列表对象api：

Java代码

```java
@Test  
    public void queryListMap() throws SQLException  
    {  
        String sql = "select * from LISTBEAN ";  
        List<HashMap> dbBeans  =  SQLExecutor.queryListWithDBName(HashMap.class, "mysql", sql);  
        System.out.println(dbBeans);  
    }  
      
    @Test  
    public void queryMap() throws SQLException  
    {  
        String sql = "select * from LISTBEAN ";  
        Map dbBeans  =  SQLExecutor.queryObject(HashMap.class, sql);  
        System.out.println(dbBeans);  
    }  
```

map对象中存放的key为查询列的大写名称，value为字段的的值。