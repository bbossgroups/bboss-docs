### bboss持久层返回mysql自增主键功能说明之二

在上一篇文章《[bboss持久层返回mysql自增主键功能说明](http://yin-bp.iteye.com/blog/1729484)》中提到如果升级该功能时需要重新编译使用了ConfigSQLExecutor和SQLExecutor两个组件的dao程序，这样会导致原有程序的升级困难， 经过短时间在项目中的应用实践发现重新编译dao程序是一个非常麻烦的事情，为了避免这个麻烦，特意将该功能改为回调方式返回自增主键，这样无需修改已有api，从而保持新旧版本之间的兼容性，升级时就无需编译原有dao程序重新生成jar包，现将改进后的使用方法做个简单介绍。

注意：本文适用于[bboss 3.6.1](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.1)分支版本。

首先，我们在mysql中建一个具有自增主键的demo表

Sql代码

```sql
CREATE TABLE  
    demo  
    (  
        id bigint NOT NULL AUTO_INCREMENT,  
        name VARCHAR(200),  
        PRIMARY KEY (id)  
    )  
```

然后构建一个PO对象：

Java代码

```java
package com.frameworkset.sqlexecutor;  
  
public class AutoKeyDemo {  
    private Long id;  
    private String name;  
    public Long getId() {  
        return id;  
    }  
    public void setId(Long id) {  
        this.id = id;  
    }  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
}  
```

接下来我们来演示单条记录插入和多条记录预编译批处理插入demo并返回自增主键的功能。

注意：多条记录预编译批处理插入时只会返回这批记录中最后一条记录产生的自增主键（如果哪位知道有全部返回所有自增主键的方法，请告诉我哦）

1.单条记录插入并返回自增主键

Java代码

```java
AutoKeyDemo demo = new AutoKeyDemo();         
        demo.setName("name2");    
        //主键被封装到GetCUDResult对象中,通过回调方式返回  
        //下面的insertBean方法最后带了一个GetCUDResult类型参数，  
        //这个方法是专门为返回自增主键而新增的一个api  
        GetCUDResult ret = new GetCUDResult();   
        SQLExecutor.insertBean("insert into demo(name) values(#[name])", demo,ret);  
        //通过GetCUDResult对象的getKeys方法获取主键，并将主键设置到demo对象中  
        demo.setId((Long)ret.getKeys());  
        //更新刚添加的记录  
        demo.setName("newname");      
        //upret是一个数字类型，表示更新成功的记录数         
        SQLExecutor.updateBean("update demo set name=#[name] where id=#[id]", demo,ret); 
```

2.多条记录预编译批处理插入并返回自增主键

Java代码 

```java
SQLExecutor.delete("delete from demo");  
        //构建多条记录  
        List<AutoKeyDemo> datas = new ArrayList<AutoKeyDemo>();       
        AutoKeyDemo demo = new AutoKeyDemo();         
        demo.setName("name2");  
        datas.add(demo);  
        demo = new AutoKeyDemo();         
        demo.setName("name3");  
        datas.add(demo);  
        demo = new AutoKeyDemo();         
        demo.setName("name4");  
        datas.add(demo);  
        //插入多条记录，并将成功插入的记录数和最后一条记录的主键值封装成GetCUDResult对象返回  
        //下面的insertBeans方法最后带了一个GetCUDResult类型参数，  
        //这个方法是专门为返回自增主键而新增的一个api  
        GetCUDResult ret = new GetCUDResult();   
        SQLExecutor.insertBeans("insert into demo(name) values(#[name])", datas,ret);  
        //获取自增主键列表（很遗憾，list中只有最后一条记录的主键，  
        //但是还是保留为List对象，以便后续有返回所有记录主键的解决方案后再以列表的方式返回这些主键）  
        List<Object> keys = (List<Object>)ret.getKeys();  
        for(int i = 0; i <keys.size(); i ++)  
        {  
            datas.get(i).setId((Long)keys.get(i));  
        }  
        //获取插入的的处理情况  
        int[] updatecount = (int[])ret.getUpdatecount();  
```

com.frameworkset.common.poolman.GetCUDResult对象是bboss持久层新增的一个类，他的功能描述如下：

保存数据库增删改的结果信息

GetCUDResult的属性含义如下：

result：操作结果，如果数据源autoprimarykey为true，并且在tableinfo表中保存了表的主键信息result为自增的主键，反之result为更新的记录数

updateCount:更新的记录数

keys:自动产生的主键，如果只有一条记录则为普通对象，如果有多条记录则为Lis**<tObject**>类型

