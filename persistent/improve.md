### bboss 持久层重大改进

bboss持久层最大特色就是其对动态sql语句的良好支持，这个机制是基于velocity模板引擎的来实现的：

Java代码

```java
Velocity.evaluate(context, out, sqlName, sql);  
```

Velocity.evaluate这个方法存在一个非常严重的性能隐患，那就是这个方法没有对解析出来的sql模板语法结构树做任何的缓存机制，每次都会硬解析sql语句，并且在解析sql语句时会向velocity的模板解析器池中获取一个池化的解析器，这样在高并发情况下会频繁地获取解析其池的共享锁，导致系统性能下降。为了避免出现该问题，最近对bboss持久层和velocity模板引擎都做了功能扩展，彻底解决了这个问题，非常完美第实现了对模板sql语法结构树的缓存功能，每条sql语句只硬解析一次，如果sql语句是通过xml配置文件来管理则按照每个文件对应一个缓存器，提高缓存的检索速度和命中率，其他非配置文件的sql语句也通过系统全局默认的缓存容器进行管理。

同时对生成sql算法进行优化，对velocity分析出的词法进行变量和句法识别，如果sql语句不包含任何velocity变量和句法，则将sql语句当作普通字符串处理，持久层后续不再针对该sql语句做任何和velocity的相关的操作（也就是说将sql语句当成普通的sql语句处理），这样也可以大幅提升系统性能。

在bboss中新增了bboss-velocity工程，这个工程就是bboss对velocity模板引擎的改造版本（基于velocity 1.7改造），改造后的velocity在包路径前面统一添加了“bboss.”前缀，避免与原始的velocity包的冲突。

上述改造是针对最新的[bboss3.6.2](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.2)分支版本实施，已经经过初步回归测试并升级到相关的实际项目中，目前未出现任何升级版本导致的问题，完全兼容先前的应用程序。而且经实践验证bboss持久层的性能同时得到大幅提升，达到了改造的最终目标。

升级bboss3.6.2需要更新以下jar文件：

bboss-aop.jar

bboss-velocity.jar（新增）

bboss-mvc.jar

frameworkset-pool.jar

frameworkset-util.jar

bboss构建方法：
http://yin-bp.iteye.com/blog/1462842

bboss3.6.2源码下载地址：
[bboss3.6.2](https://github.com/bbossgroups/bbossgroups-3.5/tree/bboss3.6.2)