### jquery之陷阱-选择器中的特殊字符转义

最近接触jquery，觉得jquery确实比较好用，但是在使用过程中碰到不少问题，最典型的问题是选择器通过元素的#id来查找元素对象时，如果id中包含jquery的特殊字符就会导致查找失败，目前我碰到了以下特殊字符需要转义：

 / $ [ ]

转义的方法如下：

var ret = id.replace(/:/g,"\\:");

​        ret = ret.replace(/\./g,"\\.");

​        ret = ret.replace(/\//g,"\\/");

​        ret = ret.replace(/\$/g,"\\$");

​        ret = ret.replace(/\[/g,"\\[");

​        ret = ret.replace(/\]/g,"\\]");

不知道大家还有没有更好的方法来解决这个问题，另外是不是还有别的特殊字符需要转义。           