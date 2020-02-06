### bboss持久层sql语句输出到log4j日志文件设置

poolman.xml数据源datasource中配置showsql开关为true:

Xml代码 

```xml
<datasource>  
  
  。。。。。。  
   <showsql>true</showsql>  
。。。。。  
 </datasource>   
```

同时将log4j.properties中日志级别设定为INFO，如果是bboss 5.0.3.6.2之前的版本设置为debug，在log4j.properties增加bboss包路径配置：

log4j.category.com.frameworkset = INFO, COMMON_FILE

log4j.category.org.frameworkset = INFO, COMMON_FILE



log4j.appender.COMMON_FILE=org.apache.log4j.RollingFileAppender

log4j.appender.COMMON_FILE.Threshold=INFO

log4j.appender.COMMON_FILE.File=common.log

log4j.appender.COMMON_FILE.Append=true

log4j.appender.COMMON_FILE.MaxFileSize=10240KB

log4j.appender.COMMON_FILE.MaxBackupIndex=10

log4j.appender.COMMON_FILE.layout=org.apache.log4j.PatternLayout

log4j.appender.COMMON_FILE.layout.ConversionPattern=[%d{yyyy-MM-dd HH:mm:ss}][%p]%x[%c] %m%n



这样sql语句就会输出到日志文件common.log中了，同时如果还需要输出到控制台，只需要在log4j.properties文件的头部增加以下配置即可：

log4j.rootLogger=INFO,CONSOLE



og4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender

log4j.appender.CONSOLE.Threshold=INFO

log4j.appender.CONSOLE.Target=System.out

log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout

log4j.appender.CONSOLE.layout.ConversionPattern=[%d{yyyy-MM-dd HH:mm:ss}][%p]%x[%c] %m%n