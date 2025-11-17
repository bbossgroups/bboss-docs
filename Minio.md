# Mino客户端组件使用介绍

bboss提供一个简单实用的OSS对象存储库Mino操作组件，基于Mino java客户端封装，可以非常方便地实现：

- Mino数据源管理（初始化和销毁），支持多数据源管理
- 操作和访问Minio进行文件上传、下载、删除以及遍历等操作

![](images\minio.png)

两个主要组件：MinioHelper和Minio

```java
org.frameworkset.nosql.minio.MinioHelper  //用于管理Mino数据源（支持多数据源管理），创建和获取单实例多线程安全的Minio对象
```

MinioHelper用于管理Mino数据源（支持多数据源管理），创建和获取单实例多线程安全的Minio对象，支持多Minio数据源

```java
Minio minio = MinioHelper.getMinio("test");//获取指定名称数据源的Minio，单实例多线程安全

```


通过minio提供的方法执行所有的Minio操作。本文示例代码对应的源文件：

Github地址 https://github.com/bbossgroups/elasticsearch-file2ftp/blob/main/src/main/java/org/frameworkset/elasticsearch/imp/minio/MinioTest.java

码云地址 https://gitee.com/bboss/elasticsearch-file2ftp/blob/main/src/main/java/org/frameworkset/elasticsearch/imp/minio/MinioTest.java



## 1.导入bboss Minio组件

导入bboss Minio和Mino客户端包，版本号均以官方发布最新版本号为准：

gradle

```java
compile 'com.bbossgroups:bboss-data:6.3.6' 
```

maven

```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>6.3.6</version>  
</dependency>  
```
导入Mino客户端包：需排除不必要的依赖包，避免冲突
gradle

```java
api (group: 'io.minio', name: 'minio', version: '8.5.17'){
    exclude group: 'com.fasterxml.jackson.jaxrs', module: 'jackson-jaxrs-json-provider'
    exclude group: 'com.fasterxml.jackson.dataformat', module: 'jackson-dataformat-csv'
    exclude group: 'com.fasterxml.jackson.module', module: 'jackson-module-scala_2.12'
    exclude group: 'com.fasterxml.jackson.datatype', module: 'jackson-datatype-jdk8'
    exclude group: 'com.fasterxml.jackson.core', module: 'jackson-databind'
    exclude group: 'com.fasterxml.jackson.core', module: 'jackson-annotations'
    exclude group: 'com.fasterxml.jackson.core', module: 'jackson-core'
}
```
maven 自行参考gradle排除不必要的依赖

```xml
<dependency>  
    <groupId>io.minio</groupId>  
    <artifactId>minio</artifactId>  
    <version>8.5.17</version>  
</dependency>  
```


## 2.bboss Minio组件使用

### 2.1 初始化和关闭Minio数据源

#### 初始化Minio数据源

通过以下代码初始化了一个名称为chan_fqa的Minio数据源，后续的用该名称来引用对应的Minio数据源来执行各种Minio操作。

```java
        //1. 初始化Minio数据源chan_fqa，用来操作Minio数据库，一个Minio数据源只需要定义一次即可，后续通过名称chan_fqa反复引用，多线程安全
        // 可以通过以下方法定义多个Minio数据源，只要name不同即可，通过名称引用对应的数据源
        MinioConfig minioConfig = new MinioConfig();

        minioConfig.setEndpoint("http://172.24.176.18:9000");

        minioConfig.setName("miniotest");
        minioConfig.setAccessKeyId("N3XNZFqSZfpthypuoOzL");
        minioConfig.setSecretAccesskey("2hkDSEll1Z7oYVfhr0uLEam7r0M4UWT8akEBqO97");
        minioConfig.setConnectTimeout(5000);
        minioConfig.setReadTimeout(5000);
        minioConfig.setWriteTimeout(5000);

        minioConfig.setMaxFilePartSize(10*1024*1024*1024);
        boolean result = MinioHelper.init(minioConfig);//启动初始化Minio数据源
```

#### 关闭Minio数据源

可以通过以下方法关闭Minio数据源并释放资源

通过数据源名称关闭

```java
MinioHelper.shutdown("chan_fqa");
```

通过ResourceStartResult参数关闭  

```java
MinioHelper.shutdown(MinioStartResult minioStartResult);
```

### 2.2 使用Minio创建bulket目录

createBucketf方法判断bulket是否存在，如果不存在则创建bulket

```java
String bucket = "user_header_imgs";
minio.createBucket(bucket);
```

### 2.3 使用Minio上传文件

使用Minio上传文件，返回自动创建的key或者提取指定的key，后续可以通过key获取bucket下的文件。如果对应的key已经存在则修改文件，否则创建文件：

```java
        
        try {
        
            long s = System.currentTimeMillis();
          	String bucket = "user_header_imgs";  
            String key = minio.uploadObject("C:/data/filedown/xxxaaaa.txt",bucket);//保存文件，返回自动创建的key，可以通过key获取bucket下创建的文件
            //String key = "xxxaaaa";
            //key = minio.uploadObject("C:/data/filedown/xxxaaaa.txt",bucket,key);//指定key，如果不指定，自动生成一个key
            long e = System.currentTimeMillis();
           
             
        } catch (Exception e) {
            throw new Exception("Send File failed:",e);
        }
```
minio提供了多种上传文件到Minio服务的方法，具体查看接口方法即可。
### 2.4 使用Minio获取文件

可以通过key获取bucket下的文件，并写入fileName对应的文件：

```java
String bucket = "user_header_imgs";
String key = "xxxaaaa";
String fileName = "/data/imgs/jack.jpg";
minio.downloadObject(bucket,key,fileName);
```

 minio提供了一系列获取oss对象的方法，可以查看接口定义，选择合适的方法实现获取oss对象内容。

### 2.5 删除文件对象

```java
String bucket = "user_header_imgs";
String key = "xxxaaaa";
minio.deleteOssFile(bucket,key);//bucket,  key
```

### 2.6 获取原生MinioClient

```java
MinioClient minioClient = minio.getMinioClient()
```

### 2.7 在ETL中的应用

[bboss etl&流处理](https://esdoc.bbossgroups.com/#/db-es-tool)框架中的文件输出插件，可以非常方便地使用Minio组件将生成的文件上传到Minio服务器。

![](images\file-oss-minio.jpg)

通过bboss输入插件从各种类型的数据源采集数据，经过加工处理后，再通过文件输出插件将数据写入文件，然后调用Minio组件将生成的文件上传到Minio服务器，使用参考文档：

[文件输出插件对接OSS对象库Minio](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_233-导出并上传oss)

**视频教程推荐**

[视频 | bboss开发环境搭建教程](https://mp.weixin.qq.com/s?__biz=MzA3MzE0MDUyNw==&mid=2247484503&idx=1&sn=59bd7cbab0ff9d89377289228a93e84b&scene=21#wechat_redirect)

[视频 | Bboss ETL和流处理作业发布部署运行教程](https://mp.weixin.qq.com/s?__biz=MzA3MzE0MDUyNw==&mid=2247484515&idx=1&sn=580dbbaf86f2ed2360ae71724b9678a6&scene=21#wechat_redirect)



