### bboss持久层ormaping机制详解

bboss持久层针对db操作即提供了原始sql语句的支持，又提供了简单而高效的ormapping机制，本文详细介绍之

1.可变参数的原生sql API

删除

SQLExecutor.delete("delete from LISTBEAN");

SQLExecutor.delete("delete from LISTBEAN where id=?",1);

插入

sql ="insert into LISTBEAN(ID,FIELDNAME,FIELDLABLE,FIELDTYPE) " +"values(?,?,?,?)";

SQLExecutor.insertWithDBName("bspf",sql,DBUtil.getNextPrimaryKey("bspf","ListBean"),"insertOpreation","ttyee","int");

查询
sql = "select * from LISTBEAN where fieldName=?";

List**<ListBean**> beans = SQLExecutor.queryListWithDBName(ListBean.class,"bspf",sql,"testttt");

更新
sql = "update LISTBEAN set FIELDNAME=? where ID=?";

SQLExecutor.update(sql, "mytest",100);

2.or mapping机制

bboss 针对增删改查都提供了or mapping机制，增加、修改、删除的or mapping机制基本一致，主要是在sql语句中使用的模板变量名称和bean属性保持一致即可。

ListBean lb = new ListBean();

lb.setFieldLable("tttt");

lb.setFieldName("testttt");

lb.setFieldType("int");

lb.setIsprimaryKey(false);

lb.setRequired(true);

lb.setSortorder("ppp");

lb.setFieldLength(20);

lb.setIsvalidated(6);

//用List存放Bean，在某特定的连接池中进行crud操作

List**<ListBean**> beans = new ArrayList**<ListBean**>();

beans.add(lb);

lb = new ListBean();

lb.setFieldLable("sss");

lb.setFieldName("ss");

lb.setFieldType("int");

lb.setIsprimaryKey(false);

lb.setRequired(true);

lb.setSortorder("ppp");

lb.setFieldLength(20);

lb.setIsvalidated(6);

beans.add(lb);



lb = new ListBean();

lb.setFieldLable("sss");

lb.setFieldName("ss556");

lb.setFieldType("int");

lb.setIsprimaryKey(false);

lb.setRequired(true);

lb.setSortorder("ppp");

lb.setFieldLength(20);

lb.setIsvalidated(6);

beans.add(lb);

插入：

String sql = "insert into LISTBEAN(ID,FIELDNAME,FIELDLABLE,FIELDTYPE,SORTORDER,ISPRIMARYKEY,REQUIRED,FIELDLENGTH,ISVALIDATED) " +"values(#[id],#[fieldName],#[fieldLable],#[fieldType],#[sortorder]," +"#[isprimaryKey],#[required],#[fieldLength],#[isvalidated])";

SQLExecutor.insertBeans("bspf",sql,beans);

修改：

sql ="update LISTBEAN set FIELDNAME=#[fieldName] where ID=#[id]";

SQLExecutor.updateBeans(sql,beans);

删除：

sql = "delete from LISTBEAN where ID=#[id]";

SQLExecutor.deleteBeans(sql, beans);

上面的都是集合批处理操作，单个对象操作，只需要将原来的接口中的s去掉，然后sql是一样的，最后使用单个bean对象：

SQLExecutor.insertBean("bspf",sql,bean);

SQLExecutor.updateBean(sql,bean);

SQLExecutor.deleteBean(sql, bean);

查询的or mapping机制是自动将select中的查询字段名称与bean的属性进行映射，不需要显示指定：

sql ="select * from LISTBEAN where FIELDNAME=#[fieldName]";

beans = (List**<ListBean**>) SQLExecutor.queryListBean(ListBean.class, sql, conditionbean);  

po对象ListBean中的属性与select *中得到的column名称会自动进行映射，自动映射规则如下:

规则一 @Column注解指定column和bean field进行映射，优先级最高，例如：

@Column(name="tableColumnName")

private String fieldType;

bboss在映射时首先检查字段有没有被Column注解映射，如果没有继续后续的两个规则。

规则二 检查bean属性名称和select table column列名称是否一样，如果一样则进行值设置，否则继续最后一个规则，例如：

bean属性名称为field_name

private String field_name;

select table column名称也为field_name，则自动匹配并设置值。

规则三 检查规范化的bean属性名称和select table column列转换为规范化的java名称是否一样，如果一样则进行值设置，select table column列转换为规范化的java属性名称的规则为：field_name_xxx被转换后的java field name为fieldNameXxx。举例如下：

private String fieldName;

表的字段名称为field_name,则二者自动匹配并设置值。

上面就是bboss持久层所提供的or mapping和原生sql的简单介绍。

如果觉得上述or mapping机制无法完全满足你的要求，那么bboss持久层还提供了更加灵活高效的行处理器机制，行处理器的使用请参考文档：[bboss持久层行处理器使用介绍](http://yin-bp.iteye.com/blog/1175847)。  





