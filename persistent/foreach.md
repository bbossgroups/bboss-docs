### bboss 动态sql使用foreach循环示例

本文介绍bboss 动态sql使用foreach循环示例。切入正题。

在sql配置文件中配置的sql语句有时需要用到foreach循环控制语句以及循环计数器velocityCount，以便遍历外部传入的list数据。在这里我们简单地介绍这个功能。

首先看看sql配置文件中配置的sql语句：

Xml代码

```xml
<property name="updateLkYjZt">  
  <![CDATA[ 
   update dtjf.t_zt_zdry_cklkyjqbxx y set y.sjqszt = '5' 
  where y.yjlx='2'  
  #if($ldxxbhs && $ldxxbhs.size() > 0) 
  and y.ldxxbh in ( 
  #foreach($ldxxbh in $ldxxbhs) 
        #if($velocityCount == 0),#end #[ldxxbhs[$velocityCount]] 
             
   #end       
  ) 
  ]]>  
</property>  
```

在sql语句中包含了foreach循环控制语句和循环控制变量velocityCount：

Xml代码

```xml
#foreach($ldxxbh in $ldxxbhs)  
       #if($velocityCount == 0),#end #[ldxxbhs[$velocityCount]]              
  #end     
```

其中的变量$ldxxbh 保存了每次迭代获取到的list元素的值，变量$ldxxbhs表示一个list集合，可以通过三种方式设置:bean的get方法，map对象，sqlparams对象；详情请参考文章《[bbossgroups 持久层模板sql变量参数设置的三种方式](http://yin-bp.iteye.com/blog/1173652)》，本例采用map方式设定这个list类型变量$ldxxbhs。

需要注意的是，需要把resources/velocity.properties文件相关配置项按照以下配置配置：

**directive.foreach.counter.name = velocityCount**

**directive.foreach.counter.initial.value = 0**

说明：

directive.foreach.counter.name

用来定义foreach循环递增变量名称，通过velocity.properties文件中全局指定这个变量名称，默认为velocityCount，项目开发之初就要确定这个变量名称，后续不能更改。

directive.foreach.counter.initial.value

用来定义循环变量velocityCount(名称由directive.foreach.counter.name属性指定)的初始值，默认从1开始，项目开发之初应将其调整为0

下面我们看看java通过ConfigSQLExcutor对象executor来执行这个带foreach循环控制逻辑的sql语句代码：

Java代码

```java
public void updateLkYjZt(List<String> ldxxbhs) {  
                try {  
            Map datas = new HashMap();  
datas.put("ldxxbhs",ldxxbhs);  
executor.updateBean("updateLkYjZt", datas);  
  
        } catch (SQLException e) {  
            e.printStackTrace();  
        }  
    }  
```

其中executor.updateBean("updateLkYjZt", datas)中的参数"updateLkYjZt"对应sql配置**<property name="updateLkYjZt"**>中name属性的值，

Map对象datas中设置了key为“ldxxbhs”，值为list类型的变量ldxxbhs，foreach循环控制语句中通过key “ldxxbhs”来引用这个list对象，然后完成循环控制语句的执行。