### bboss持久层分页接口使用示例

[bboss持久层快速上手](http://yin-bp.iteye.com/blog/2318974)

bboss持久层分页接口比较有特色，提供了四种Style的分页接口：

第一种Style 根据sql语句直接分页，这种风格是bboss 3.6.0及之前版本一直沿用的接口

第二种Style 根据sql语句和外部传入的总记录数进行分页，这是bboss 3.6.1及之后版本提供的接口

第三种Style 根据sql语句和外部传入的总记录数sql语句进行分页，这是bboss 3.6.1及之后版本提供的接口

第四种Style 使用数据库row_number() over()分析函数结合排序条件实现数据库物理分页

前三种style的支持oracle，mysql，maradb，sqlite，postgres四个主流数据库的高效物理分页，其他数据采用游标机制实现分页（效率相对较低);第4种风格支持oracle，mysql，maradb，sqlite，postgres，derby，ms sql server 2008，db2数据库的高效物理分页（其他类型数据库请采用前面三种风格进行分页）

还有一种不返回总记录数的高效分页查询方法，参考文档：[bboss持久层More分页查询API使用介绍](http://yin-bp.iteye.com/blog/1960559)

我们根据查询参数的传入方式，分别下面举例介绍四种Style。

1.准备工作-编写一个sql语句配置文件

com/frameworkset/sqlexecutor/purchaseApply.xml

内容如下，用来演示四种Style

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<properties>  
    <property name="queryMaterialList">  
    <![CDATA[ 
        select * from td_app_bom where id=#[id] 
        ]]>  
    </property>  
      
    <property name="queryCountMaterialList">  
    <![CDATA[ 
        select count(1) from td_app_bom where id=#[id] 
        ]]>  
    </property>  
      
      
    <property name="queryMaterialListbindParam">  
    <![CDATA[ 
        select * from td_app_bom where id=? 
        ]]>  
    </property>  
      
    <property name="queryCountMaterialListbindParam">  
    <![CDATA[ 
        select count(1) from td_app_bom where id=? 
        ]]>  
    </property>  
  
  
    <property name="testsqlinfo"><![CDATA[select * from TD_APP_BOM]]></property>  
  
    <property name="ROW_NUMBERquery"><![CDATA[select * from TD_APP_BOM where 1=1  
            #if($bm && !$bm.equals("")) 
                and bm = #[bm] 
            #end 
            #if($app_name_en && !$app_name_en.equals("")) 
                and app_name_en like #[app_name_en] 
            #end 
            #if($app_name && !$app_name.equals("")) 
                and app_name like #[app_name] 
            #end 
            #if($soft_level && !$soft_level.equals("")) 
                and soft_level=#[soft_level] 
            #end 
            #if($state && !$state.equals("")) 
                and state=#[state] 
            #end 
            #if($rd_type && !$rd_type.equals("")) 
                and rd_type=#[rd_type] 
            #end ]]></property>  
  
    <property name="ROW_NUMBERquery_orderby"><![CDATA[ 
            #if($sortKey && !$sortKey.equals("")) 
                order by $sortKey  
                #if($sortDESC ) 
                    desc 
                #else 
                    asc 
                #end     
            #else 
                order by bm  
            #end]]></property>  
</properties>  
```

说明：

queryMaterialList为分页sql

queryCountMaterialList为查总记录数sql

2.分页查询方法示例代码

Java代码

```java
public class ApplyService {  
  
    private com.frameworkset.common.poolman.ConfigSQLExecutor executor = new ConfigSQLExecutor("com/frameworkset/sqlexecutor/purchaseApply.xml");  
      
    /*******************************以bean方式传递查询条件开始*******************************/  
    public ListInfo queryMaterailListInfoFirstStyleBean(int offset, int pagesize ,PurchaseApplyCondition condition) throws Exception {  
          
        //执行分页查询，queryMaterialList对应分页查询语句，  
        //根据sql语句在分页方法内部执行总记录数查询操作，这种风格使用简单，效率相对较低  
        //condition参数保存了查询条件  
        return executor.queryListInfoBean(HashMap.class, "queryMaterialList", offset, pagesize,condition);  
    }  
      
    public ListInfo queryMaterailListInfoSecondStyleBean(int offset, int pagesize ,PurchaseApplyCondition condition) throws Exception {  
        //执行总记录查询并存入totalSize变量中，queryCountMaterialList对应一个优化后的总记录查询语句  
        //condition参数保存了查询条件  
        long totalSize = executor.queryObjectBean(long.class, "queryCountMaterialList", condition);  
        //执行总记分页查询，queryMaterialList对应分页查询语句，通过totalsize参数从外部传入总记录数，  
        //这样在分页方法内部无需执行总记录数查询操作，以便提升系统性能，这种风格使用简单，效率相对第一种风格较高，但是要额外配置总记录数查询sql  
        //condition参数保存了查询条件  
        return executor.queryListInfoBean(HashMap.class, "queryMaterialList", offset, pagesize,totalSize ,condition);  
    }  
      
      
    public ListInfo queryMaterailListInfoThirdStyleBean(int offset, int pagesize ,PurchaseApplyCondition condition) throws Exception {  
        //根据sql语句和外部传入的总记录数sql语句进行分页，这种风格使用简单，效率最高，但是要额外配置总记录数查询sql  
        ListInfo list = executor.queryListInfoBeanWithDBName(HashMap.class, "bspf","queryMaterialList", 0, 10,"queryCountMaterialList" ,condition);  
        return list;  
    }  
    /*******************************以bean方式传递查询条件结束*******************************/  
      
    /*******************************以传统绑定变量方式传递查询条件开始*******************************/  
    public ListInfo queryMaterailListInfoFirstStyle(int offset, int pagesize ,String id) throws Exception {  
          
        //执行分页查询，queryMaterialList对应分页查询语句，  
        //根据sql语句在分页方法内部执行总记录数查询操作，这种风格使用简单，效率相对较低  
        //id参数保存了查询条件  
        return executor.queryListInfo(HashMap.class, "queryMaterialListbindParam", offset, pagesize,id);  
    }  
      
    public ListInfo queryMaterailListInfoSecondStyle(int offset, int pagesize ,String id) throws Exception {  
        //执行总记录查询并存入totalSize变量中，queryCountMaterialList对应一个优化后的总记录查询语句  
        //id参数保存了查询条件  
        long totalSize = executor.queryObject(long.class, "queryCountMaterialListbindParam",id);  
        //执行总记分页查询，queryMaterialList对应分页查询语句，通过totalsize参数从外部传入总记录数，  
        //这样在分页方法内部无需执行总记录数查询操作，以便提升系统性能，这种风格使用简单，效率相对第一种风格较高，但是要额外配置总记录数查询sql  
        //id参数保存了查询条件  
        return executor.queryListInfoWithTotalsize(HashMap.class, "queryMaterialListbindParam", offset, pagesize,totalSize,id );  
    }  
      
      
    public ListInfo queryMaterailListInfoThirdStyle(int offset, int pagesize ,String id) throws Exception {  
        //根据sql语句和外部传入的总记录数sql语句进行分页，这种风格使用简单，效率最高，但是要额外配置总记录数查询sql,id参数保存了查询条件  
        ListInfo list = executor.queryListInfoWithDBName2ndTotalsizesql(HashMap.class, "bspf","queryMaterialListbindParam", 0, 10,"queryCountMaterialListbindParam",id );  
        return list;  
    }  
    /*******************************以传统绑定变量方式传递查询条件结束*******************************/  
  
       /********************************第四种风格测试用例开始******/  
public @Test void testoraclerownumoverorderby() throws Exception {  
        testsqlinfoorderby("oracle");  
    }  
    public @Test void testmysqlrownumoverorderby() throws Exception {  
        testsqlinfoorderby("mysql");  
    }  
    public @Test void testderbyrownumoverorderby() throws Exception {  
        testsqlinfoorderby("derby");  
    }  
    public @Test void testsqliterownumoverorderby() throws Exception {  
        testsqliteorderby("sqlite");  
    }  
      
    public @Test void testdb2rownumoverorderby() throws Exception {  
        testsqlinfoorderby("db2");  
    }  
    public @Test void testpostgresrownumoverorderby() throws Exception {  
        testsqlinfoorderby("postgres");  
    }  
    public @Test void testmssqlrownumoverorderby() throws Exception {  
        testsqlinfoorderby("mssql");  
    }  
    public void testsqlinfoorderby(String dbname) throws Exception {  
        //读取配置文件中的原生sql（select * from TD_APP_BOM where bm like ?），通过PlainPagineOrderby(原生排序条件封装对象)对象构造函数传入分页排序条件order by bm，分页pageisize=10参数和PlainPagineOrderby之间的是其他绑定变量参数条件  
        ListInfo list = executor.queryListInfoWithDBName (HashMap.class, dbname,"testsqlinfo", 0, 10,'%c%',new PlainPagineOrderby("order by bm"));  
        Map params = new HashMap();  
        params.put("app_name_en", "%C%");  
//读取配置文件中的模板sql，通过PlainPagineOrderby(原生排序条件封装对象)对象传入分页排序条件order by bm和其他模板变量参数条件对象  
        list = executor.queryListInfoBeanWithDBName (HashMap.class, dbname,"ROW_NUMBERquery", 0, 10,new PlainPagineOrderby("order by bm",params));  
        StringBuilder orderby = new StringBuilder();  
        orderby.append(" #if($sortKey && !$sortKey.equals(\"\"))")  
        .append(" order by $sortKey ")  
        .append(" #if($sortDESC )")  
        .append("   desc ")  
        .append(" #else")  
        .append("   asc")  
        .append(" #end")  
        .append(" #else")  
        .append(" order by bm ")  
        .append(" #end");  
//读取配置文件中的模板sql，通过PagineOrderby(模板动态排序条件封装对象)对象传入分页动态排序条件和其他模板变量参数条件对象  
        list = executor.queryListInfoBeanWithDBName (HashMap.class, dbname,"ROW_NUMBERquery", 0, 10,new PagineOrderby(orderby.toString(),params));  
        params.put("sortKey", "id");  
        params.put("sortDESC", true);  
//调整排序字段为id，设置排序顺序为降序，读取配置文件中的模板sql，通过ConfigPagineOrderby(模板动态排序条件封装对象，但是只是指定了一个排序条件的名称，实际是从配置文件读取的)对象传入分页动态排序条件和其他模板变量参数条件对象  
        list = executor.queryListInfoBeanWithDBName (HashMap.class, dbname,"ROW_NUMBERquery", 0, 10,new ConfigPagineOrderby("ROW_NUMBERquery_orderby",params));  
        //三个SQLExecutor排序分页接口使用用法，除了没有ConfigPagineOrderby分页封装方式外，使用方法与ConfigSQLExecutor使用方法一致  
        //SQLExecutor直接操作原生sql（select * from TD_APP_BOM where bm like ?）做分页查询，通过PlainPagineOrderby(原生排序条件封装对象)对象构造函数传入分页排序条件order by bm，分页pageisize=10参数和PlainPagineOrderby之间的是其他绑定变量参数条件，  
        list = SQLExecutor.queryListInfoWithDBName (HashMap.class, dbname,"select * from TD_APP_BOM", 0, 10,new PlainPagineOrderby("order by bm"));  
        StringBuilder testsqlinfoorderby = new StringBuilder();  
        testsqlinfoorderby.append("select * from TD_APP_BOM where 1=1")  
                .append("   #if($bm && !$bm.equals(\"\"))")  
                .append("   and bm = #[bm]")  
                .append("   #end")  
                .append("   #if($app_name_en && !$app_name_en.equals(\"\"))")  
                .append("       and app_name_en like #[app_name_en]")  
                .append("   #end")  
                .append("   #if($app_name && !$app_name.equals(\"\"))")  
                .append("       and app_name like #[app_name]")  
                .append(" #end")  
                .append("       #if($soft_level && !$soft_level.equals(\"\"))")  
                .append("       and soft_level=#[soft_level]")  
                .append("   #end")  
                .append("   #if($state && !$state.equals(\"\"))")  
                .append("       and state=#[state]")  
                .append("       #end")  
                .append("           #if($rd_type && !$rd_type.equals(\"\"))")  
                .append("   and rd_type=#[rd_type]")  
                .append("   #end");  
           
        list = SQLExecutor.queryListInfoBeanWithDBName (HashMap.class, dbname,testsqlinfoorderby.toString(), 0, 10,new PlainPagineOrderby("order by bm",params));  
          
        list = SQLExecutor.queryListInfoBeanWithDBName (HashMap.class, dbname,testsqlinfoorderby.toString(), 0, 10,new PagineOrderby(orderby.toString(),params));  
       
          
        return ;  
    }  
      
    public void testsqliteorderby(String dbname) throws Exception {  
        //根据sql语句和外部传入的总记录数sql语句进行分页，这种风格使用简单，效率最高，但是要额外配置总记录数查询sql  
        ListInfo list = null;  
        Map params = new HashMap();  
        params.put("moudleName", "%t%");  
           
        StringBuilder orderby = new StringBuilder();  
        orderby.append(" #if($sortKey && !$sortKey.equals(\"\"))")  
        .append(" order by $sortKey ")  
        .append(" #if($sortDESC )")  
        .append("   desc ")  
        .append(" #else")  
        .append("   asc")  
        .append(" #end")  
        .append(" #else")  
        .append(" order by moudleName ")  
        .append(" #end");  
       
          
          
        list = SQLExecutor.queryListInfoWithDBName (HashMap.class, dbname,"select * from BBOSS_GENCODE", 0, 3,new PlainPagineOrderby("order by moudleName"));  
        StringBuilder testsqlinfoorderby = new StringBuilder();  
        testsqlinfoorderby.append("select * from BBOSS_GENCODE where 1=1")  
                .append("   #if($DBNAME && !$DBNAME.equals(\"\"))")  
                .append("   and DBNAME = #[DBNAME]")  
                .append("   #end")  
                .append("   #if($moudleName && !$moudleName.equals(\"\"))")  
                .append("       and moudleName like #[moudleName]")  
                .append("   #end")  
                ;  
           
        list = SQLExecutor.queryListInfoBeanWithDBName (HashMap.class, dbname,testsqlinfoorderby.toString(), 0, 3,new PlainPagineOrderby("order by moudleName",params));  
        params.put("sortKey", "id");  
        params.put("sortDESC", true);  
        list = SQLExecutor.queryListInfoBeanWithDBName (HashMap.class, dbname,testsqlinfoorderby.toString(), 0, 3,new PagineOrderby(orderby.toString(),params));  
       
          
        return ;  
    }  
       /********************************第四种风格测试用例结束******/  
}  
```

举例完毕，如有疑问，请留言进一步探讨。

补充说明一下：ListInfo对象包含当页记录集和总记录数以及每页最多纪录数，如果是more分页查询，还包含了more分页标识