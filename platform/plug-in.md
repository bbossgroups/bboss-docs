### 平台登录插件开发和配置

**新版本平台登录插件开发和配置**

编写自己的插件，以便在登录后执行相应的操作：

Java代码

```java
public class TicketUserPasswordLoginModule extends UserPasswordLoginModule {  
      
    private static Logger logger = Logger.getLogger(TicketUserPasswordLoginModule.class);   
  
    @Override  
    protected void buildCallback(CheckCallBackWrapper checkCallBack, User user,  
            String userName, String password, String password_i,  
            Organization org) throws ManagerException {  
          
        super.buildCallback(checkCallBack, user, userName, password, password_i, org);  
          
        try {  
            //dosomething here 业务  
              
            //获取业务信息设置到会话对象中（登录时可以在这里将登录的酒店信息设置到会话属性中）  
            String ticket = AppHelper.getTicket(userName, user.getUserWorknumber());              
            checkCallBack.setUserAttribute("ticket", ticket);  
              
            //系统中获取ticket对象的方法:  
            AccessControl accesscontroler = AccessControl.getAccessControl();  
            String ticket_ = accesscontroler.getUserAttribute("ticket");  
              
        } catch (Exception e) {  
            logger.error(e);  
        }  
          
    }  
  
    /** 
     * 重置用户属性 
     * 业务中使用方法：AccessControl.getAccessControl().resetUserAttribute("hotelcode"); 
     */  
    @Override  
      
    public void resetUserAttribute(HttpServletRequest request,  
            CheckCallBack checkCallBack, String userAttribute) {  
        String userName = (String)checkCallBack.getUserAttribute("userAccount");  
        String userAttributevalue = "";//........;//获取最新的属性值              
        checkCallBack.setUserAttribute(userAttribute, userAttributevalue);//更新属性值  
        // TODO Auto-generated method stub  
        //super.resetUserAttribute(request, checkCallBack, userAttribute);  
    }  
  
    /** 
     * 更新用户会话属性，酒店切换时可以调用下面的方法更新登录的酒店信息： 
     * 业务中调用方法：AccessControl.getAccessControl().resetUserAttributes(); 
     */  
    @Override  
    public void resetUserAttributes(HttpServletRequest request,  
            CheckCallBack checkCallBack) {  
        //根据需要更新属性值  
        try {  
            String userName = (String)checkCallBack.getUserAttribute("userAccount");  
            String userWorknumber = (String)checkCallBack.getUserAttribute("userWorknumber");  
            String ticket = AppHelper.getTicket(userName, userWorknumber);            
            checkCallBack.setUserAttribute("ticket", ticket);  
        } catch (Exception e) {  
            logger.error(e);  
        }  
    }  
}  
```

写好自己的登录插件后，就可以修改文件resources/config-manager.xml的内容：

Xml代码

```xml
<loginModule name="console" controlFlag="required" debug="true" registTable="DB" class="org.frameworkset.platform.security.authenticate.UserPasswordLoginModule" />   
```

为：

Xml代码

```xml
<loginModule name="console" controlFlag="required" debug="true" registTable="DB" class="com.bboss.application.util.TicketUserPasswordLoginModule" />   
```

即可。

**备注**

平台后台java程序中获取当前登录用户会话对象及用户属性方法：

Java代码

```java
com.frameworkset.platform.security.AccessControl accesscontroler = com.frameworkset.platform.security.AccessControl.getAccessControl();  
  
String userID = accesscontroler.getUserID();//获取用户id  
    String userName = accesscontroler.getUserName();//获取用户中文名  
    accesscontroler.getUserAttribute(userAttribute)  
    String worknumber = accesscontroler.getUserAttribute("userWorknumber");//获取用户工号  
    String fullorgjob = accesscontroler.getUserAttribute("fullorgjob");//获取用户带层级的机构岗位信息  
    String orgjob = accesscontroler.getUserAttribute("orgjob");//获取用户直属机构岗位信息  
```

**获取用户登录信息**

新版平台获取用户

Java代码  

```java
userAccount：用户账号  
userID:用户唯一标识  
depart:部门名称  
departId:部门id  
job：用户岗位信息  
title：中文名称(userAccount);  
userName:用户中文名称  
userSex：用户性别  
worknumber：工号  
telphone：手机号码  
userLeaderid：主管id  
userLeaderName：主管名称  
userLeaderAccount：主管账号  
```

同时系统可以扩展自己的用户loginmodule，在buildCallback方法中添加自己的用户属性。

**2.老版本平台登录插件开发和配置**

编写自己的插件，以便在登录后执行相应的操作：

Java代码

