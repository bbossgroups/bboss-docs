### bboss与spring中配置和引用bboss数据源和bboss dao组件方法说明

首先在项目中导入bboss 持久层包：

Xml代码

```xml
<dependency>   
    <groupId>com.bbossgroups</groupId>   
    <artifactId>bboss-persistent</artifactId>   
    <version>6.2.6</version>   
</dependency>
```

**gradle坐标**

Java代码

```java
compile 'com.bbossgroups:bboss-persistent:6.2.6'  

```

   **在bboss中引用bboss数据源：**

Xml代码

```xml
<property name="dataSource" factory-class="com.frameworkset.common.poolman.util.SQLManager" factory-method="getTXDatasourceByDBName">    
      <construction>    
          <property value="wood" />    
      </construction>    
  </property>  
```

 **在spring中引用bboss数据源：**

Xml代码

```xml
<bean id="dataSource"   
     class="com.frameworkset.common.poolman.util.SQLManager"  
     factory-method="getTXDatasourceByDBName">  
     <constructor-arg value="wood"></constructor-arg>  
 </bean>
```

**bboss ioc 配置bboss dao组件并加载sql配置文件**     

Xml代码

```xml
<property name="appbom.configsqlexecutor"          
    class="com.frameworkset.common.poolman.ConfigSQLExecutor" >  
    <construction>  
        <property value="com/s/appbom/service/appbom.xml"/>  
    </construction>  
      
</property>  
```

**spring ioc 配置bboss dao组件并加载sql配置文件**

Xml代码

```xml
<bean id="appbom.configsqlexecutor" class="com.frameworkset.common.poolman.ConfigSQLExecutor">                                    
        <constructor-arg value="com/s/appbom/service/appbom.xml" type="String" index ="1"></constructor-arg>                            
    </bean>  
```

