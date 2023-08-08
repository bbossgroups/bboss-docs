### bboss persistent 1.0.4 发布,功能变更清单见正文

bboss persistent 1.0.4 发布,下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=302766&release_id=688812 

功能变更清单如下：

o 调整com.frameworkset.common.poolman.handle.RowHandler

将原来的接口改为抽象类

将接口方法public void handleRow(Object rowValue,Record origine)

改为抽象方法public abstract void handleRow(Object rowValue,Record origine)

增加init和destroy方法，以便进行查询源数据和数据库名称dbname的初始化和销毁，这些信息对存储过程和函数调用中的行处理器不起作用。

增加方法

public SchemaType getSchemaType(String colName)

以便在行处理器中 获取列的java数据类型名称和数据库类型名称

o 修改com/frameworkset/common/poolman/Record.java类

增加记录的原始行号信息和get/set方法：

/**

*设置记录对应的数据库原始记录行号

*/

 private int rowid;

 public void setRowid(int rowid)

 {

this.rowid = rowid;

 }

 public int getRowid()

   {

​       return rowid;

   }

o ResoultMap类中对Record类的初始化方法进行了优化

o 扩展xml行处理器

可以自定义根节点名称

可以自定义字符集

可以自定义版本号

添加构造xml节点串的两个方法

实现

在com.frameworkset.common.poolman.handle.XMLRowHandler程序中添加以下方法：

​        /**

​         \* rowValue类型为StringBuffer

​         */

 public void handleRow(Object rowValue,Record origine) 的默认实现

/**

*返回xml串的根节点名称

*缺省为records，用户可以扩展这个方法

*@return

*/

 public String getRootName()

 {

return "records";

 }

 /**

*返回xml的编码字符集

gb2312，用户可以扩展这个方法

*@return

​        public String getEncoding()

​        {

return "gb2312";

​        }

​       /**

​         \* 返回xml语法的版本号

​         \* 缺省为1.0，用户可以扩展这个方法

​         \* @return

​         */

​        public String getVersion()

​        {

​            return "1.0";

​        }

构建xml节点串的方法

public static String buildNode(String columnNodeName,//xml节点名称

​                                      String columnName,//结点列名name属性的值

​                                      String columnType, //结点列jdbc类型属性名称值

​                                      String columnJavaType, //结点列java类型属性名称值

​                                      String value,//结点值

​                                      String split)//结点与节点之间的分割符

public static String buildNode(String columnNodeName, //xml节点名称

​                                Map attributes,//节点属性集

​                                String value, //节点值

​                                String split)//节点间的分割符

这些方法都有缺省实现，如果不一致的话可以在子类中覆盖。

使用实例

public class TestXMLHandler {

​    public static void testCustomXMLHandler()

​    {

​        PreparedDBUtil db = new PreparedDBUtil();

​        try {

​            db.preparedSelect("select * from tableinfo");

//            String results_1 = db.executePreparedForXML();

​            String results_ = db.executePreparedForXML(new XMLRowHandler(){

//

//           

​                public void handleRow(Object rowValue, Record origine)  {

​                    StringBuffer record = (StringBuffer )rowValue;

​                    record.append("    < record >\r\n");

​               try {

​                    SchemaType schemaType = super.getSchemaType("TABLE_NAME");

​                    record.append(super.buildNode("attribute",

​                                                  "TABLE_NAME",

​                                                  schemaType.getName(),

​                                                  schemaType.getJavaType(),

​                                                  origine.getString("TABLE_NAME"),

​                                                  "\r\n"));

​                    schemaType = super.getSchemaType("table_id_name");

​                    record.append(super.buildNode("attribute",

​                                                  "table_id_name",

​                                                  schemaType.getName(),

​                                                  schemaType.getJavaType(), 

​                                                  origine.getString("table_id_name"),

​                                                  "\r\n"));

​                    schemaType = super.getSchemaType("TABLE_ID_INCREMENT");

​                    record.append(super.buildNode("attribute",

​                                                  "TABLE_ID_INCREMENT",

​                                                  schemaType.getName(),

​                                                  schemaType.getJavaType(), 

​                                                  origine.getString("TABLE_ID_INCREMENT"),

​                                                  "\r\n"));

​                   schemaType = super.getSchemaType("TABLE_ID_GENERATOR");

​                    record.append(super.buildNode("attribute",

​                                                  "TABLE_ID_GENERATOR",

​                                                  schemaType.getName(),

​                                                  schemaType.getJavaType(),

​                                                  origine.getString("TABLE_ID_GENERATOR"),

​                                                  "\r\n"));

​                    schemaType = super.getSchemaType("TABLE_ID_TYPE");

​                    record.append(super.buildNode("attribute",

​                                                  "TABLE_ID_TYPE",

​                                                  schemaType.getName(),

​                                                  schemaType.getJavaType(), 

​                                                  origine.getString("TABLE_ID_TYPE"),

​                                                  "\r\n"));

​                  } catch (SQLException e) {

​                                     throw new RowHandlerException(e);

​                    }

​                    record.append("    </ record >\r\n");

​                }

​                public String getEncoding() {

​                    // TODO Auto-generated method stub

​                    return "gbk";

​                }

​                public String getRootName() {

​                    // TODO Auto-generated method stub

​                    return "tableinfo";

​                }

​                public String getVersion() {

​                    // TODO Auto-generated method stub

​                    return "2.0";

​                }

​            });

​            System.out.println(results_);

//            System.out.println(results_1);

​        } catch (SQLException e) {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​        }

​    }

​    public static void testDefualtXML()

​    {

​        PreparedDBUtil db = new PreparedDBUtil();

​        try {

​            db.preparedSelect("select * from tableinfo");

​            String results_1 = db.executePreparedForXML();

​            System.out.println(results_1);

​        } catch (SQLException e) {

​            // TODO Auto-generated catch block

​            e.printStackTrace();

​        }

​    }

