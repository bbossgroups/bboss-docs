### 扩展bboss ioc语法实现自定义的ioc依赖注入功能

[bboss ioc](http://yin-bp.iteye.com/blog/1434626)配置语法简洁而严谨，提供了强大的依赖注入功能，而且开发人员可以任意为ioc元素property增加扩展属性，从而实现自定义的ioc依赖注入功能。业界其他ioc框架自定义扩展ioc语法，可能需要编写xsd描述文件，bboss ioc很容易扩展且不需要编写xsd文件，本文举例说明如何实现bboss中的自定义ioc依赖注入功能。

实现这个功能需要三个步骤：

1.定义好ioc依赖注入插件，插件必须实现接口：

org.frameworkset.spi.assemble.plugin.IocPlugin<T,R>

Java代码

```java
public interface IocPlugin<T,R> {  
    /** 
     * 返回扩展ioc机制生成的对象实例 
     * @param object 输入的ioc元素对象,类型范围为：Pro,ProMap,ProList,ProArray,ProSet 
     * @param callcontext ioc依赖注入上下文，用来提供对循环依赖注入支持的 
     * @return 
     */  
    public R ioc(T object, CallContext callcontext);  
  
}  
```

2.利用写好的插件编写ioc xml文件

3.定义一个加载写好的ioc xml文件的ioc容器并获取通过自定义ioc机制定义的组件

接下来举例说明上述三个步骤，这个示例的功能很简单：定义了几个excel导入模板，通过ioc语法来描述这些模板结构信息，然后通过ioc依赖注入插件来将这些excel模板信息解析成一个Map<String, ExcelTemplate>对象，然后利用模板信息实现一个通用的excel导入功能。

**第一步 编写ioc注入插件以及依赖的excel模板结构po对象**

ioc注入插件-ExcelIOCPlugin 接口方法接收一个ProMap结构，其中包含了excel导入模板map数据结构，这些信息通过ioc xml语法描述

Java代码

```java
package org.frameworkset.spi.assemble.plugin;  
  
import java.util.ArrayList;  
import java.util.HashMap;  
import java.util.Iterator;  
import java.util.List;  
import java.util.Map;  
import java.util.Set;  
  
import org.frameworkset.spi.CallContext;  
import org.frameworkset.spi.assemble.Pro;  
import org.frameworkset.spi.assemble.ProList;  
import org.frameworkset.spi.assemble.ProMap;  
  
public class ExcelIOCPlugin implements IocPlugin<ProMap, Map<String, ExcelTemplate>>{  
  
    @Override  
    public Map<String, ExcelTemplate> ioc(ProMap promap, CallContext callcontext) {  
        Map<String, ExcelTemplate> excelTemplates = new HashMap<String, ExcelTemplate>();  
        Set<Map.Entry<String, Pro>> entries = promap.entrySet();  
        Iterator<Map.Entry<String, Pro>> it = entries.iterator();  
        while (it.hasNext()) {  
            Map.Entry<String, Pro> e = it.next();  
            String name = e.getKey();  
            Pro pro = e.getValue();  
            String templatepath = pro.getStringExtendAttribute("templatepath");  
  
            ExcelTemplate template = new ExcelTemplate();  
            template.setName(name);  
            template.setStarrow(pro.getIntExtendAttribute("statrow", 1));  
            template.setEndrow(pro.getIntExtendAttribute("endrow", -1));  
            template.setTemplatepath(templatepath);  
            excelTemplates.put(name, template);  
  
            if(!name.equals("exceltemplatefile4"))//根据ioc元数据手动构建execl模板的单元格信息  
            {  
                List<ExcelCell> cellList = new ArrayList<ExcelCell>();  
                ProList templateCellList = pro.getList();  
      
                for (int i = 0; i < templateCellList.size(); i++) {  
      
                    Pro cellPro = (Pro) templateCellList.get(i);  
      
                    ExcelCell cellBean = new ExcelCell();  
                    cellBean.setCellpostion(cellPro.getIntExtendAttribute("cellpostion"));  
                    cellBean.setJavaFiledName(cellPro.getStringExtendAttribute("javaFiledName"));  
                    cellBean.setCelltype(cellPro.getStringExtendAttribute("celltype"));  
                    cellList.add(cellBean);  
                }  
                template.setCells(cellList);  
            }  
            else//利用bboss ioc原生功能自动构建execl模板的单元格信息  
            {  
                 @SuppressWarnings("unchecked")  
                List<ExcelCell> cellList = (List<ExcelCell>) pro.getBeanObject(callcontext);  
                 template.setCells(cellList);  
            }  
        }  
        return excelTemplates;  
    }  
  
}  
```

ExcelTemplate模板信息类-包含了execl的数据起始行和数据结束行以及单元格信息列表等

Java代码

```java
package org.frameworkset.spi.assemble.plugin;  
  
import java.util.List;  
  
public class ExcelTemplate {  
    String name;  
  
    String templatepath;  
  
    int starrow;  
  
    int endrow = -1;  
  
    List<ExcelCell> cells;  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public String getTemplatepath() {  
        return templatepath;  
    }  
  
    public void setTemplatepath(String templatepath) {  
        this.templatepath = templatepath;  
    }  
  
    public int getStarrow() {  
        return starrow;  
    }  
  
    public void setStarrow(int starrow) {  
        this.starrow = starrow;  
    }  
  
    public int getEndrow() {  
        return endrow;  
    }  
  
    public void setEndrow(int endrow) {  
        this.endrow = endrow;  
    }  
  
    public List<ExcelCell> getCells() {  
        return cells;  
    }  
  
    public void setCells(List<ExcelCell> cells) {  
        this.cells = cells;  
    }  
}  

```

excel单元格对象-包含单元格位置、单元格数据类型、单元格对应的java值对象属性名称

Java代码 

```java
package org.frameworkset.spi.assemble.plugin;  
  
public class ExcelCell {  
    private int cellpostion;  
  
    private String javaFiledName;  
  
    private String celltype;  
  
    public int getCellpostion() {  
        return cellpostion;  
    }  
  
    public void setCellpostion(int cellpostion) {  
        this.cellpostion = cellpostion;  
    }  
  
      
  
    public String getCelltype() {  
        return celltype;  
    }  
  
    public void setCelltype(String celltype) {  
        this.celltype = celltype;  
    }  
  
    public String getJavaFiledName() {  
        return javaFiledName;  
    }  
  
    public void setJavaFiledName(String javaFiledName) {  
        this.javaFiledName = javaFiledName;  
    }  
}  
```

**第二步 定义ioc xml文件-excelFieldInfo.xml**

excelFieldInfo.xml存放在包路径org/frameworkset/spi/assemble/plugin下面，内容为：

Xml代码 

```xml
<?xml version="1.0" encoding="UTF-8"?>  
  
<properties>  
    <property name="excelMapping" iocplugin="org.frameworkset.spi.assemble.plugin.ExcelIOCPlugin">  
        <map componentType="bean">   
            <property name="exceltemplatefile1" templatepath="/usr/local/exceltemplates/file1.xlsx" statrow="1" endrow="1000">  
                <list componentType="bean">   
                    <property cellpostion="1" javaFiledName="companyname" celltype="VARCHAR"/>   
                    <property cellpostion="2" javaFiledName="age" celltype="INTEGER"/>   
                    <property cellpostion="3" javaFiledName="sex" celltype="VARCHAR"/>   
                    <property cellpostion="4" javaFiledName="description" celltype="VARCHAR"/>   
                </list>    
            </property>     
            <property name="exceltemplatefile2" templatepath="/usr/local/exceltemplates/file2.xlsx" statrow="1" endrow="1000">  
                <list componentType="bean">   
                    <property cellpostion="1" javaFiledName="factoryname" celltype="VARCHAR"/>   
                    <property cellpostion="2" javaFiledName="createyear" celltype="DATE"/>   
                    <property cellpostion="3" javaFiledName="address" celltype="VARCHAR"/>   
                    <property cellpostion="4" javaFiledName="worksers" celltype="VARCHAR"/>   
                </list>    
            </property>     
            <property name="exceltemplatefile3" templatepath="/usr/local/exceltemplates/file3.xlsx" statrow="1" endrow="1000">  
                <list componentType="bean">   
                    <property cellpostion="1" javaFiledName="product" celltype="VARCHAR"/>   
                    <property cellpostion="2" javaFiledName="totalcount" celltype="INTEGER"/>   
                    <property cellpostion="3" javaFiledName="name" celltype="VARCHAR"/>   
                    <property cellpostion="4" javaFiledName="description" celltype="VARCHAR"/>  
                </list>    
            </property>     
              
            <property name="exceltemplatefile4" templatepath="/usr/local/exceltemplates/file3.xlsx" statrow="1" endrow="1000">  
                <list componentType="bean">   
                    <property class="org.frameworkset.spi.assemble.plugin.ExcelCell" f:cellpostion="1" f:javaFiledName="product" f:celltype="VARCHAR"/>   
                    <property class="org.frameworkset.spi.assemble.plugin.ExcelCell" f:cellpostion="2" f:javaFiledName="totalcount" f:celltype="INTEGER"/>   
                    <property class="org.frameworkset.spi.assemble.plugin.ExcelCell" f:cellpostion="3" f:javaFiledName="name" f:celltype="VARCHAR"/>   
                    <property class="org.frameworkset.spi.assemble.plugin.ExcelCell" f:cellpostion="4" f:javaFiledName="description" f:celltype="VARCHAR"/>  
                </list>    
            </property>     
        </map>   
    </property>  
      
    <property name="POIExcelService" class="org.frameworkset.spi.assemble.plugin.POIExcelService" f:excelTemplates="attr:excelMapping"/>  
      
      
</properties>  
```

内容说明：

组件excelMapping配置了ioc依赖注入插件：

iocplugin="org.frameworkset.spi.assemble.plugin.ExcelIOCPlugin"

通过插件ExcelIOCPlugin会将excelMapping转换为一个Map<String, ExcelTemplate>对象，这个对象会被注入给业务组件POIExcelService的excelTemplates属性。

POIExcelService组件的实现类如下：

Java代码 

```java
package org.frameworkset.spi.assemble.plugin;  
  
import java.io.IOException;  
import java.io.InputStream;  
import java.util.List;  
import java.util.Map;  
/** 
 * 本实例演示ioc注入插件的使用方法，通过ioc注入插件来扩展ioc依赖注入功能，实现自己的ioc依赖注入功能 
 * @author yinbp 
 * 
 */  
public class POIExcelService {  
    /** 
     * 存储各种execl模板信息，模板的映射信息全部都设置在ioc配置文件中 
     */  
     private Map<String, ExcelTemplate> excelTemplates;  
       
    /** 
     * 根据传入的excel流，将其中的数据根据对应的excel模板信息类将数据解析出来，形成beanType对象记录列表返回 
     *  
     * @param file 
     * @return 
     * @throws IOException 
     */  
    public <T> List<T> parseHSSFMapList(InputStream in,Class<T> beanType,String templatename) throws IOException   
    {  
         ExcelTemplate template = excelTemplates.get(templatename);//获取excel模板  
         //根据模板，从excel输入流in对象中获取excel文件中包含的所有记录形成一个List<beanType>的集合并返回：  
         List<beanType> datas = .....;  
        return datas ;  
    }  
}  

```

**第三步 验证功能-编写和执行测试用例**

Java代码 

```java
package org.frameworkset.spi.assemble.plugin;  
  
import java.io.IOException;  
  
import org.frameworkset.spi.BaseApplicationContext;  
import org.frameworkset.spi.DefaultApplicationContext;  
import org.junit.Test;  
  
public class TestIocPlugin {  
    @Test  
    public void testiocplugin() throws IOException  
    {  
          BaseApplicationContext excelMapping = DefaultApplicationContext.getApplicationContext("org/frameworkset/spi/assemble/plugin/excelFieldInfo.xml");//定义ioc容器  
          POIExcelService serice = excelMapping.getTBeanObject("POIExcelService", POIExcelService.class);//获取业务组件，ioc容器会通过ioc插件解析excelMapping组件元素并返回一个Map<String, ExcelTemplate>类型对象  
          List datas = serice.parseHSSFMapList(in, dataClass,"exceltemplatefile1");//执行业务方法  
            
    }  
  
}  
```

相关测试用例源码github获取地址：

https://github.com/bbossgroups/bbossgroups-3.5/tree/master/bboss-core/test/org/frameworkset/spi/assemble/plugin