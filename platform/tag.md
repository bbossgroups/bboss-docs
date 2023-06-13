### 平台字典标签使用方法

平台字典标签使用方法：

导入标签定义文件：

<%@ taglib uri="/WEB-INF/dictionary.tld" prefix="dict"%>

**select标签使用方法：**

国际化案例

<dict:select type="sex" name="userSex" textValueCode="s.pdp.common.operation.select" textNAN="-1"  extend=" onclick='alert(1) ' class='blue'"/>

本地化写法：

<dict:select type="sex" name="userSex" textValue="请选择性别" textNAN="-1"  **extend=" onclick='alert(1) ' class='blue'"**/>

extend是一个自由属性，可以写任意html脚本，其内容将会直接增加到实际的select元素上面，例如：上面的写法的实际效果为：

```html
<select name="userSex" onclick='alert(1) ' class='blue'>
  <option value="-1">请选择性别</option>
  <option value="M">男</option>
  <option value="F">女</option>
</select>
```

**和list标签结合使用**

使用字典标签需要导入字典标签定义文件

<%@ taglib uri="/WEB-INF/dictionary.tld" prefix="dict"%>

标签使用实例-select

<pg:list actual="${users}">

<dict:select type="marglogrt" name="marglogrt" colName="userType"

textValue="请选择" style="width:115px"/>



<dict:select type="marglogrt" name="marglogrt" colName="userType"

textValue="请选择" extend=" style=’width:115px’ width=’’ onclick=’aa()’"/>

标签使用实例-checkbox

<dict:checkbox type="marglogrt" name="marglogrt" colName="userType"

textValue="请选择" style="width:115px"/>



<dict:checkbox type="marglogrt" name="marglogrt" colName="userType"

extend=" style=’width:115px’ width=’’ onclick=’aa()’"/>

标签使用实例-radio

<dict:radio type="marglogrt" name="marglogrt" colName="userType"

textValue="请选择" style="width:115px"/>



<dict:radio type="marglogrt" name="marglogrt" colName="userType"

extend=" style=’width:115px’ width=’’ onclick=’aa()’"/>

标签使用实例- itemname

<dict:itemname type="marglogrt" colName="userType" />

标签使用实例- itemvalue

<dict:itemvalue type="marglogrt" colName="userType" />

</pg:list>

**字典数据java接口**

bboss-cms（老版本平台）

com.frameworkset.dictionary.DataManagerFactory

Data data = DataManagerFactory.getDataManager().getData("marglogrt");

Data数据对象包含了字典的所有数据：

private String dataId;

private String name;

private List items;

private List allitems;

private String parent;

private String description;

private Map itemsIdxByName;

private Map itemsIdxByValue;

新版平台

Params data = ParamsHandler.getParams("default", "marglogrt");

Params数据对象包含了字典的所有数据：

private String dataId;

private String name;

private List items;

private List allitems;

private String parent;

private String description;

private Map itemsIdxByName;

private Map itemsIdxByValue;