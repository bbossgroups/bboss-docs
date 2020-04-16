### 树页面参数传递方法-树标签展开的参数传递

树页面参数传递方法-树标签展开的参数传递

bboss 项目文件清单：https://sourceforge.net/projects/bboss/files/ bboss taglib 1.0.2 发布 ，下载地址：[https://sourceforge.net/projects/bboss/files/Tag%20framework/bboss-taglib-1.0.2.zip](https://sourceforge.net/projects/bboss/files/Tag framework/bboss-taglib-1.0.2.zip)

如果树的展开模式设置为动静结合时，那么在第一次展开从未展开的树节点时就存在一个参数传递的问题，树标签库中提供了一个param标签来解决这个问题，下面举例来说明param标签的使用方法：

首先有一个树的jsp页面checkboxtree.jsp，与树相关的代码为：

​                       <tree:tree tree="TreeWithContextMenu"

​                                    node="TreeWithContextMenu.node"

​                                    imageFolder="tree_images"

​                                    collapse="true"

​                                          includeRootNode="true"                   

​                                          href="testtreenode.jsp"                         

​                                          target="_blank"

​                                          mode="static-dynamic"

​                                          \>

​                                          <!--

​                                                   树的展开和折叠时  保持页面的参数

​                                          -->                       

​                                 <tree:param name="oid"/>

​                                  <tree:checkbox name="test_checkbox"/>

​                                          <tree:treedata treetype="test.tree.TreeWithContextMenu"

​                                            scope="request"

​                                            rootid="root" 

​                                            rootName="测试树"

​                                            expandLevel="2"

​                                            showRootHref="true"

​                                            needObserver="false"

​                                            refreshNode="false"

​                                            enablecontextmenu="true"

​                                            />

</ tree:tree >

用下面的方式发出请求：checkboxtree.jsp？oid=testparamtransfer来展示树。

从jsp代码中可以看出expandLevel="2"，因此树默认只展开两级，展开模式为mode="static-dynamic"，即动静结合模式，当第一次展开第3级时，树的展开机制就会发出一个checkboxtree.jsp的请求来获取第3级数据，因此如果不保持oid参数的话就会使该参数丢失，导致树无法展示，我们在树的代码中看到<tree:tree>内嵌了一个标签：

<tree:param name="oid"/>

这个param标签就是用来保持树页面的参数的，保证树页面在动态展开节点时不会丢失页面请求参数，param标签的属性name对应于请求参数的名称oid，如果有多个参数只需要将每个参数以以下方式保持即可：

<tree:param name="oid"/>

<tree:param name="oid1"/>

<tree:param name="oid2"/>

等等。