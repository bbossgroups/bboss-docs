### bboss 持久层框架直接返回基础数据类型和基础数据类型List集合介绍

本文介绍如何通过bboss 持久层框架相关查询api直接返回基础数据类型和基础数据类型List集合功能，基础数据类型包括以下几种：

Java代码 

```java
{String.class,  
        int.class ,Integer.class,  
        long.class,Long.class,  
        java.sql.Timestamp.class,java.sql.Date.class,java.util.Date.class,  
        boolean.class ,Boolean.class,  
        BigFile.class,//bboss的大文件对象  
        float.class ,Float.class,  
        short.class ,Short.class,  
        double.class,Double.class,  
        char.class ,Character.class,  
          
        byte.class ,Byte.class,  
        BigDecimal.class};  
```

下面就是具体的使用方法，以[SQLExecutor](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/sqlexecutor/SimpleApiTest.java)组件为例来说明（[ConfigSQLExecutor](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-persistent/test/com/frameworkset/sqlexecutor/ConfigSQLExecutorTest.java)类似，这里不做过多说明）：

Java代码

```java
@Test  
    public void dynamicqueryBean() throws SQLException  
    {  
         ListBean bean = new ListBean();  
            bean.setFieldName("阿斯顿飞");  
         //<property name="refresh_interval" value="10000"/>  
         List<ListBean> result = SQLExecutor.queryListBean(ListBean.class, "select *  from LISTBEAN", bean);  
         System.out.println(result.size());  
          bean.setFieldName("");  
         result = (List<ListBean>) SQLExecutor.queryListBean(ListBean.class,"select *  from LISTBEAN", bean);  
         System.out.println(result.size());  
           
         bean.setFieldName(null);  
         result = (List<ListBean>) SQLExecutor.queryListBean(ListBean.class,"select *  from LISTBEAN", bean);  
           
           
         System.out.println(result.size());  
           
         List<String> result_string =  SQLExecutor.queryListBean(String.class,"select *  from LISTBEAN", bean);  
           
           
         System.out.println(result_string.size());  
           
         List<Integer> result_int =  SQLExecutor.queryListBean(Integer.class,"select *  from LISTBEAN", bean);  
           
           
         System.out.println(result_int.size());  
           
    }  
      
    @Test  
    public void dynamicquery() throws SQLException  
    {  
           
        List<ListBean> result =  SQLExecutor.queryList(ListBean.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result.size());  
           
         List<String> result_string =  SQLExecutor.queryList(String.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result_string.size());  
           
         List<Integer> result_int =  SQLExecutor.queryList(Integer.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result_int.size());  
           
    }  
      
    @Test  
    public void dynamicqueryObject() throws SQLException  
    {  
           
        ListBean result =  SQLExecutor.queryObject(ListBean.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result.getId());  
           
         String result_string =  SQLExecutor.queryObject(String.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result_string);  
           
         int result_int =  SQLExecutor.queryObject(int.class,"select id  from LISTBEAN");  
           
           
         System.out.println(result_int);  
           
    }  
```

bboss的开发环境参考文档：

[搭建自己的bbossmvc eclipse开发工程，编写第一个实例](http://yin-bp.iteye.com/blog/1026261)