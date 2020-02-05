##   bboss mvc json插件设置日期类型格式方法

​       一般的json请求都有返回日期类型的数据，bboss mvc json插件在不指定日期格式dateformat的情况下，默认将日期类型的数据转换为长整型的数据，我们可以通过以下方式制定全局的日期转换格式：

修改bboss-mvc.xml配置，实例如下： 

**Xml代码** 

```xml
<property class="org.frameworkset.http.converter.json.MappingJacksonHttpMessageConverter">  
                    <property name="objectMapper" class="com.fasterxml.jackson.databind.ObjectMapper">  
                        <property name="dateFormat" class="java.text.SimpleDateFormat">  
                            <construction>    
                            <property value="yyyy-MM-dd HH:mm:ss"/>  
                        </construction>  
                        </property>  
                    </property>  
                </property>  
```

