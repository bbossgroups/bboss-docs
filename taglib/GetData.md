### bboss taglib直接指定数据库sql语句获取数据

bboss taglib下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290092

直接指定数据库和sql获取数据的分页列表标签的做法和通过加载器获取数据的方法的区别就是：

​       不需要写数据加载器

​       不要配置数据加载器到配置文件

​         不需要使用listdata标签

​       Pager标签不需要指定data属性

​         Pager标签需要指定statement属性和dbname属性用来指定执行查询的数据库和查询的sql语句，例如：

​           <pg:pager maxPageItems="5"

​                 scope="request"   

​                  statement="<%=query%>"  //数据库查询语句，比如"select name,birthdate from student"

​                 dbname="oa"           //poolman.xml文件中指定的dbname  

​                  title="分页标题"                   

​                  //isList="true" 如果是列表的话指定isList属性为true

​                  \>    

​       Cell标签和逻辑编辑中指定colName属性对应值为sql中的字段名而不是对象的属性名称

​    其他方面和通过使用数据加载器获取数据的使用方法都是一样的，这里就不举例说明了。