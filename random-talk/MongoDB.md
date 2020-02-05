### MongoDB与内存

但凡初次接触MongoDB的人，无不惊讶于它对内存的贪得无厌，至于个中缘由，我先讲讲Linux是如何管理内存的，再说说MongoDB是如何使用内存的，答案自然就清楚了。

据说带着问题学习更有效，那就先看一个MongoDB服务器的top命令结果：

  shell> top -p $(pidof mongod)

Mem:  32872124k total, 30065320k used,  2806804k free,   245020k buffers

Swap:  2097144k total,      100k used,  2097044k free, 26482048k cached

VIRT  RES  SHR %MEM

1892g  21g  21g 69.6

这台MongoDB服务器有没有性能问题？大家可以一边思考一边继续阅读。  

先讲讲Linux是如何管理内存的:

在Linux里（别的系统也差不多），内存有物理内存和虚拟内存之说，物理内存是什么自然无需解释，虚拟内存实际是物理内存的抽象，多数情况下，出于方便性的考虑，程序访问的都是虚拟内存地址，然后操作系统会通过Page Table机制把它翻译成物理内存地址，详细说明可以参考Understanding Memory和Understanding Virtual Memory，至于程序是如何使用虚拟内存的，可以参考Playing with Virtual Memory，这里就不多费口舌了。

很多人会把虚拟内存和Swap混为一谈，实际上Swap只是虚拟内存引申出的一种技术而已：操作系统一旦物理内存不足，为了腾出内存空间存放新内容，就会把当前物理内存中的内容放到交换分区里，稍后用到的时候再取回来，需要注意的是，Swap的使用可能会带来性能问题，偶尔为之无需紧张，糟糕的是物理内存和交换分区频繁的发生数据交换，这被称之为Swap颠簸，一旦发生这种情况，先要明确是什么原因造成的，如果是内存不足就好办了，加内存就可以解决，不过有的时候即使内存充足也可能会出现这种问题，比如MySQL就有可能出现这样的情况，一个可选的解决方法是限制使用Swap：

shell> sysctl -w vm.swappiness=0

查看内存情况最常用的是free命令：

shell> free -m

​          total       used       free     shared    buffers     cached

Mem:         32101      29377       2723          0        239      25880

-/+ buffers/cache:       3258      28842

Swap:         2047          0       2047

新手看到used一栏数值偏大，free一栏数值偏小，往往会认为内存要用光了。其实并非如此，之所以这样是因为每当我们操作文件的时候，Linux都会尽可能的把文件缓存到内存里，这样下次访问的时候，就可以直接从内存中取结果，所以cached一栏的数值非常的大，不过不用担心，这部分内存是可回收的，操作系统的虚拟内存管理器会按照LRU算法淘汰冷数据。还有一个buffers，也是可回收的，不过它是保留给块设备使用的。

知道了原理，我们就可以推算出系统可用的内存是free + buffers + cached：

shell> echo $((2723 + 239 + 25880))

28842

至于系统实际使用的内存是used – buffers – cached：

shell> echo $((29377 - 239 - 25880))

3258

除了free命令，还可以使用sar命令：

shell> sar -r

kbmemfree kbmemused  %memused kbbuffers  kbcached

  3224392  29647732     90.19    246116  26070160

shell> sar -W

pswpin/s pswpout/s

​    0.00      0.00

希望你没有被%memused吓到，如果不幸言中，重读本文。

再说说MongoDB是如何使用内存的:

目前，MongoDB使用的是内存映射存储引擎，它会把数据文件映射到内存中，如果是读操作，内存中的数据起到缓存的作用，如果是写操作，内存还可以把随机的写操作转换成顺序的写操作，总之可以大幅度提升性能。MongoDB并不干涉内存管理工作，而是把这些工作留给操作系统的虚拟内存管理器去处理，这样做的好处是简化了MongoDB的工作，但坏处是你没有方法很方便的控制MongoDB占多大内存，幸运的是虚拟内存管理器的存在让我们多数时候并不需要关心这个问题。

