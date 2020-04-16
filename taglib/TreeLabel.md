### 关于树标签的问题1-动静模式是，点击下级节点不能展开的问题

关于树标签的问题-动静模式是，点击下级节点不能展开的问题

bboss 项目文件清单：https://sourceforge.net/projects/bboss/files/

bboss taglib 1.0.2 发布 ，下载地址：[https://sourceforge.net/projects/bboss/files/Tag%20framework/bboss-taglib-1.0.2.zip](https://sourceforge.net/projects/bboss/files/Tag framework/bboss-taglib-1.0.2.zip)

问题描述

动静结合的树，我setSon里面判断到了为root节点返回true，不为root节点我有判断的，但是在返回true的时候，为什么在页面上点击，动都不动一下呢，根本就没有提交到后台去。

yin-bp  10 : 09 : 09

怎么会内

李峰高  10 : 10 : 50

我也很奇怪了，我返回true，执行了hasSon是返回true的，但是却不执行setSon，按道理是不是如果hasSon是true的话，就应该执行setSon的呢？

yin-bp  1 0 : 11 : 15

没错

yin-bp  10 : 11 : 27

你看看是不是你的页面报错了

李峰高  10 : 12 : 25

页面没有咯

yin-bp  10 : 13 : 54

查错的方法如下：

1.先找到文件

webapp/WEB-INF/templates/tree.vm

2.找到以下代码

< iframe height='0' width='0' name='tree_content_bridge' FRAMEBORDER='no' border=0 ></ iframe >

修改height='0' width='0' 为height='100' width='100'

然后再看看

李峰高  10 : 15 : 00

哦，那我看看，还真的是很奇怪

yin-bp  10 : 15 : 20

调完了后，再改回去