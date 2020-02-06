### bbossgroups标签库使用大全（续二）-表达式使用

本文详细介绍bboss标签库特有表达式的使用方法。可以在分页/列表/详细信息标签及相关标签中使用表达式。

**1 概述**

 表达式支持的基本操作有：乘法，除法，求余，加/减，字符串的加操作

  操作数：可以为变量，整数，小数，字符串

 其中变量是指列表/分页/页面详细信息中的对象属性名称，为如下形式：
    {variable name}

表达式中用"{"和"}"来标识变量

  在表达式中需要属性的值时必须通过变量的方式来获取

变量名称有三种形式，每种形式有不同的含义：

​    简单变量    －－ 及为当前列表中基本属性，例如 name

​    二重变量    －－ 及为当前列表中属性对应对象中的属性，例如 user.name

​    带索引的变量－－ 如果列表或者分页或者详细信息标签的使用出现嵌套的情况时通过在变量中使用索引来求外层列表中的对象属性的值， 索引必须设置在变量名称的最前面并且为数字，以‘.’号与变量名称隔开,  例如 1.name或者1.user.name表示第二层嵌套中的属性（外层嵌套的编号从最外层开始，最外层也就是第一层的编号为0，第二层为1。。。）

聚合操作也是针对变量（属性）而设计   

 例如：

   2 * 2

  2 + 2 * 3

  2 - 2 / 3

  2 - 3 % 2

 2* {var1} / {var2}

上述操作可以任意组合

对应的cell标签属性名称为expression,属性值可以为一个表达式，也可以为一个字符串或者一个数，例如：  

​        数：整数123，或者小数21342.234234

​        字符串："\"测试\""

​        表达式：{属性1} + {属性}

对应的逻辑标签有两个属性：expression和expressionValue，属性值同cell标签expression属性值

**2 保留变量**

整个标签库为表达式预留了以下变量：

rowid：可以在表达式中获取当前记录集的行号，带页面偏移量，前面可带外面集合的索引号，索引号从最外层集合以0开始,依次加1

offset：在表达式中获取当前页面记录的起始位置，分页时有用

rowcount :保存总记录数

pagesize：保存当页获取到的实际纪录数

mapkey： 保存map迭代当前的key值

currentcell：保存list/array循环中当前的值

例如：

<pg:equal value=”1” expression=”{rowid}%5”>//当行号rowid模5求余为1时执行等于标签中的值

Output something.

</pg:equal>

**3 实例**

**3.1  通过计算表达式的值确定cell标签的输出值，例如：**  

情况1 表达式中只有数字（整数和小数都可以，如果全是整数那么输出的结果是一个整数，如果出现小数那么输出的结果将是小数）

​    只有整数<pg:cell expression="1 + 2 + (123 * 12)/2*(12-3)"/>表达式计算的值作为cell标签的输出，可以通过numerformat属性对输出的数字进行格式化

​    有小数  <pg:cell expression="1 + 2.2 + (123 * 1.2)/2*(12-3.2)"/>表达式计算的值作为cell标签的输出，可以通过numerformat属性对输出的数字进行格式化

情况2 表达式中有字符串，那么表达式将会把操作数全部当作字符串来处理

​    <pg:cell expression="\"值：\" + {属性}"/>，标签库首先会求当前列表中属性值，然后将属性值和前面的提示信息一起输出

与前面的提示信息一起输出

 情况3 表达式中有属性：

​    <pg:cell expression="{属性1} + {属性2} + (123 * 12)/2*(12-{属性3})"/>

​          标签库在解析表达式的时候会获取表达式中出现的变量（及属性）的值，然后再求整个表达式的值。

​          比如"{属性1} + {属性2} + (123 * 12)/2*(12-{属性3})"，   首先求出属性1、属性2
​          属性3的值,如果求出的值分别为4，5，6,那么表达式就会变成：

