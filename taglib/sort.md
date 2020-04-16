### bboss taglib 列表/分页的排序功能介绍

bboss taglib下载地址：

h[ttps://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092](https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092)

列表分页标签中可以针对特定的列进行升序和降序排序，排序的数据集可以是当前页面的记录，也可以是所有的记录集，二者不能同时使用。

排序功能与list标签、标签head、标签title相关，具体描述如下：

List标签的autosort属性：

如果通过title标签设置排序字段，通过该autosort属性来控制是否自动对当前页数据排序，还是在数据加载器中手工构造sql语句对全部数据排序：

true-自动对当前页面数据排序，缺省值，这时用户不应该再根据title字段去构造sql的排序条件，否则排序功能将会不正常。

false-屏蔽对当前页面数排序功能，在数据加载器中手工构造sql语句对全部数据排序,如果数据加载器中没有将title指定的排序字段作为sql的排序字段，那么title对应的排序字段将不起作用。

Head标签其功能是用来输出表头行，标签体中可以包含title标签，head标签类似htmlt的tr标签，具有tr的一系列的属性。

Title标签可以用来指定排序的字段及标题信息。

下面是两个简单的实例：

实例1 直接使用list标签直接进行分页，指定了sql语句并且对当前页面数据进行排序

< table >

​     通过设置autosort属性为true，使标签自动对当页数据排序

   <pg:list autosort="true" id="testid" statement="select * from tableinfo order by table_id_value desc"

​          dbname="bspf" isList="false" maxPageItems="5">

​       < pg:header >

​             按照表名排序

​          <pg:title type="td" width="15%" className="headercolor" title="表名" sort="true" colName="table_name"/>

​             按照表id名排序

​          <pg:title type="td" width="15%" className="headercolor"  sort="true" colName="table_id_name" title="表id名"/>

​             不按照表id值排序

​          <pg:title type="td" width="15%" className="headercolor"  sort="false" colName="table_id_value" title="表id值"/>           

​       </ pg:header >

​       <pg:param name="table_name"/>

​       <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">

​          < td > <pg:rowid offset="false" increament="1"/>

​              <pg:cell colName="table_name" defaultValue=""/>

​          </ td >

​          < td >

​              <pg:cell colName="table_id_name" defaultValue="" />

​          </ td >

​          < td class="tablecells" align=center height='30' width="5%" >

​              <pg:cell colName="table_id_value" defaultValue=""/>

​          </ td > 

​       </ tr >

   </ pg:list >

   < tr >< td ><pg:rowcount id="testid"/></ td >< td colspan="2"><pg:index id="testid"/></ td ></ tr >         

</ table >

实例2 在数据加载器中根据排序字段构造排序sql语句

< table >

​    test.TestDataInfo对应数据加载器的实现类，更据方法传递的sortkey和desc两个参数构建排序sql，就会实现全部数据集的排序功能

   <pg:listdata dataInfo="test.TestDataInfo" keyName="TestDataInfo" />

   <!-- 分页显示开始,分页标签初始化 -->

   <pg:pager maxPageItems="10" id="TestDataInfo" scope="request" data="TestDataInfo" isList="false">

​      autosort="false"用来屏蔽自动对当前页面的排序功能

   <pg:list autosort="false">

​       <tr class="cms_data_tr" id="<pg:cell colName="table_name" defaultValue=""/>">

​          < td >

​              <pg:cell colName="table_name" defaultValue=""/>

​          </ td >

​          < td >

​              <pg:cell colName="table_id_name" defaultValue="" />

​          </ td >   

​       </ tr >

   </ pg:list >

   < tr >< td >rowcount:< pg:rowcount /></ td >< td colspan="2">< pg:index /></ td ></ tr >

   </ pg:pager >

test.TestDataInfo的实现代码如下：

package test;

import java.sql.SQLException;

import com.frameworkset.common.poolman.DBUtil;

import com.frameworkset.common.tag.pager.DataInfoImpl;

import com.frameworkset.util.ListInfo;

public class TestDataInfo extends DataInfoImpl {

​       protected ListInfo getDataList(String sortKey,

​                     boolean desc) {

​              ListInfo info = new ListInfo();

​              DBUtil dbUtil = new DBUtil();

​              try {

​                     if(sortKey != null && !sortKey.equals(""))

​                     {

​                            dbUtil.executeSelect("select * from " +

​                                          "tableinfo order by "

​                                          \+ sortKey + (desc?" desc" : " asc"));

​                     }

​                     else

​                     {

​                            dbUtil.executeSelect("select * from " +

​                                          "tableinfo ");

​                     }

​                     info.setArrayDatas(dbUtil.getAllResults());

​              } catch (SQLException e) {

​                    

​                     e.printStackTrace();

​              }

​              return info;

​       }

​       protected ListInfo getDataList(String sortKey,

​                     boolean desc, long offSet,

​                     int pageItemsize) {

​              ListInfo info = new ListInfo();

​              DBUtil dbUtil = new DBUtil();

​              try {

​                     if(sortKey != null && !sortKey.equals(""))

​                     {

​                            dbUtil.executeSelect("select * from tableinfo " +

​                                          "order by "

​                                   \+ sortKey + (desc?" desc" : " asc"),

​                                   offSet,pageItemsize);

​                     }

​                     else

​                     {

​                            dbUtil.executeSelect("select * from tableinfo",

​                                          offSet,pageItemsize);

​                     }

​                     info.setArrayDatas(dbUtil.getAllResults());

​                     info.setTotalSize(dbUtil.getTotalSize());

​              } catch (SQLException e) {

​                     e.printStackTrace();

​              }

​              return info;

​       }

}

 

