### bboss 持久层sql语句中一维/多维数组类型变量、list变量、map变量、bean对象变量使用说明

本文介绍bboss 持久层sql语句中一维/多维数组类型变量、list变量、map变量、bean对象变量使用方法，该功能在bboss 3.5.2版本及后续版本提供。

很高兴地告诉大家，bboss模板sql中已经可以处理对象、数组、list、map类型的变量了，bboss 能够快速分析出这些变量，并将sql语句转换为预编译sql语句执行。

先看一个处理数组的实例：

Java代码 

```java
String[] FIELDNAMES = new String[]{"ss","testttt","sdds","insertOpreation","ss556"};  
        String deleteAllsql = "delete from LISTBEAN where FIELDNAME in (#[FIELDNAMES[0]],#[FIELDNAMES[1]],#[FIELDNAMES[2]],#[FIELDNAMES[3]],#[FIELDNAMES[4]])";  
        Map conditions = new HashMap();  
        conditions.put("FIELDNAMES", FIELDNAMES);         
        SQLExecutor.deleteBean(deleteAllsql, conditions);  
```

变量格式由以前的#[aaa]扩展为以下几种格式：

#[aaa] 简单的变量属性引用

#[aaa[0]] （一维数组中的第一个元素，或者list中的第一个元素,具体取决于aaa变量是一个数组还是list对象）

#[aaa[0][1]]（二维数组中的第一维度的第二个个元素，或者list中的第一个元素的数第二个组元素或者list第第二个元素,具体取决于aaa变量是每一维度是数组还是list对象）

#[aaa[0][1]...]（多维数组中的第一维度的第二个个元素的n维元素，或者list中的第一个元素的第二个数组元素或者list第二个元素的n维元素引用,具体取决于aaa变量是每一维度是数组还是list对象）

#[aaa[key]] 引用map对象aaa中key所对应的value数据,引用map元素的等价方法#[aaa->key]

#[aaa[key][0]] 引用map对象aaa中key所对应的value的第一个元素，取决于value的类型是数组还是list,等价引用方法#[aaa->key[0]]

\#[aaa->bb] 如果aaa是一个bean对象，这个变量格式表示了对aaa对象的bb属性的引用，如果aaa是一个map对象，这个变量格式表示了对aaa对象的key为bb的元素值引用

以上就是全部的类型，每种类型可以任意组合

以上就是全部的类型，每种类型可以任意组合，例如：  

#[aaa->bb[0]]

\#[aaa[0]->bb[0]]

\#[aaa[0]->bb[0]->cc[keyname]]

\#[aaa[0]->bb->cc[keyname]]

等等

下面在看几个简单的实例：  

**1.foreach处理数组的实例**

java方法

Java代码

```java
public List<CandidateGroup> getGroupInfoByNames(String groups) {  
        try {  
            String[] groupnames = groups.split(",");  
            List<CandidateGroup> list = null;  
            if (groupnames.length > 0) {  
                Map<String, String[]> groups_ = new HashMap<String, String[]>();//定义包含变量的map对象，key为变量名称，value为变量值  
                groups_.put("groups", groupnames);//将数组groupnames设置到map对象中，key为groups，我们在后面的foreach循环中会通过groups来引用groupnames中的每个数据  
                list = executor.queryListBean(CandidateGroup.class,  
                        "selectGroupInfoByNames", groups_);//执行查询  
            }  
            return list;  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        return null;  
    }  
```

sql配置示例：

Xml代码

```xml
<property name="selectGroupInfoByNames">  
        <![CDATA[ 
                    select #foreach($filed in $groupfileds)  
                    #if($velocityCount == 0) 
                        $filed 
                    #else 
                        ,$filed 
                    #end 
                #end 
         
        from td_sm_group g where g.group_name in ( 
            #foreach($group in $groups) 
                            #if($velocityCount == 0) 
                                #[groups[$velocityCount]] 
                            #else 
                                ,#[groups[$velocityCount]] 
                            #end 
                        #end 
                        )       ]]>  
    </property>  
```

