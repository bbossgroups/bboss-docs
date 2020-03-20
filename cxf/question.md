### cxf webserivce与jdk1.6包冲突问题解决办法

cxf webserivce与jdk1.6存在以下包冲突问题：

jaxb-impl-2.1.12.jar与jdk 1.6 的rt.jar包有冲突，会导致webservice服务发布失败。

我目前的解决办法是换回jdk 1.5，不知道大家有没有更好的解决办法


    


  