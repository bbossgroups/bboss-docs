### 平台登录账号口令加密机制设置方法

平台加密机制：

[1]MD5：一种不可逆算法，安全 

[2]BASE64：可逆算法，比较安全

[3]HEX passwordsEncryptionAlgorithm=SHA-384 

[4]NONE：对密码不加密

具体设置方法：

修改/resources/properties-sys.xml文件中的两个属性：

passwordsEncryptionAlgorithm

encrpytype

平台默认密码为123456.

每种机制设置方法如下：
NONE
vproperty name="passwordsEncryptionAlgorithm" value="NONE" /

property name="encrpytype" value="NONE"/

每个用户默认密码为：123456

将密码设置为默认密码的sql：

update td_sm_user u set u.USER_PASSWORD='123456'

md5算法设置

property name="passwordsEncryptionAlgorithm" value="NONE" /

property name="encrpytype" value="MD5"/

每个用户默认密码为（123456的md5码）：E10ADC3949BA59ABBE56E057F20F883E

将密码设置为默认密码的sql：

update td_sm_user u set u.USER_PASSWORD='E10ADC3949BA59ABBE56E057F20F883E'

BASE64

property name="passwordsEncryptionAlgorithm" value="NONE" /

property name="encrpytype" value="BASE64"/

每个用户默认密码为（123456的BASE64码）：MTIzNDU2

将密码设置为默认密码的sql：

update td_sm_user u set u.USER_PASSWORD='MTIzNDU2'

  HEX

property name="passwordsEncryptionAlgorithm" value="SHA-384" /

property name="encrpytype" value="HEX"/

每个用户默认密码为（123456的HEX码）：

0a989ebc4a77b56a6e2bb7b19d995d185ce44090c13e2984b7ecc6d446d4b61ea9991b76a4c2f04b1b4d244841449454

将密码设置为默认密码的sql：

update td_sm_user u set

u.USER_PASSWORD=

'0a989ebc4a77b56a6e2bb7b19d995d185ce44090c13e2984b7ecc6d446d4b61ea9991b76a4c2f04b1b4d244841449454'

设置好平台的加密机制后，可以通过以下方法对明文密码进行加密：  

Java代码

```java
String p = com.frameworkset.platform.security.authentication.EncrpyPwd.encodePassword("123456");  
        System.out.println(p);  
```

在控制台上可以看到加密后的口令。