需要注意的是，需要把resources/velocity.properties文件相关配置项按照以下配置配置：

**directive.foreach.counter.name = velocityCount**

**directive.foreach.counter.initial.value = 0**

  说明：

directive.foreach.counter.name

用来定义foreach循环递增变量名称，通过velocity.properties文件中全局指定这个变量名称，默认为velocityCount，项目开发之初就要确定这个变量名称，后续不能更改。

directive.foreach.counter.initial.value

用来定义循环变量velocityCount(名称由directive.foreach.counter.name属性指定)的初始值，默认从1开始，项目开发之初应将其调整为0

**2.bean类型变量属性引用处理实例**

Java代码  

```java
@Test  
    public void beanVariableTest() throws SQLException  
    {  
        /** 
         * 删除数据，数据条件由数组FIELDNAMES，这里主要演示如果通过bean属性引用变量语法获取数据项 
         * 后台转换为预编译执行 
         */  
        insertOpera();  
        BeanVariable beanvariable = new BeanVariable();  
        beanvariable.setBean(new Bean());  
        String deleteAllsql = "delete from LISTBEAN where FIELDNAME in (#[bean->fss],#[bean->ftestttt],#[bean->fsdds]," +  
                "#[bean->finsertOpreation],#[bean->fss556])";  
        SQLExecutor.deleteBean(deleteAllsql, beanvariable);  
    }  
```

**3.List类型变量元素引用处理实例**

Java代码

```java
@Test  
    public void listVariableTest() throws SQLException  
    {  
        /** 
         * 删除数据，数据条件由list 对象FIELDNAMES提供，这里主要演示如何通过list变量语法获取数据项 
         * 后台转换为预编译执行 
         */  
        insertOpera();  
        List<String> FIELDNAMES = new ArrayList<String>();  
        FIELDNAMES.add("ss");  
        FIELDNAMES.add("testttt");  
        FIELDNAMES.add("sdds");  
        FIELDNAMES.add("insertOpreation");  
        FIELDNAMES.add("ss556");  
        String deleteAllsql = "delete from LISTBEAN where FIELDNAME in (#[FIELDNAMES[0]],#[FIELDNAMES[1]],#[FIELDNAMES[2]],#[FIELDNAMES[3]],#[FIELDNAMES[4]])";  
        Map<String,List<String> > conditions = new HashMap<String,List<String> >();  
        conditions.put("FIELDNAMES", FIELDNAMES);         
        SQLExecutor.deleteBean(deleteAllsql, conditions);  
          
    }  
```

**4.Map类型变量元素引用处理实例**

Java代码

```java
@Test  
    public void mapVariableTest() throws SQLException  
    {  
        /** 
         * 删除数据，数据条件由FIELDNAMES为名称索引的map对象中，这里主要演示如果通过map变量获取数据项 
         * 后台转换为预编译执行 
         */  
        insertOpera();  
        Map<String,String> datas = new HashMap<String,String>();  
        datas.put("sskey", "ss");  
        datas.put("testtttkey", "testttt");  
        datas.put("sddskey", "sdds");  
        datas.put("insertOpreationkey", "insertOpreation");  
        datas.put("ss556key", "ss556");  
        String deleteAllsql = "delete from LISTBEAN where FIELDNAME in " +  
                "(#[FIELDNAMES[sskey]],#[FIELDNAMES[testtttkey]],#[FIELDNAMES[sddskey]],#[FIELDNAMES[insertOpreationkey]]," +  
                "#[FIELDNAMES[ss556key]])";  
        Map conditions = new HashMap();  
        conditions.put("FIELDNAMES", datas);          
        SQLExecutor.deleteBean(deleteAllsql, conditions);  
    }  
```

  最新版本源码可以到github中下载，下载地址如下：
https://github.com/bbossgroups/bbossgroups-3.5

源码构建方法请参考文章：
[bboss 版本ant构建方法](http://yin-bp.iteye.com/blog/1462842)  