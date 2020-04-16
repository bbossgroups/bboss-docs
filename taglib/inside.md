### bboss taglib分页/列表标签中的内置变量

下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092

  List标签

dataSet(缺省变量名，用户自定义变量名由list标签的dataSetName属性指定)：访问当前页面数据结果属性的方法，提供了一系列的get方法

​                         dataSet.getString(attName)

dataSet.getStringWithdefault(attName,defaultvalue)

dataSet.getString(attName)

dataSet.getBoolean();

​                         dataSet.getDouble(attName)

​                         dataSet.getDate(attName)

dataSet.getInt(attName);

​                         等等

rowid(缺省变量名，用户自定义变量名由list标签的rowidName属性指定)：当前行号