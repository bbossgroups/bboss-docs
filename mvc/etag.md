### bboss mvc控制器实现etag和last modify两种http缓存机制

#### **1.缓存控制器实现**

缓存控制器的类文件：

[HttpCache.java](https://github.com/bbossgroups/bestpractice/blob/master/mvc/src/org/frameworkset/mvc/HttpCache.java)

实现etag和last modify两种http缓存机制方法分别如下：

**last modify**

Java代码

```java
public ResponseEntity<String> cache(   
              //为了方便测试，此处传入文档最后修改时间  
              @RequestParam(name="millis") long lastModifiedMillis,  
              //浏览器验证文档内容是否修改时传入的Last-Modified If-Modified-Since  
              @RequestHeader (name = "If-Modified-Since", required = false,dateformat="EEE, d MMM yyyy HH:mm:ss.SSS 'GMT'",locale = "en_US") Date ifModifiedSince) {  
  
            //当前系统时间  
            long now = System.currentTimeMillis();  
            //文档可以在浏览器端/proxy上缓存多久  
            long maxAge = 20;  
  
            //判断内容是否修改了，此处使用等值判断  
            if(ifModifiedSince != null && ifModifiedSince.getTime() == lastModifiedMillis) {  
                return new ResponseEntity<String>(HttpStatus.NOT_MODIFIED);  
            }  
  
            DateFormat gmtDateFormat = new SimpleDateFormat("EEE, d MMM yyyy HH:mm:ss.SSS 'GMT'", Locale.US);  
            System.out.println(System.currentTimeMillis());  
            String body = "<a href=''>点击访问当前链接</a>";  
            MultiValueMap<String, String> headers = new HttpHeaders();  
  
            //文档修改时间  
            headers.add("Last-Modified", gmtDateFormat.format(new Date(lastModifiedMillis)));  
            //当前系统时间  
            headers.add("Date", gmtDateFormat.format(new Date(now)));  
            //过期时间 http 1.0支持  
            headers.add("Expires", gmtDateFormat.format(new Date(now + maxAge)));  
            //文档生存时间 http 1.1支持  
            headers.add("Cache-Control", "max-age=" + maxAge);  
            return new ResponseEntity<String>(body, headers, HttpStatus.OK,"String");  
        }  

```

**etag**

Java代码

```java
public ResponseEntity<String> etgcache(   
              //浏览器验证文档内容的实体 If-None-Match  
              @RequestHeader (name = "If-None-Match", required = false) String ifNoneMatch) {  
  
            //当前系统时间  
            long now = System.currentTimeMillis();  
            //文档可以在浏览器端/proxy上缓存多久  
            long maxAge = 10;  
  
            String body = "<a href=''>点击访问当前链接</a>";  
  
            //弱实体  
            String etag = "W/\"" + DigestUtils.md5DigestAsHex(body.getBytes()) + "\"";  
  
            if(ifNoneMatch != null &&  ifNoneMatch.equals(etag)) {  
                return new ResponseEntity<String>(HttpStatus.NOT_MODIFIED);  
            }  
  
            DateFormat gmtDateFormat = new SimpleDateFormat("EEE, d MMM yyyy HH:mm:ss 'GMT'", Locale.US);  
            MultiValueMap<String, String> headers = new HttpHeaders();  
  
            //ETag http 1.1支持  
            headers.add("ETag", etag);   
            //当前系统时间  
            headers.add("Date", gmtDateFormat.format(new Date(now)));  
            //文档生存时间 http 1.1支持  
            headers.add("Cache-Control", "max-age=" + maxAge);  
            return new ResponseEntity<String>(body, headers, HttpStatus.OK);  
        }  
```

#### **2.运行和访问实例**

启动示例应用之前，必须先安装好gradle并配置好gradle环境变量，至少[gradle 3.0+ （Complete distribution）](https://gradle.org/gradle-download/)版本
从github下载源码工程：
git clone -b master --depth 1 https://github.com/bbossgroups/bestpractice.git
切换到在命令行，进入源码目录
cd bestpractice

执行gradle指令
gradle :mvc:tomcatStart

启动成功后访问示例：
etag
http://localhost:8080/mvc/caches/etgcache.page
Last-Modified
http://localhost:8080/mvc/caches/cache.page?millis=1473172518546