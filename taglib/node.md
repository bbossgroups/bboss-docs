### 树标签的内置节点参数

bboss 项目文件清单：https://sourceforge.net/projects/bboss/files/

bboss taglib 1.0.2 发布 ，下载地址：[https://sourceforge.net/projects/bboss/files/Tag%20framework/bboss-taglib-1.0.2.zip](https://sourceforge.net/projects/bboss/files/Tag framework/bboss-taglib-1.0.2.zip)

在添加每个节点时将节点的所有参数放置到一个Map对象中，但是以下的几个名称是树中保留的参数名称，不能作为其他参数：

node_recursive:指定特定树节点的复选框是否具有递归选择的功能，值类型Boolean

node_uprecursive:指定特定树节点的复选框是否具有递归选择上级复选框的功能，值类型Boolean

node_partuprecursive：boolean值，指定特定节点的复选框是否具有部分递归

node_checkboxname：单独指定的复选框的名称

nodeLink:指定每个树节点的链接地址，可以是javascript函数，也可以是超链接

node_linktarget：指定每个节点链接地址弹出窗口名称

node_radioname：定义每个节点单选框名称

node_checkboxchecked :boolean值 标识复选框是否选中

node_checkboxdisabled：boolean值 标识复选框是否被禁用

node_radiochecked：boolean值 标识单选按钮是否被选中

node_radiodisabled：boolean值 标识单选按钮是否被禁用

树形标签包含5个标签：tree,checkbox,radio,treedata，param标签。