  public static void main(String[] args)

​    {

​        testCustomXMLHandler();

​    }

}

o 修改bug，执行o/r mapping 查询时，如果数字类型/byte/boolean的数据值为null时，会报以下异常：

Build ValueObject for ResultSet[select * from mq_node where NODE_NAME='test'] Get Column[CA_ID] from  ResultSet to [com.bboss.mq.client.MqNode@10cec16.CA_ID[int](mailto:com.bboss.mq.client.MqNode@10cec16.CA_ID[int)] failed:null

ERROR 01-06 17 : 30 : 25,093 - Build ValueObject for ResultSet[select * from mq_node where 

NODE_NAME='test'] Get Column[CA_ID] from  ResultSet to [com.bboss.mq.client.MqNode@10cec16.CA_ID[int](mailto:com.bboss.mq.client.MqNode@10cec16.CA_ID[int)] failed:null

java.lang.IllegalArgumentException

 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)

 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)

 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)

 at java.lang.reflect.Method.invoke(Method.java:585)

 at com.frameworkset.common.poolman.ResultMap.buildValueObject(ResultMap.java:193)

 at com.frameworkset.common.poolman.StatementInfo.buildResultMap(StatementInfo.java:731)

 at com.frameworkset.common.poolman.util.SQLUtil.innerExecuteJDBC(SQLUtil.java:540)

 at com.frameworkset.common.poolman.DBUtil.executeSelectForObject(DBUtil.java:3753)

 at com.frameworkset.common.poolman.DBUtil.executeSelectForObject(DBUtil.java:3742)

 at com.frameworkset.common.poolman.DBUtil.executeSelectForObject(DBUtil.java:3618)

 at com.bboss.mq.client.MqNodeService.getNodeByName(MqNodeService.java:150)

 at be.ibridge.kettle.consumer_stream.ConsumerService.buildMQClient(ConsumerService.java:63)

 at be.ibridge.kettle.consumer_stream.Consumer.processRow(Consumer.java:155)

 at be.ibridge.kettle.consumer_stream.Consumer.run(Consumer.java:200)

原因分析：

数据库查询返回的结果集，由于数字类型/byte/boolean为null时，原来的处理程序直接返回null，而不是返回具体的数字类型/byte/boolean的缺省值，导致将null值设置给对象属性失败。

解决办法，修改对应数字类型/byte/boolean的handle接口提供null值的转换函数,将null转换为相应的缺省值。

com/frameworkset/common/poolman/handle/type/BigDecimalTypeHandler.java

com/frameworkset/common/poolman/handle/type/BooleanTypeHandler.java

com/frameworkset/common/poolman/handle/type/ByteTypeHandler.java

com/frameworkset/common/poolman/handle/type/DoubleTypeHandler.java

com/frameworkset/common/poolman/handle/type/FloatTypeHandler.java

om/frameworkset/common/poolman/handle/type/IntegerTypeHandler.java

com/frameworkset/common/poolman/handle/type/LongTypeHandler.java

com/frameworkset/common/poolman/handle/type/ShortTypeHandler.java

