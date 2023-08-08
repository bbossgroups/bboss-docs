### bboss taglib 通过数据加载器获取数据的分页/列表标签

下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092

通过数据加载器获取数据的分页/列表标签的使用分3步

1. 定义数据加载器

2. 将数据加载器配置到listdata.properties文件中（2007-11-7 之前的版本一定要采用配置文件，

后续版本可直接在listdata标签的dataInfo属性中指定数据加载器的全路径，而无需在配置文件中进行配置

比如com.bboss.tag.DataList）

​    3. 编写页面jsp页面引用标签，实现分页/列表展示

A数据加载器定义

所有的数据加载器必须从抽象类

com.frameworkset.common.tag.pager.DataInfoImpl继承并实现DataInfoImpl中的两个抽象方法。

DataInfoImpl还有以下下特点：

提供获取分页数据和列表数据的两个抽象方法。

提供对内存中的大数据集分页的方法；

提供访问控制对象accessControl，方便获取当前登录的用户帐号信息

提供了request,session两个jsp页面对象，方便获取当前页面请求参数和会话信息

下面分别说明一下两个抽象方法和对大数据集的分页方法

l         protected ListInfo getDataList(String sortKey,//未使用

​                     boolean desc, //未使用

long offset,//从数据源中或记录的起点

​                                   int maxPagesize //页面

)

   DataInfoImpl提供的抽象方法，由具体的数据加载器实现。实现分页数据加载，返回的对象中包含当前总记录数，当前页面结果列表(列表中封装值对象)，另外结果集还可以是一个对象数组

返回值说明：

ListInfo<List< ValueObject > datas,int totalsize>,包含两个信息

List datas获取到当前页面的结果集，封装值对象，对象的每个属性提供get/set方法

int totalsize 总记录数

ListInfo<Object[] datas,int totalsize>

​          Object[] datas从数据源中获取到对象数组，数组中对象的类型可以为一

般的值对象，也可以是Map类型的对象

int totalsize 总记录数

 pager标签的isList值为false时调用本方法

l         protected ListInfo getDataList(String sortkey, boolean desc)

DataInfoImpl提供的抽象方法，由具体的数据加载器实现。获取列表数据接口，当pager标签的isList属性为true时调用本方法

l         public static ListInfo pagerList(List datas,int offset,int pageItems)

DataInfoImpl提供的实用方法，实现对内存中的一个大的数据集分页。

参数datas对应于大的数据集，offset对应于分页的起点，pageItems对应于每页最大的记录条数，方法根据offset和pageItems对大数据集datas进行分页得到ListInfo对象。本方法可在方法

protected ListInfo getDataList(String sortKey,//未使用

​                     boolean desc, //未使用

long offset,//从数据源中或记录的起点

​                                   int maxPagesize //页面

)

中调用。

1.结果集为列表的情况

public class DocumentList extends DataInfoImpl