```java
package com.bboss.application.util;  
  
  
import javax.servlet.http.HttpServletRequest;  
  
import org.apache.log4j.Logger;  
  
import com.frameworkset.platform.security.AccessControl;  
import com.frameworkset.platform.security.authentication.CheckCallBack;  
import com.frameworkset.platform.security.authentication.CheckCallBackWrapper;  
import com.frameworkset.platform.sysmgrcore.authenticate.UserPasswordLoginModule;  
import com.frameworkset.platform.sysmgrcore.entity.Organization;  
import com.frameworkset.platform.sysmgrcore.entity.User;  
import com.frameworkset.platform.sysmgrcore.exception.ManagerException;  
  
  
public class TicketUserPasswordLoginModule extends UserPasswordLoginModule {  
      
    private static Logger logger = Logger.getLogger(TicketUserPasswordLoginModule.class);   
  
    @Override  
    protected void buildCallback(CheckCallBackWrapper checkCallBack, User user,  
            String userName, String password, String password_i,  
            Organization org) throws ManagerException {  
          
        super.buildCallback(checkCallBack, user, userName, password, password_i, org);  
          
        try {  
            //dosomething here 业务  
              
            //获取业务信息设置到会话对象中（登录时可以在这里将登录的酒店信息设置到会话属性中）  
            String ticket = AppHelper.getTicket(userName, user.getUserWorknumber());              
            checkCallBack.setUserAttribute("ticket", ticket);  
              
            //系统中获取ticket对象的方法:  
            AccessControl accesscontroler = AccessControl.getAccessControl();  
            String ticket_ = accesscontroler.getUserAttribute("ticket");  
              
        } catch (Exception e) {  
            logger.error(e);  
        }  
          
    }  
  
    /** 
     * 重置用户属性 
     * 业务中使用方法：AccessControl.getAccessControl().resetUserAttribute("hotelcode"); 
     */  
    @Override  
      
    public void resetUserAttribute(HttpServletRequest request,  
            CheckCallBack checkCallBack, String userAttribute) {  
        String userName = (String)checkCallBack.getUserAttribute("userAccount");  
        String userAttributevalue = "";//........;//获取最新的属性值              
        checkCallBack.setUserAttribute(userAttribute, userAttributevalue);//更新属性值  
        // TODO Auto-generated method stub  
        //super.resetUserAttribute(request, checkCallBack, userAttribute);  
    }  
  
    /** 
     * 更新用户会话属性，酒店切换时可以调用下面的方法更新登录的酒店信息： 
     * 业务中调用方法：AccessControl.getAccessControl().resetUserAttributes(); 
     */  
    @Override  
    public void resetUserAttributes(HttpServletRequest request,  
            CheckCallBack checkCallBack) {  
        //根据需要更新属性值  
        try {  
            String userName = (String)checkCallBack.getUserAttribute("userAccount");  
            String userWorknumber = (String)checkCallBack.getUserAttribute("userWorknumber");  
            String ticket = AppHelper.getTicket(userName, userWorknumber);            
            checkCallBack.setUserAttribute("ticket", ticket);  
        } catch (Exception e) {  
            logger.error(e);  
        }  
    }  
}  
```

写好自己的登录插件后，就可以修改文件resources/config-manager.xml的内容：

Xml代码

```xml
<loginModule name="console" controlFlag="required" debug="true" registTable="DB" class="com.frameworkset.platform.sysmgrcore.authenticate.UserPasswordLoginModule" />   
```

为：

Xml代码

```xml
**<****loginModule** name="console" controlFlag="required" debug="true" registTable="DB" class="com.bboss.application.util.TicketUserPasswordLoginModule" **/>**  
```

即可。

备注**

平台后台java程序中获取当前登录用户会话对象及用户属性方法：

Java代码

```java
com.frameworkset.platform.security.AccessControl accesscontroler = com.frameworkset.platform.security.AccessControl.getAccessControl();  
  
String userID = accesscontroler.getUserID();//获取用户id  
    String userName = accesscontroler.getUserName();//获取用户中文名  
    accesscontroler.getUserAttribute(userAttribute)  
    String worknumber = accesscontroler.getUserAttribute("userWorknumber");//获取用户工号  
    String fullorgjob = accesscontroler.getUserAttribute("fullorgjob");//获取用户带层级的机构岗位信息  
    String orgjob = accesscontroler.getUserAttribute("orgjob");//获取用户直属机构岗位信息  
```

**获取用户登录信息**

**老版本平台**

accesscontroler.getUserAttribute能够获取到的所有默认属性清单：

Java代码

```java
userName：用户真实名称  
        userID：用户id       
        logincount：登录次数  
        userAccount：用户帐号  
        remark1：备注1  
        remark2：备注2  
        remark3：备注3  
        remark4：备注4  
        remark5：备注5  
        userAddress：地址  
        userEmail：邮箱  
        userFax：传真  
        userHometel：家庭电话  
        userIdcard：身份证  
        userMobiletel1：手机1  
        userMobiletel2：手机2  
        userOicq：oicq  
        userPinyin：用户名拼音  
        userPostalcode：邮编  
        userSex：性别  
        userType：用户类型  
        userWorknumber：工号  
        userWorktel：工作电话  
          
        userBirthday：生日  
        userRegdate：注册日期  
        userSn：用户排序号  
        userIsvalid：用户是否有效  
                orgjob：用户直属机构岗位信息  
                fullorgjob：用户带层级的机构岗位信息  
                CHARGEORGID:com.frameworkset.platform.sysmgrcore.entity.Organization用户所属机构对象  
```

同时系统可以扩展自己的用户loginmodule，在buildCallback方法中添加自己的用户属性。

bboss为了方便系统在jsp页面上获取当前用户的会话属性，特意定义了一个accesscontrol标签，使用方法如下：

先在jsp头部导入标签：

Html代码

```html
<%@ taglib uri="/WEB-INF/sany-taglib.tld" prefix="sany"%>  
```

接着就可以使用标签了：

Html代码

```html
<sany:accesscontrol userattribute="userName"/>  
<sany:accesscontrol userattribute="userAccount"/>  
<sany:accesscontrol userattribute="orgjob"/>  
```

检查资源操作权限的方法：

Java代码 

```java
boolean hasaddpermission = accesscontroler.checkPermission("testid",//资源id  
                                                                "add",//资源操作  
                                                                "testresource"//资源类型  
                                                                );  
    boolean hasupdatepermission = accesscontroler.checkPermission("testid","write","testresource");  
    boolean hasdeletepermission = accesscontroler.checkPermission("testid","delete","testresource");  
    boolean hasreadpermission = accesscontroler.checkPermission("testid","read","testresource");  
    boolean hasglobaltestreadpermission = accesscontroler.checkPermission("globaltest","read","testresource");  
    boolean hasglobaltestdeletepermission = accesscontroler.checkPermission("globaltest","delete","testresource");  
```

