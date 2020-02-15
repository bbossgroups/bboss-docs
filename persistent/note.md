### bboss持久层bean属性和表字段相关注解使用说明

bboss持久层引入了三个注解：PrimaryKey、Column和IgnoreORMapping

本文介绍三个个注解的功能和使用方法。

**1.Column注解属性和功能介绍**

Column用来实现bean属性与表字段名称、字段类型映射功能

Column注解可以指定以下属性：

name 表字段名称

type 表字段类型

dataformat 数据日期类型格式

ignorebind 忽略对象属性insert，update，delete，select四种DB操作的对象属性与表字段的 o/r mapping绑定操作，相当于@IgnoreORMapping和@Column(ignoreCUDbind=true)组合使用

ignoreCUDbind 忽略对象属性insert，update，delete三种DB操作的对象属性与表字段的 o/r mapping绑定操作

editorparams 指定editor插件对应的参数值，可以自己任意组合参数串，由插件自行解析

editor 在o/r mapping操作时，自定义属性值和字段值的转换插件，分为column->field和field->column，column<->field三个方向的转换方法：

- column->field 查询时表字段值向对象field属性值的单向转换
- column<-field insert，update，delete时对象field属性值向表字段值的单向转换
- column<->field select insert，update，delete时对象field属性值与表字段值的双向转换

column<->field双向转换时，editor插件必须实现com.frameworkset.util.ColumnEditorInf接口：

Java代码 

```java
package com.frameworkset.util;  
  
import org.frameworkset.util.annotations.wraper.ColumnWraper;  
  
public interface ColumnEditorInf {  
     /** 
     * 查询时，表字段值转换为对象属性值，Gets the property value.将值转换为属性对应类型的值 
     * @param fromValue 
     * @return The value of the property.  Primitive types such as "int" will 
     * be wrapped as the corresponding object type such as "java.lang.Integer". 
     */  
    Object getValueFromObject(ColumnWraper columnWraper,Object fromValue) ;     
     /** 
     * 查询时，表字段值转换为对象属性值，Gets the property value.将值转换为属性对应类型的值 
     * @param fromValue 
     * @return The value of the property.  Primitive types such as "int" will 
     * be wrapped as the corresponding object type such as "java.lang.Integer". 
     */  
    Object getValueFromString(ColumnWraper columnWraper,String fromValue);  
    /** 
     * insert,update,delete时值转换方法，对象属性值转换为表字段值 
     * @param columnWraper 
     * @param fromValue 
     * @return 
     */  
    Object toColumnValue(ColumnWraper columnWraper,Object fromValue);  
    /** 
     * insert,update,delete时值转换方法，对象属性值转换为表字段值 
     * @param columnWraper 
     * @param fromValue 
     * @return 
     */  
    Object toColumnValue(ColumnWraper columnWraper,String fromValue);  
     
}  
```

column<-field insert，update，delete时对象field属性值向表字段值的单向转换,editor插件只需继承抽象类

com.frameworkset.util.FieldToColumnEditor，实现两个抽象方法即可：

Java代码

```java
@Override  
    public abstract Object toColumnValue(ColumnWraper columnWraper, Object fromValue) ;  
  
    @Override  
    public abstract Object toColumnValue(ColumnWraper columnWraper, String fromValue) ;  
```

column->field 查询时表字段值向对象field属性值的单向转换,editor插件只需继承抽象类

com.frameworkset.util.ColumnToFieldEditor，实现两个抽象方法即可：

Java代码

```java
@Override  
    public abstract Object getValueFromObject(ColumnWraper columnWraper, Object fromValue) ;  
  
    @Override  
    public abstract Object getValueFromString(ColumnWraper columnWraper, String fromValue) ;  
```

**2.@IgnoreORMapping注解功能介绍**

IgnoreORMapping注解没有属性，忽略DB查询操作时对象属性与表字段的o/r mapping映射操作

**3.PrimaryKey注解属性和功能介绍**

PrimaryKey用来实现自动设置主键值和bean属性与表字段名称、字段类型映射功能

Column注解可以指定以下属性：

name 表字段名称

type 表字段类型

**4.注解使用方法示例**

PrimaryKey和Column的作用域都是bean的字段属性，使用方法非常简单：

Java代码 

```java
public class ParentListBean  
{  
    @PrimaryKey  
    private String id ;  
 。。。。。。  
}  
```

Java代码

```java
public class ParentListBean  
{  
                @Column(type="blob")//指示属性的值按blob类型写入或者读取  
        private String blobname;  
        @Column(type="clob")//指示属性的值按clob类型写入或者读取  
        private String clobname;   
  
        @Column(name="name_")//指示属性名称与表字段名称映射关系，name属性对应于表中的name_字段  
        private String name;   
        @Column(dataformat="yyyy-mm-dd")//指示日期类型属性值的存储和读取转换日期格式  
        private String regdate;   
@IgnoreORMapping //忽略DB查询操作时对象属性与表字段的o/r mapping映射操作，只正对增删，改三种操作进行绑定  
private String aa;   
  
@Column(ignorebind=true)// 忽略insert，update，delete，select四种DB操作对象属性与表字段的 o/r mapping绑定操作，相当于@IgnoreORMapping和@Column(ignoreCUDbind=true)组合使用  
  
private String bb;   
  
@Column(ignoreCUDbind =true)// 忽略对象属性insert，update，delete三种DB操作的对象属性与表字段的 o/r mapping绑定操作，只针对查询操作进行绑定  
  
private String cc;   
  
  
@Column(editorparams="yyyy-MM-dd HH:mm:ss",  
            editor="com.frameworkset.platform.util.DateformatEditor",  
            ignoreCUDbind=true)//指定查询时表字段值与对象属性值的转换插件，同时忽略对象属性insert，update，delete三种DB操作的对象属性与表字段的 o/r mapping绑定操作,通过editordataformat属性指定了插件需要的日期格式  
private String dd;   
  
@Column(editordataformat="yyyy-MM-dd HH:mm:ss",  
            editor="com.frameworkset.platform.util.CommonDateformatEditor",  
            )//指定insert，update，delete，select四种DB操作对象属性与表字段的 o/r mapping绑定转换插件，通过editordataformat属性指定了插件需要的日期格式  
private String ee;   
  
}  
```

**5.相关内容**

关于bboss persistent主键生成机制的说明，请参考文档：

http://yin-bp.iteye.com/blog/407254



bboss声明式事务和注解事务参考以下文档的第82-86页：

[bbossgroups 框架培训教程.ppt](https://github.com/bbossgroups/bboss-document/blob/master/文档/bbossgroups 框架培训教程.ppt?raw=true)