{

​    /**

​     \* 分页数据获取接口，如果pager标签的isList属性指定为false时必须实现此方法

​     */

protected ListInfo getDataList(String sortKey, boolean desc, long

offset,int maxPagesize) {  

​           //从request中获取查询条件

​           String status = request.getParameter("status");

​           String user = request.getParameter("userid");

​           String doctype = request.getParameter("doctype");

​           String docLevel = request.getParameter("docLevel");

​           String userid = accessControl.getUserID();//从accessControl

//中获取登录用户id

​                      。。。。。。

​           ListInfo listInfo = new ListInfo();      

​           try { 

​              //定义查询sql

​              StringBuffer sql = new StringBuffer();

​                 DBUtil dbUtil = new DBUtil();

​                  。。。。。

​              //调用dbutil的分页查询方法

​                 //将结果集封装到值对象列表，将总记录数和值对象列表设置到listinfo

//对象中。

​                 dbUtil.executeSelect(sql,offset,maxPagesize);

​                 List datas = new ArrayList();

​                 for(int I = 0; I < dbUtil.size();I ++)

​                 {

​                   Document doc = new Document();

​                       doc.setAttribute(dbUtil.getString(I,”attribute”));

​                       ...

​                  datas.add(doc);

}

​                 //调用listInfo.setDatas(datas)存放当前页面的数据集

​                listInfo.setDatas(datas);

​                 //调用listInfo.setTotalsize(dbUtil.getTotalsize())设置总//记录条数      

​                 listInfo.setTotalsize(dbUtil.getTotalsize()); 

​                 //也可以将上述逻辑封装到业务方法中，调用业务方法获取listinfo对象

​               //listInfo= docManager.getDocList(sql.toString(),

​                 //              (int)offset,maxPagesize);  

​            }catch(Exception e){

​                      e.printStackTrace();

​             }

​           //返回查询结果

​          return listInfo;  

​      } 

​      /**

​       \* 列表数据获取接口，如果pager标签的isList属性指定为true时必须实现此方

\* 法

​       */

​      protected ListInfo getDataList(String arg0, boolean arg1) {

​            //从request中获取查询条件

​           String status = request.getParameter("status");

​           String user = request.getParameter("userid");

​           String doctype = request.getParameter("doctype");

​           String docLevel = request.getParameter("docLevel");

​           String userid = accessControl.getUserID();//从accessControl

//中获取登录用户id

​                      。。。。。。

​           ListInfo listInfo = new ListInfo();      

​           try { 

​              //定义查询sql

​              StringBuffer sql = new StringBuffer();

​                 DBUtil dbUtil = new DBUtil();

​                  。。。。。

​              //调用dbutil的分页查询方法

​                 //将结果集封装到值对象列表，将总记录数和值对象列表设置到listinfo

//对象中。

​                 dbUtil.executeSelect(sql);

​                 List datas = new ArrayList();

​                 for(int I = 0; I < dbUtil.size();I ++)

​                 {

​                   Document doc = new Document();

​                       doc.setAttribute(dbUtil.getString(I,”attribute”));

​                       ...

​                  datas.add(doc);

}

​                 //调用listInfo.setDatas(datas)存放当前页面的数据集

​                listInfo.setDatas(datas);

​                 //也可以将上述逻辑封装到业务方法中，调用业务方法获取listinfo对象

​               //listInfo= docManager.getDocList(sql.toString(),

​                 //              );            

}catch(Exception e){

​             e.printStackTrace();

​       }

​       //返回查询结果

​       return listInfo;       

}

2.结果集为对象数组的情况

public class DocumentList extends DataInfoImpl

{

​    /**

​     \* 分页数据获取接口，如果pager标签的isList属性指定为false时必须实现此方法

​     */

protected ListInfo getDataList(String sortKey, boolean desc, long

offset,int maxPagesize) {  

​           //从request中获取查询条件

​           String status = request.getParameter("status");

​           String user = request.getParameter("userid");

​           String doctype = request.getParameter("doctype");

​           String docLevel = request.getParameter("docLevel");

​           String userid = accessControl.getUserID();//从accessControl

//中获取登录用户id

​                      。。。。。。

​           ListInfo listInfo = new ListInfo();      

​           try { 

​              //定义查询sql

​              StringBuffer sql = new StringBuffer();

​                 DBUtil dbUtil = new DBUtil();

​                  。。。。。

​              //调用dbutil的分页查询方法

​                 //将结果集封装到值对象列表，将总记录数和值对象列表设置到listinfo

//对象中。

​                 dbUtil.executeSelect(sql,offset,maxPagesize);

​                 //调用listInfo.setArrayDatas(datas)存放当前页面的数据集

​                Map[] datas = dbUtil.getAllResults();

​                listInfo.setArrayDatas(datas);

​                 //调用listInfo.setTotalsize(dbUtil.getTotalsize())设置总//记录条数      

​                 listInfo.setTotalsize(dbUtil.getTotalsize()); 

​                 //也可以将上述逻辑封装到业务方法中，调用业务方法获取listinfo对象

​               //listInfo= docManager.getDocList(sql.toString(),

​                 //              (int)offset,maxPagesize);  

​            }catch(Exception e){

​                      e.printStackTrace();

​             }

​           //返回查询结果

​          return listInfo;  

​      } 

​      /**

​       \* 列表数据获取接口，如果pager标签的isList属性指定为true时必须实现此方

\* 法

​       */

​      protected ListInfo getDataList(String arg0, boolean arg1) {

​            //从request中获取查询条件

​           String status = request.getParameter("status");

​           String user = request.getParameter("userid");

​           String doctype = request.getParameter("doctype");

​           String docLevel = request.getParameter("docLevel");

​           String userid = accessControl.getUserID();//从accessControl

//中获取登录用户id

​                      。。。。。。

​           ListInfo listInfo = new ListInfo();      

​           try { 

​              //定义查询sql

​              StringBuffer sql = new StringBuffer();

​                 DBUtil dbUtil = new DBUtil();

​                  。。。。。

​              //调用dbutil的分页查询方法

​                 //将结果集封装到值对象列表，将总记录数和值对象列表设置到listinfo

//对象中。

​                 dbUtil.executeSelect(sql);

​                 Map[] datas = dbUtil.getAllResults();

​               

​                 //调用listInfo.setArrayDatas(datas)存放页面的数据集,该方法的参数类型为Object[]

​                listInfo.setArrayDatas(datas);

​                

​                 //也可以将上述逻辑封装到业务方法中，调用业务方法获取listinfo对象

​               //listInfo= docManager.getDocList(sql.toString(),

​                 //              );            

}catch(Exception e){

​             e.printStackTrace();

​       }

​       //返回查询结果

​       return listInfo;       

}