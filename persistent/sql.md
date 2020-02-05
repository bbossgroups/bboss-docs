##   bboss持久层公共sql片段定义和引用方法说明

​        在配置sql语句时，经常会碰到多条sql语句里面使用同样的sql条件或者sql片段的情况，为了便于维护和提高配置的简洁性，一般会将这些公共部分剥离出来，配置成独立的sql片段，然后在所有需要的地方引入即可。下面介绍在bboss持久层框架里面如何定义和引用公共sql片段。
首先在项目中导入bboss 持久层包：  

maven坐标
<dependency>
    <groupId>com.bbossgroups</groupId>
    <artifactId>bboss-persistent</artifactId>
    <version>5.0.7.5</version>
</dependency>
compile 'com.bbossgroups:bboss-persistent:5.0.7.5'

直接看实例：

Xml代码

```xml
定义公共sql片段：queryOrgmanagerRoleIDs  
<property name="queryOrgmanagerRoleIDs">  
        <![CDATA[  
            select role_id from td_sm_role where role_name in ('orgmanager','orgmanagerroletemplate') 
        ]]>  
    </property>  
通过@{}语法引用公共sql片段：queryOrgmanagerRoleIDs  
    <property name="removeUserRoles">  
        <![CDATA[  
            delete from td_sm_userrole where user_id = ? and role_id not in (@{queryOrgmanagerRoleIDs}) 
        ]]>  
    </property>  
<property name="removeGroupRoles">  
        <![CDATA[  
            delete from td_sm_grouprole where group_id = ? and role_id not in (@{queryOrgmanagerRoleIDs}) 
        ]]>  
    </property>  
```

  @{queryOrgmanagerRoleIDs}是sql片段引用语法，其中**片段sql queryOrgmanagerRoleIDs一定要在引用这个片段的sql之前定义**，queryOrgmanagerRoleIDs对应的sql片段也可以定义在外部属性文件中。

@{queryOrgmanagerRoleIDs}sql片段引用语法一定要和其他的sql绑定变量区分开来：
\#[varname] 这个是sql绑定变量语法
$varname 这个是sql语句值替换变量语法
${varname} 这个是非sql配置ioc文件引用外部属性变量的语法 参考文档：http://yin-bp.iteye.com/blog/2325602  