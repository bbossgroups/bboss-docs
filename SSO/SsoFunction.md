### bboss 票据实现系统SSO功能介绍

测试环境应用账号：
appid：gspoffice
secret:52ad4782-9002-4b88-9c70-83858d772b69

**第一种方案：比较复杂**

登录时获取ticket，自己保存在某个地方（session或者数据库都行）：
Java代码

```java
String url = "http://bboss.bbossgroups.com/token/v2/genTicket.freepage?appid="+appid + "&secret="+secret + "&account="+account+ "&worknumber="+worknumber;  
String ticket = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
ticket 格式：  
{  
ticket：令牌  
resultcode: 操作结果码  
livetime:ticket有效期，每次访问时会刷新访问时间，以最近访问时间为起点计算有效期，有效期内都可以使用  
message:错误信息  
  
}  
```

每次跳转到其他系统获取带有时效性的一次性临时令牌，作为参数传递给其他系统：
Java代码

```java
String url = "http://bboss.bbossgroups.com/token/v2/getAuthTempToken.freepage?appid="+appid + "&secret="+secret + "&ticket="+ticket;  
String token = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
```

token 格式：

  {

resultcode: 操作结果码
token:临时token
message:错误信息
}  

其他系统得到令牌，通过令牌获取用户账号和工号，获取完毕后临时令牌失效：

Java代码 

```java
String url = "http://bboss.bbossgroups.com/checktoken/v2/checkToken.freepage?appid="+appid + "&secret="+secret + "&token="+token;  
String personresult = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
  
personresult 格式：  
{  
validateResult：boolean 如果获取到用户信息则为true，否则为false  
resultcode: 操作结果码  
userAccount:用户账号  
worknumber:用户工号  
message:错误信息  
  
}  
```

**第二种方案，更加简单**

直接获取临时票据

跳转时获取一个临时性的ticket：

Java代码

```java
String url = "http://bboss.bbossgroups.com/token/v2/getTempTicket.freepage?appid="+appid + "&secret="+secret + "&account="+account+ "&worknumber="+worknumber;  
String ticket = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
ticket 格式：  
{  
ticket：令牌  
resultcode: 操作结果码  
livetime:ticket有效期，每次访问时会刷新访问时间，以最近访问时间为起点计算有效期，有效期内都可以使用  
message:错误信息  
  
}  
```

其他系统获取到临时ticket，通过临时ticket获取用户账号和工号，同时临时ticket会失效（临时ticket的有效期在令牌服务器端设置）：

Java代码

```java
String url = "http://bboss.bbossgroups.com/checktoken/v2/checkTicket.freepage?appid="+appid + "&secret="+secret + "&ticket="+ticket;  
String personresult = org.frameworkset.spi.remote.http.HttpReqeust.httpPostforString(url);  
  
personresult 格式：  
{  
validateResult：boolean 如果获取到用户信息则为true，否则为false  
resultcode: 操作结果码  
userAccount:用户账号  
worknumber:用户工号  
message:错误信息  
  
}  
```

**接下来我们来运行第二种方案：**

在chrome浏览器中输入地址获取临时ticket：

Java代码 

```java
http://bboss.bbossgroups.com/token/v2/getTempTicket.freepage?appid=gspoffice&secret=52ad4782-9002-4b88-9c70-83858d772b69&account=yinbp&worknumber=10006673  
```

响应以下json报文，说明临时ticket生成正确，ticket的有效期是60秒：

Java代码

```java
{"resultcode":"ok","ticket":"tmptk_564b1212-7dce-4180-a885-44b235bced05","livetime":60000,"message":null}  
```

通过上面生成的ticket获取用户账户和工号，在chrome中输入以下请求：

Java代码 

```java
http://bboss.bbossgroups.com/checktoken/v2/checkTicket.freepage?appid=gspoffice&secret=52ad4782-9002-4b88-9c70-83858d772b69&ticket=tmptk_e8d179c3-cfe9-41cb-ba81-d466e19c5e0f  
```

如果浏览器中响应以下json报文，说明通过ticket获取账号和工号正确：

Java代码

```java
{"resultcode":"ok","userAccount":"yinbp","worknumber":"10006673","message":null,"validateResult":true}  
```

如果浏览器中响应以下json报文，说明ticket已经不存在，获取账号和工号为null：

Java代码

```java
{"resultcode":"TICKETNOTEXIST","userAccount":null,"worknumber":null,"message":null,"validateResult":false}  
```

如果浏览器中响应以下json报文，说明ticket已经失效，获取账号和工号为null：

Java代码 

```java
{"resultcode":"TICKETEXPIRED","userAccount":null,"worknumber":null,"message":null,"validateResult":false}  
```

