### bboss 将内容为GBK编码文件转UTF-8工具介绍

[bboss 3.6](https://github.com/bbossgroups/bbossgroups-3.5/zipball/master)中新增了文件内容字符编码集自动扫描和编码转换工具。本文介绍详细使用方法对应的组件：[bboss.org.mozilla.intl.chardet.UTF8Convertor](https://github.com/bbossgroups/bbossgroups-3.5/blob/61d31341c673f9bb85927ef0ae6a04b693a8d91b/bboss-util/src-charset/bboss/org/mozilla/intl/chardet/UTF8Convertor.java)

3.6版本尚未发布，不过可以在下载最新的源码，自行构建jar包升级

组件的详细用法如下：

Java代码

```java
UTF8Convertor convertor = new UTF8Convertor();  
        //转换文件字符编码GBK,GB2312,gb18030为UTF-8  
        convertor.convertCharsetToUtf_8("D:\\workspace\\smc-desktop");  
          
        //转换文件字符编码GBK,GB2312,gb18030为UTF-8,指定忽略的目录名称，指定要转换的文件类型  
        convertor.convertCharsetToUtf_8("D:\\workspace\\smc-desktop",  
                new String[]{".svn","classes",".settings","文档","dbinit-system","distrib","doc","lib","lib-client","lib-compile"},  
                new String[]{".jsp",".java",".js",".css",".txt",".properties",".html",".htm",".tld",".vm",".xml"});  
          
        //获取文件内容编码集  
         String charset = convertor.takefilecharset(new File("D:\\workspace\\smc-desktop/src-sys/com/frameworkset/platform/sysmgrcore/purviewmanager/PurviewManagerOrgTree.java"));  
        //打印没有精确识别出字符集的文件信息  
         System.out.println(convertor.getUnknown()); 
```

