### bboss 持久层数据库信息加密功能介绍

bboss 持久层数据库信息加密插件功能介绍，适用于3.6及后续版本。

bboss 持久层数据库信息加密插件是3.6中新增的功能，可以在poolman.xml中配置加密后的数据库url、数据库账号、数据库口令，采用des算法进行加密，可以通过控制开关来启用加密机制。  

加密插件缺省提供了四个插件,分别说明如下：

com.frameworkset.common.poolman.security.DESDBInfoEncrypt

DESDBInfoEncrypt-采用des算法对数据库url，账号，密码进行加密和解密操作

com.frameworkset.common.poolman.security.DESDBPasswordEncrypt

DESDBPasswordEncrypt-采用des算法对数据库密码进行加密和解密操作

com.frameworkset.common.poolman.security.DESDBUserEncrypt

DESDBUserEncrypt-采用des算法对数据库用户名进行加密和解密操作

com.frameworkset.common.poolman.security.DESDBUrlEncrypt

DESDBUrlEncrypt-采用des算法对数据库url进行加密和解密操作

具体采用哪个插件取决于你想加密哪些内容，具体配置插件的方法为：

打开bboss-aop.jar编辑器中aop.properties文件：

Java代码

```java
#   aop实现机制：  
    #   javaproxy java动态代理模式  
    #   cglib cglib模式  
     #  
aop.proxy.type=cglib  
aop.webservice.scope=mvc,application,default  
sqlfile.refresh_interval=5000  
approot=  
#DESDBInfoEncrypt-采用des算法对数据库url，账号，密码进行加密和解密操作  
#DBInfoEncryptclass=com.frameworkset.common.poolman.security.DESDBInfoEncrypt  
  
#DESDBPasswordEncrypt-采用des算法对数据库密码进行加密和解密操作  
DBInfoEncryptclass=com.frameworkset.common.poolman.security.DESDBPasswordEncrypt  
  
#DESDBUserEncrypt-采用des算法对数据库用户名进行加密和解密操作  
#DBInfoEncryptclass=com.frameworkset.common.poolman.security.DESDBUserEncrypt  
  
#DESDBUrlEncrypt-采用des算法对数据库url进行加密和解密操作  
#DBInfoEncryptclass=com.frameworkset.common.poolman.security.DESDBUrlEncrypt  
```

打开相应的加密插件即可。

poolman.xml文件中相应的属性配置成密文(根据插件来配置）

以下配置是DESDBInfoEncrypt插件对应的配置： 

Xml代码

```xml
<url>b708bd11fa5eb60c7eee4be2c974c2800c5296e4dde70723e9a6f3ceadcbc516</url>   
    <username>ac6023515aa7d147</username>  
    <password>40ebe1805f90ebf5</password>  
```

以下配置是DESDBPasswordEncrypt插件对应的配置：

Xml代码

```xml
<password>40ebe1805f90ebf5</password>  
```

以下配置是DESDBUserEncrypt插件对应的配置：

Xml代码

```xml
<username>ac6023515aa7d147</username> 
```

以下配置是DESDBUrlEncrypt插件对应的配置：

Xml代码

```xml
<url>b708bd11fa5eb60c7eee4be2c974c2800c5296e4dde70723e9a6f3ceadcbc516</url>   
```

poolman.xml文件中属性encryptdbinfo配置成true:

<**encryptdbinfo>**true<**/encryptdbinfo>**

信息加密的方法如下：

Java代码

```java
com.frameworkset.common.poolman.security.DESCipher aa = new com.frameworkset.common.poolman.security.DESCipher();  
        String bb = aa.encrypt("123456");         
        bb = aa.encrypt("root");          
        bb = aa.encrypt("jdbc:mysql://localhost:3306/cim");  
```

  然后将加密后的信息配置到poolman.xml中的对应属性即可。

同时如果想对账号、口令、url之间的任意两个组合加密的话，用户可以自己继承
com.frameworkset.common.poolman.security.BaseDBInfoEncrypt类，参考默认插件，实现相应的信息加密方法并配置到aop.properties中即可。
  