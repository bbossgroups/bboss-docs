### bboss分页组件翻页js前置处理函数和js后置处理函数使用方法

bboss 3.7.7版本及后续版本为分页组件增加翻页js前置处理函数和翻页js后置处理函数，本文介绍使用方法。

1.说明：

为了实现这个功能，对/include下面的pager.js文件做了重构，将所有的分页处理函数封装到bboss.pager对象中。

2.翻页js前置处理函数和js后置处理函数使用实例

Js代码

```js
$(document).ready(function(){  
                    //opt:包含pageurl, containerid, selector三个属性  
                    bboss.pager.pagerevent = {  
                            beforeload:function(opt){  
                                alert("beforeload containerid:"+opt.containerid);  
                            },  
                            afterload:function(opt){  
                                alert("afterload containerid:"+opt.containerid);  
                            }};  
                       $("#pagecontainer").load("pagerqueryuser.htm #pagecontent");  
                       $("#pagecontainer1").load("pagerqueryuser1.htm #pagecontent");   
                      $("#pagecontainer2").load("pagerqueryuser2.htm #pagecontent");  
                    });  
```

前置处理函数beforeload和后置处理函数afterload通过bboss.pager.pagerevent属性指定：

**bboss.pager.pagerevent = {**

**beforeload:function(opt){**

**alert("beforeload containerid:"+opt.containerid);**

**},**

**afterload:function(opt){alert("afterload containerid:"+opt.containerid);**

**}};**

函数的参数opt包含以下属性：

pageurl 请求的页面地址

containerid 加载页面的容器id

selector 要加载的内容容器id（可选）

如果beforeload函数返回false，分页组件将阻止翻页动作的执行。

