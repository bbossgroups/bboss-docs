### bbossgroups标签使用大全（续一）-安全篇

本文是bboss 标签库系列文章续篇-安全篇，前两篇文章[《bbossgroups标签使用大全》](http://yin-bp.iteye.com/blog/1136924)和[bbossgroups标签库使用大全（续）](http://yin-bp.iteye.com/blog/1137674) 介绍了bboss数据展示标签库和逻辑标签库。

本文介绍两个和安全相关的标签：

dtoken

assertdtoken

这两个标签所属的标签定义文件为[pager-taglib.tld](https://github.com/bbossgroups/bbossgroups-3.5/blob/master/bboss-mvc/WebRoot/WEB-INF/pager-taglib.tld),他们配合一起来解决跨站攻击和表单重复提交等安全性问题。

一、dtoken标签

dtoken标签用于在客户端生成动态令牌，用来防止跨站伪造攻击和表单重复提交问题，一般将其放置在：

1.表单中input hidden元素中

2.url的参数串中

3.ajax请求的json参数中

这些都可以通过dtoken标签来实现，dtoken定义在pager-taglib.tld文件中，三种方式参考文档：
[bboss 动态令牌机制轻松搞定网站跨站攻击和表单重复提交问题](http://yin-bp.iteye.com/blog/1662594)

dtoken属性：

element-可选属性，参数有4个值（如果没有指定默认为input类型）：

json 生成json串令牌，名称和值的分隔符默认为'号，如果要指定为"号，那么需要通过jsonsplit参数来指定， 如果jsonsplit为'，则输出值为：

*_dt_token_:'1518435257'

*如果如果jsonsplit为",则输出值为：

*_dt_token_:"1518435257"  

input输出input hidden，生成隐藏域令牌,输出值示例为：<**input type="hidden" name="_dt_token_" value="-1518435257">**  

param(等号表达式)，生成url参数串令牌， 输出值示例为：_dt_token_=1518435257

token-只生成令牌本身，对于这种方式，客户端必须将该token以参数名_dt_token_传回服务端，否则不起作用，输出值示例为：1518435257

jsonsplit-可选属性，指定json串字符串值得修饰符，默认为',可以指定为"

cache-标识是否在服务器端保存令牌，true保存，false不保存，默认为true，令牌存储机制由tokenFilter的tokenstore参数指定。

fid-可选属性，令牌所属窗体唯一标识，一个窗体只需要一个令牌，如果在同一个窗体中dtoken标签需要出现多次，比如在逻辑判断中出现多次，那么就需要指定fid属性，除此以外都不需要指定fid属性

二、assertdtoken标签

assertdtoken标签没有定义属性集，和dtoken标签配合使用，dtoken标签在客户端放置动态临时令牌，assertdtoken标签用于在服务端页面上判断令牌被客户端传回并且校验令牌是否正确。如果没有启用动态令牌机制，该标签无效；如果启用了，assertdtoken判断令牌是否被客户端传回并且校验令牌是否正确；如果没有传回则拒绝访问，如果传回的令牌已经失效则拒绝访问。

assertdtoken标签主要用于jsp页面，它和assertdtoken注解的功能一致，assertdtoken注解主要用于bboss mvc的控制器方法中。

到此两个简单的标签就介绍完了，虽然简单但却为我们有效解决了以下问题：

1.防止表单重复提交

2.防止跨站脚本编制漏洞

3.防止跨站请求伪造攻击

4.有效避免框架钓鱼攻击

5.有效防止链接注入攻击

