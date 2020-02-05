### bboss持久层查询元数据缓存机制开启方法

 bboss持久层为了提升数据库查询操作性能，提供了对查询字段信息、字段对应的java filed名称信息等元数据（后文统称为查询元数据）进行缓存的机制，bboss持久层为数据源额外提供了控制参数cachequerymetadata来控制是否缓存这些查询元数据。cachequerymetadata为true时开启缓存机制，为false时关闭缓存机制，默认为true。

 cachequerymetadata的设置方法。在poolman.xml文件的datasource元素中进行配置，实例如下：

Xml代码

```xml
<datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>druid_datasource_jndiname</jndiName>  
        <datasourceFile>druid-mysql.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
        <cachequerymetadata>true</cachequerymetadata>  
    </datasource>  

```

对于缓存查询元数据的缓存容器和缓存key的说明：

缓存容器，根据程序使用的查询组件有不同的容器

ConfigSQLExecutor组件：

一个ConfigSQLExecutor组件加载一个xml配置文件，同时也会为这个ConfigSQLExecutor组件创建一个查询元数据缓存容器，当xml文件中的sql语句被修改后，ConfigSQLExecutor组件会重新loadxml文件的sql，同时也会重置对应的查询元数据缓存容器。这个缓存容器中缓存查询元数据的key，可能是配置的sql对应的别名，也可能是经过bboss解析后最终生成的sql语句。

例如下面的sql就是以getCopyTaskReadUsers作为查询元数据的缓存key：

Xml代码 

```xml
<property name="getCopyTaskReadUsers">  
        <![CDATA[ 
        select ID, 
                        copyid, 
                        COPORG, 
                        COPER, 
                        PROCESS_ID, 
                        PROCESS_KEY, 
                        BUSINESSKEY, 
                        COPYTIME, 
                        READTIME, 
                         ACT_ID, 
                        act_name, 
                        act_instid, 
                        COPERCNName from td_wf_hi_copytask  where act_instid=? and COPER is not null  ORDER by READTIME asc 
        ]]>  
    </property> 
```

getCopyTaskReadUsers之所以能作为缓存key是因为对应的sql是一条确定的sql语句，没有velocity动态变量（类似$varname格式的变量），也没用velocity动态逻辑判断和foreach循环语句。下面的sql就不能用名称getUserReaderCopyTasks作为缓存key：

Xml代码

```xml
<property name="getUserReaderCopyTasks">  
        <![CDATA[ 
        select ID, 
                        copyid, 
                        COPORG, 
                        COPER, 
                        PROCESS_ID, 
                        PROCESS_KEY, 
                        BUSINESSKEY, 
                        COPYTIME, 
                        READTIME, 
                         ACT_ID, 
                        act_name, 
                        act_instid, 
                        COPERCNName from td_wf_hi_copytask  where 1=1 and  
                        #if(!$isAdmin)                       
                            COPER=#[user]                        
                        #end 
                        #if($process_key && !$process_key.equals(""))  
                            and PROCESS_KEY = #[process_key] 
                        #end     
                        #if($businesskey && !$businesskey.equals(""))  
                            and BUSINESSKEY = #[businesskey] 
                        #end 
                        ORDER by READTIME asc    
        ]]>  
    </property>  
```

  因为getUserReaderCopyTasks对应的sql根据实际运行的参数不同而不同，所以必须以最终生成的sql作为元数据的查询元数据缓存key。

SQLExecutor组件/PreparedDBUtil组件：由于这两个组件直接使用sql语句进行查询，所以他们统一使用一个全局查询元数据容器，只有在jvm退出时，这个全局容器才会被销毁。这个容器缓存查询元数据是以sql语句作为缓存key。

缓存的元数据全部采用软缓存SoftReference,在jvm内存不够时会进行自动回收。

当以sql作为查询元数据缓存key时，如果sql语句中包含查询通配符*时，如果在数据库表中增加了字段，但是应用没有重启，可能会导致or mapping机制不能正确工作，所以如果数据在有以下查询sql语句的情况下：select n.*,t.name from a n,b m where n.id = m.fid,往a表中增加了字段，需要重启一下应用，否则缓存的元数据会没有包含新的a表中新加的字段，导致o/r mapping不能正确工作，或者将n.*改为查询具体的字段，也不会存在问题，例如：n.id,n.name等等。