​          4 + 5 + (123 * 12)/2*(12-6)，最终计算的结果将会作为cell标签的输出

**3.2 逻辑标签可以指定表达式来确定实际值（expression属性）和期望值(expressionValue属性)，或者是确定其中的一个，表达式的求值过程和cell标签中表达式求值过程一致，下面以equal标签来举几个例子，其它的逻辑标签都是一样的**

情况1 比较两个属性的值

​        <pg:equal colName="attr1" expressionValue="{attr2}"/>

​        或者

​        <pg:equal expression="{attr1}" expressionValue="{attr2}"/>这两种方式是等价的
​       

​    如果有嵌套：

​    <pg:equal colName="attr1" expressionValue="{0.attr2}"/>

​    或者

​    <pg:equal expression="{attr1}" expressionValue="{0.attr2}"/>这两种方式是等价的

​    或者

​    <pg:equal expression="{0.attr1}" expressionValue="{0.attr2}"/>这两种方式是等价的

 情况2

​        <pg:equal colName="attr1" expressionValue="1+2+{attr2}"/>

​        或者

​        <pg:equal expression="{attr1}" expressionValue="1+2+{0.attr2}"/>这两种方式是等价的

​        或者

​        <pg:equal expression="{0.attr1}" expressionValue="1+2+{0.attr2}"/>这两种方式是等价的       

​    其它逻辑标签雷同

**3.3 逻辑标签in，notin使用说明、实例**

逻辑标签in功能说明：判断计算属性或者expression属性对应的表达式得到值是否包含在scope属性或表达式对应的值范围之内，值范围以逗号分隔，如果包含则执行in标签体对应的代码，否则不执行。

逻辑标签notin功能说明：判断计算属性或者expression属性对应的表达式得到值是否包含在scope属性或表达式对应的值范围之内，值范围以逗号分隔，如果不包含则执行notin标签体对应的代码，否则不执行。

 <pg:in expression="{aaa}" scope="1,2,3,4,5">ddddd</pg:in>

<pg:notin expression="{aaa}" scope="1,2,3,4,5">ddddd</pg:notin

<pg:in colName="aaa" scope="1,2,3,4,5">ddddd</pg:in>

<pg:notin colName="aaa" scope="1,2,3,4,5">ddddd</pg:notin>

<pg:in expression="{aaa}" scope="{scopevaribale}">ddddd</pg:in

<pg:notin expression="{aaa}" scope="{scopevaribale}">ddddd</pg:notin>

**3.4关于表达式的改进：**

expression，expressionValue可以用来指定表达式，并对表达式求值，之前不能指定常量，现在可以了举例如下：

并且增加了表达式内置变量rowid，offset，这两个变量可以直接再表达式中使用，rowid存放当前记录的行号，offset存放当前页面的起始地址

<pg:equal expression="{rowid}" expressionValue="1">

   啊啊啊啊

</pg:equal>

**3.5 增加contain和match两个对属性值和表达式值进行正则表达式匹配的标签，要匹配的模式通过pattern属性来指定。**

使用实例：

<pg:contain expression="{aaa}" pattern="[1-2]+">ddddd</pg:contain

<pg:match expression="{aaa}" pattern="[1-2]+">ddddd</pg:match>

<pg:contain colName="aaa" pattern="[1-2]+">ddddd</pg:contain>

<pg:match colName="aaa" pattern="[1-2]+">ddddd</pg:match

**3.6 表达式中对变量求长度方法**

表达式中对变量求长度方法，如果变量是一个容器集合对象或者数组对象或者字符串，想在表达式中获取其长度，只需要在变量名称前面加size:前缀即可。

使用实例：

<pg:equal expression="{size:aaa}" value="10">ddddd</pg:equal

<pg:equal expression="{size:aaa}" expressionValue="{size:bbb}">ddddd</pg:equal

更多的标签属性可以参考标签定义tld文件：[pager-taglib.tld](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/pager-taglib.tld)