MongoDB的内存使用机制让它在缓存重建方面更有优势，简而言之：如果重启进程，那么缓存依然有效，如果重启系统，那么可以通过拷贝数据文件到/dev/null的方式来重建缓存，更详细的描述请参考：Cache Reheating – Not to be Ignored。

有时候，即便MongoDB使用的是64位操作系统，也可能会遭遇OOM问题，出现这种情况，多半是因为限制了内存的大小所致，可以这样查看当前值：

shell> ulimit -a | grep memory

多数操作系统缺省都是把它设置成unlimited的，如果你的操作系统不是，可以这样修改：

shell> ulimit -m unlimited

shell> ulimit -v unlimited

注：ulimit的使用是有上下文的，最好放在MongoDB的启动脚本里。

  有时候，MongoDB连接数过多的话，会拖累性能，可以通过serverStatus查询连接数：

mongo> db.serverStatus().connections

每个连接都是一个线程，需要一个Stack，Linux下缺省的Stack设置一般比较大：  

mongo> db.serverStatus().connections

每个连接都是一个线程，需要一个Stack，Linux下缺省的Stack设置一般比较大：

shell> ulimit -a | grep stack

stack size              (kbytes, -s) 10240

至于MongoDB实际使用的Stack大小，可以用如下命令确认（单位：K）：

shell> cat /proc/$(pidof mongod)/limits | grep stack | awk -F 'size' '{print int($NF)/1024}'

如果Stack过大（比如：10240K）的话没有意义，简单对照命令结果中的Size和Rss：

shell> cat /proc/$(pidof mongod)/smaps | grep 10240 -A 10

所有连接消耗的内存加起来会相当惊人，推荐把Stack设置小一点，比如说1024：

shell> ulimit -s 1024

注：从MongoDB1.8.3开始，MongoDB会在启动时自动设置Stack。

  有时候，出于某些原因，你可能想释放掉MongoDB占用的内存，不过前面说了，内存管理工作是由虚拟内存管理器控制的，幸好可以使用MongoDB内置的closeAllDatabases命令达到目的：

mongo> use admin

mongo> db.runCommand({closeAllDatabases:1})

另外，通过调整内核参数drop_caches也可以释放缓存：  

shell> sysctl -w vm.drop_caches=1

平时可以通过mongo命令行来监控MongoDB的内存使用情况，如下所示：

mongo> db.serverStatus().mem:

{
    "resident" : 22346,

   "virtual" : 1938524,

  "mapped" : 962283
}

还可以通过mongostat命令来监控MongoDB的内存使用情况，如下所示：

shell> mongostat

mapped  vsize    res faults

  940g  1893g  21.9g      0

其中内存相关字段的含义是：

mapped：映射到内存的数据大小

visze：占用的虚拟内存大小

res：占用的物理内存大小

注：如果操作不能在内存中完成，结果faults列的数值不会是0，视大小可能有性能问题。

在上面的结果中，vsize是mapped的两倍，而mapped等于数据文件的大小，所以说vsize是数据文件的两倍，之所以会这样，是因为本例中，MongoDB开启了journal，需要在内存里多映射一次数据文件，如果关闭journal，则vsize和mapped大致相当。

如果想验证这一点，可以在开启或关闭journal后，通过pmap命令来观察文件映射情况：

shell> pmap $(pidof mongod)

到底MongoDB配备多大内存合适？宽泛点来说，多多益善，如果要确切点来说，这实际取决于你的数据及索引的大小，内存如果能够装下全部数据加索引是最佳情况，不过很多时候，数据都会比内存大，比如本文所涉及的MongoDB实例：

mongo> db.stats()

{

  "dataSize" : 1004862191980,

  "indexSize" : 1335929664

}

本例中索引只有1G多，内存完全能装下，而数据文件则达到了1T，估计很难找到这么大内存，此时保证内存能装下热数据即可，至于热数据是多少，取决于具体的应用，你也可以通过观察faults的大小来判断当前内存是否能够装下热数据，如果faults持续变大，就说明当前内存已经不能满足热数据的大小了。如此一来内存大小就明确了：内存 > 索引 + 热数据，最好有点富余，毕竟操作系统本身正常运转也需要消耗一部分内存。

关于MongoDB与内存的话题，大家还可以参考官方文档中的相关介绍。