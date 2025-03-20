# Mino客户端组件使用介绍

bboss提供一个简单的OSS对象存储库Mino操作组件，基于Mino java客户端进行封装

```java
org.frameworkset.nosql.minio.MinioHelper
```

MinioHelper提供单例静态方法,来获取Minio实例，支持多Minio数据源

```java
Minio minio = MinioHelper.getMinio(minioFileConfig.getName());//获取指定名称的Minio数据源

```
通过minio提供的方法执行所有的Minio操作。本文示例代码对应的源文件：

https://gitee.com/bboss/elasticsearch-file2ftp/blob/main/src/main/java/org/frameworkset/elasticsearch/imp/minio/MinioTest.java

## 1.导入bboss Minio组件

gradle

```java
compile 'com.bbossgroups:bboss-data:6.3.1' 
```

maven


```xml
<dependency>  
    <groupId>com.bbossgroups</groupId>  
    <artifactId>bboss-data</artifactId>  
    <version>6.3.1</version>  
</dependency>  
```

## 2.bboss Minio组件使用

### 2.1 初始化和关闭Minio数据源

#### 初始化Minio数据源

通过以下代码初始化了一个名称为chan_fqa的Minio数据源，后续的用该名称来引用对应的Minio数据源来执行各种Minio操作。

```java
        //1. 初始化Minio数据源chan_fqa，用来操作向量数据库，一个Minio数据源只需要定义一次即可，后续通过名称chan_fqa反复引用，多线程安全
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
File file = new File("/data/header/user.jpg");
         
        try {
        
            long s = System.currentTimeMillis();
          	String bucket = "user_header_imgs";            
            String key = minio.saveOssFile(file,bucket);//保存文件，返回自动创建的key，可以通过key获取bucket下创建的文件
            //String key = "xxxaaaa";
            //key = minio.saveOssFile(file,bucket,key);//指定key，如果不指定，自动生成一个key
            long e = System.currentTimeMillis();
            String msg = "Send file "+filePath+" to minio bucket "+bucket+" complete,耗时:"+
            logger.info(msg);
             
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
minio.getOssFile(  bucket,  key,   fileName) ;
```

 minio提供了一系列获取oss对象的方法，可以查看接口定义，选择合适的方法实现获取oss对象内容。

### 2.5 删除文件对象

```java
minio.deleteOssFile("filedown","HN_BOSS_TRADE_202501092032_000001.txt");//bucket,  key
```

### 2.6 获取原生MinioClient

```java
MinioClient minioClient = minio.getMinioClient()
```

### 2.7 在ETL中的应用

bboss datatran使用Minio组件实现文件输出插件将生成的文件上传到Minio服务器。使用bboss输入插件从各种数据源采集数据，通过文件输出插件将数据写入文件，然后调用Minio组件将生成的文件上传到Minio服务器，使用参考文档：

[文件输出插件对接OSS对象库Minio](https://esdoc.bbossgroups.com/#/datatran-plugins?id=_233-导出并上传oss)





