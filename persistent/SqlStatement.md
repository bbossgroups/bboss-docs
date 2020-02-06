### bboss持久层改进支持模块sql配置文件引用其它模块sql配置文件中sql语句

具体使用方法如下：

Xml代码

```xml
<property name="selectByKeys"  
sqlfile="com/sany/appbom/service/appbom-1.xml" sqlname="selectByKeys"/>  
```

  sqlfile：指定引用sql语句所在的配置文件

sqlname：指定引用sql语句所在的配置文件中的sql语句名称。

一般不建议在dao层引用其他模块sql语句，应该在业务层通过模块业务接口来实现模块之间的依赖关系  