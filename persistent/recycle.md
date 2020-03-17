### bbossgroups持久层框架链接池配置优化策略之一 空闲链接回收配置

poolman.xml文件中和空闲链接回收相关的配置项如下：

Xml代码

```xml
<initialConnections>2</initialConnections>  
  
<minimumSize>2</minimumSize>  
<maximumSize>10</maximumSize>  
<maximumSoft>false</maximumSoft> 
```

Xml代码

```xml
<!--单位：秒-->  
    <skimmerFrequency>120000</skimmerFrequency>  
    <!--单位：秒  
    空闲链接回收时间，空闲时间超过指定的值时，将被回收  
    -->  
    <connectionTimeout>240000</connectionTimeout>  
```

现在对这些配置项进行说明：

1.initialConnections是连接池初始化的数据库连接数，可根据需要进行配置，这里配置为2

2.minimumSize最小空闲链接数,缺省为0，有两种配置建议：

  配为maximumSize的一半，如果maximumSize为200则minimumSize配为100,或者和initialConnections保持一致
minimumSize最好不要设置为0，免得池中的空闲连接被全部回收后，不再保持有空闲连接，也就是说为0时可能会造成连接池为空，从而失去连接池的意义。  

3.maximumSize-连接池中允许的最大链接数，连接数达到这个数值后，如果maximumSoft为false时，连接池将不再允许池中的连接增长，如果为true将会增长连接数，增长的链接数是否回收以及什么时候回收依赖于skimmerFrequency、connectionTimeout参数的配置

4.maximumSoft-当连接池中的连接个数达到maximumSize后，是否允许再创建新的connection——true：允许，缺省值 ;false：不允许 ，一般如果应用程序中不存在链接泄露的情况下，设置为false。

5.skimmerFrequency-回收空闲链接操作间隔时间,秒（s）为单位，这个时间不能太短，否则检测频率太高，影响系统性能，我们一般设置为120000秒  

6.connectionTimeout-单位：秒; 空闲链接回收时间，空闲时间超过指定的值时，将被回收;缺省为1200秒，我们设置为240000秒。


配置建议：minimumSize和initialConnections保持一致，如果initialConnections为0，会导致连接池向数据库获取物理链接的频率变高，从而导致降低系统性能（尤其是像mysql，获取链接的代价非常高，因为mysql会进行dns解析，会很慢，除非mysql关闭了该机制）。

maximumSoft为false，skimmerFrequency为120000秒，connectionTimeout为240000秒，这样配置也就是为了避免过渡频繁地回收空闲链接和初始化最小链接数,从而导致降低系统性能

以上建议纯属个人想法，如有不妥，欢迎批评指点,感兴趣的朋友可以参考我的另一篇文章：

[bbosspersistent 性能初探](http://yin-bp.iteye.com/blog/727701)。  