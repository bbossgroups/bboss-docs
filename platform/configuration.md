### bboss平台子系统配置及系统登录以及其它常用配置介绍

bboss平台可以包含一个主系统和多个子系统，每个子系统可以配置独立的子系统登录界面以及登录成功的跳转界面。
**主系统配置：**

主系统配置文件为/resources/module.xml文件，可以在module.xml中配置子系统和主系统的模块、首页及菜单

**主系统登录跳转页配置和控制参数topMenus配置****

修改module.xml中sysmenu元素：

Xml代码

```xml
<sysmenu ...  
        successRedirect="sanydesktop/index.page"  
        logoutredirect="login.page"  
                topMenus="2"  
...  
        >
```

logoutredirect-子系统登录界面，子系统注销时退出到logoutredirect指定的登录页，如果没有配置，则跳转到全局登录页面（/login.page）

successRedirect-子系统登录成功跳转页面，如果没有设置则由平台决定跳转地址（根据登录风格和子系统综合判断）

**topMenus：**新版平台参数，在sysmenu元素上指定顶部菜单最多显示个数topMenus，默认-1，全部显示，顶部菜单增加更多项，可以指定默认显示几个顶部菜单，超过的菜单放到更多下拉中

  **icon配置-新版平台菜单log配置**
<publicitem name="首页"
i18n:en_US="Index" iframe="true"
id="indexpage" icon="icon-home" main="true">

<item name="系统监控" id="dbmonitor" i18n:en_US="System Monitor"  icon="icon-home" >

<module name="系统监控" id="dbmonitor" i18n:en_US="System Monitor"  icon="icon-home" >

**1.子系统配置**  

Xml代码 

```xml
<subsystem name="移动门户"  id="mbp" module="module-mbp.xml"  
            successRedirect="sanydesktop/index.page"  
    logoutredirect="/sanymbp/login.page"/>  
```

直接在system根节点下配置subsystem 元素，**如果不需要在登录界面看到或者要废弃某个子系统注释或者删掉**即可，相关属性说明如下：

属性说明：

name-子系统名称，必填项

id-子系统唯一标识，必填项

module-子系统首页、模块以及菜单配置文件，必填项

logoutredirect-子系统登录界面，子系统注销时退出到logoutredirect指定的登录页，如果没有配置，则跳转到全

局登录页面（/login.page）

successRedirect-子系统登录成功跳转页面，如果没有设置则由平台决定跳转地址（根据登录风格和子系统综合判断）：逻辑如下

**Java代码**

```java
private String getSuccessRedirect(String loginStyle, String subsystem) {  
        StringBuilder ret = new StringBuilder();  
        if(StringUtil.isEmpty(subsystem ))  
        {  
          
            if (loginStyle == null || loginStyle.equals("5") || loginStyle.equals("6")) {  
                ret.append("sanydesktop/indexcommon.page");  
            }   
            else if ((loginStyle != null && loginStyle.equals("1")) || loginStyle.equals("cms")) {  
                ret.append("index.jsp?subsystem_id=").append(subsystem);  
            } else if (loginStyle.equals("3")) {  
                ret.append("sanydesktop/index.page");  
            } else if (loginStyle.equals("2")) {  
                ret.append("desktop/desktop1.page");  
            } else if (loginStyle.equals("4")) {  
                ret.append("sanydesktop/webindex.page");  
            }  
            else  
            {  
                ret.append("sanydesktop/indexcommon.page");  
            }  
        }  
        else  
        {  
            SubSystem sys = Framework.getSubSystem(subsystem);  
            if(sys != null && !StringUtil.isEmpty(sys.getSuccessRedirect()))  
                ret.append(sys.getSuccessRedirect());  
            else  
            {  
                if (loginStyle == null || loginStyle.equals("5") || loginStyle.equals("6")) {  
                    ret.append("sanydesktop/indexcommon.page");  
                }   
                else if ((loginStyle != null && loginStyle.equals("1")) || loginStyle.equals("cms")) {  
                    ret.append("index.jsp?subsystem_id=").append(subsystem);  
                } else if (loginStyle.equals("3")) {  
                    ret.append("sanydesktop/index.page");  
                } else if (loginStyle.equals("2")) {  
                    ret.append("desktop/desktop1.page");  
                } else if (loginStyle.equals("4")) {  
                    ret.append("sanydesktop/webindex.page");  
                }  
                else  
                {  
                    ret.append("sanydesktop/indexcommon.page");  
                }  
            }  
        }  
        return ret.toString();  
    }  
```

**2.指定全局默认子系统**

在单点登录时系统没有传递subsystem_id参数时默认读取全局默认子系统id作为当前登录的子系统，并且会进入对应子系统的首页，指定全局默认子系统方法，修改resources\properties-sys.xml文件中的属性default_module即可：

Xml代码 

```xml
<property name="default_module" value="module"/> 
```

**3.指定热加载菜单配置文件控制开关**

热加载菜单配置文件控制开关用来控制当菜单文件修改后是否自动加载修改后的文件，修改resources\properties-sys.xml文件中的属性menu_monitor(默认为true)即可：

Java代码

```java
<property name="menu_monitor" value="true"/>  
```

**4.全局默认登录页面配置**

如果子系统没有指定自己的登录界面，则和主系统一样使用全局默认登录界面作为登录页，指定方法为：修改resources/config-manager.xml中authenticate元素的loginpage属性

Xml代码

```xml
<authenticate loginpage="login.page">  
            .....  
        </authenticate> 
```

**5.系统用户访问页面权限检测失败（无权限）调整地址配置**

修改WebRoot/WEB-INF/web.xml文件过滤securityFilter的authorfailedurl参数：

Xml代码

```xml
<filter>  
    <filter-name>securityFilter</filter-name>  
    <filter-class>com.frameworkset.platform.security.SYSAuthenticateFilter</filter-class>  
        。。。。   
        <init-param>  
            <param-name>authorfailedurl</param-name>  
            <param-value>/common/jsp/authorfail.jsp</param-value>  
        </init-param>  
        。。。。。。         
  </filter>  
```

**6.平台免登录url配置**

修改WebRoot/WEB-INF/web.xml文件过滤securityFilter的patternsExclude参数：

Xml代码

```xml
<filter>  
    <filter-name>securityFilter</filter-name>  
    <filter-class>com.frameworkset.platform.security.SYSAuthenticateFilter</filter-class>  
       。。。。      
        <init-param>  
      <param-name>patternsExclude</param-name>  
      <param-value>  
            /sysmanager/logoutredirect.jsp,  
            /login.jsp,  
            /login.page,  
            /login_en.jsp,  
            /logout.jsp,  
            /webseal/websealloginfail.jsp,  
            /webseal/message.jsp,  
            /test/testmmssso.jsp,  
            /test/testssowithtoken.jsp,  
            /sso/login.jsp,  
            /sso/sso.page,  
            /sso/ssowithtoken.page,  
            /sso/ssowithticket.page,  
            /sanydesktop/cookieLocale.page,  
           /sysmanager/password/modifyExpiredUserPWD.jsp,  
          /passward/modifyExpiredPassword.page,  
          /passward/generateImageCode.page,  
          /common/jsp/tokenfail.jsp,  
          /sanymbp/login.page,  
          /monitor/dbmonitor_activitedetail.jsp  
           </param-value>  
    </init-param>  
        。。。。。。         
  </filter>  x
```

其中地址清单以逗号分隔，可以写具体的地址，亦可以写通配符地址，例如：

/sso/ssowithticket.page

/sso/*.page

**7.URL权限配置及控制方法**

bboss平台可以非常方便地对url资源进行权限控制，需要进行权限控制的url资源需要与菜单关联或者与自定义资源操作关联，也可以对关联参数的url进行权限控制。

**菜单中配置url权限**

菜单中配置url权限，在item或者module的authoration节点中配置与该菜单权限关联的url，用户只有拥有这个菜单的访问权限才能访问这些关联的url资源，如果没有菜单访问权限，则会跳转的无权限提示页面；如果url和多个菜单关联或者和自定义资源的操作关联，则只要有一个菜单或者操作有权限访问，则有这个url资源的访问权限；workspace->content元素中的菜单入口地址默认和菜单权限关联；

Xml代码 

```xml
<item name="绩效审批页面"  
                i18n:en_US="web list" id="performance_approval"  showleftmenu="false" menuType="type1">  
                       
                    <top>header.page</top>  
                       
                    <workspace>  
                        <content>/html3/hrm/performance_approval.html</content>  
                    </workspace>  
                <authoration>                
                 <url>/sysmanager/resmanager/res_query.jsp,/sysmanager/user/userres_querylist.jsp</url><url>/sysmanager/resmanager/res_query.jsp,/sysmanager/user/userres_querylist.jsp</url>  
   
            </authoration>  
                </item>   
```

**自定义资源操作中配置url权限**

平台自定义资源权限文档，参考文档：
http://yin-bp.iteye.com/blog/2147000

如果url只要登录就可以访问的话只要不和任何菜单或者操作关联即可，万一因为菜单配置的入口地址而强制关联了，可以将对应的url地址配置到系统菜单配置文件中的publicitem节点的authoration节点中，publicitem相关的url都是对所有登录用户公开访问，例如下面列出了平台中必须配置在publicitem中的所有登陆用户都能够访问的公共url资源：

Xml代码

```xml
<publicitem name="首页"   
            i18n:en_US="Index"    
            id="indexpage" main="true">             
             。。。。  
            <authoration>                
                    <url>/sysmanager/resmanager/res_query.jsp,/sysmanager/user/userres_querylist.jsp</url>  
                    <url>/sysmanager/resmanager/delRedundance.jsp,/sysmanager/password/saveModifySelfPWD.jsp,/sysmanager/password/modifySelfPassword.jsp</url>  
                    <url>/sysmanager/user/userres_querylist.jsp</url>  
                    <url>/sysmanager/resmanager/res_queryframe.jsp</url>      
                    <url>/sysmanager/user/resChangeAjax.jsp</url>                         
            </authoration>  
        </publicitem>  
```

注意：bboss平台中的url权限控制功能需要在应用的/WEB-INF/web.xml文件中开启才可以的，具体方法,修改过滤器securityFilter中的参数enablePermissionCheck为true，开启后无权限提示页在authorfailedurl参数中配置：

Xml代码

```xml
<filter-name>securityFilter</filter-name>  
    <filter-class>com.frameworkset.platform.security.SYSAuthenticateFilter</filter-class>  
          
      
        <init-param>  
            <param-name>enablePermissionCheck</param-name>  
            <param-value>true</param-value>  
        </init-param>  
           
          
        <init-param>  
            <param-name>authorfailedurl</param-name>  
            <param-value>/common/jsp/authorfail.jsp</param-value>  
        </init-param>  
        <init-param>  
            <param-name>failedback</param-name>  
            <param-value>true</param-value>  
        </init-param>  
        <init-param>  
            <param-name>failedbackurlpattern</param-name>  
            <param-value>/sanydesktop/index.page,/sanydesktop/indexcommon.page</param-value>  
        </init-param>  
           
  </filter>  
```

**直接访问系统url，如果想在认证失败跳转到登录页并登录成功后再回到刚访问url，则需要在failedbackurlpattern参数中配置这个url地址，否则登录成功后会跳转到系统或者子系统首页（publicitem中对应的地址），这个机制也同样需要开启failedback为true才生效**

**8.按照菜单配置顺序获取当前用户带权限的菜单列表**

Java代码

```java
1.获取module下按配置顺序排序的权限菜单方法：  
  
public static void geSubMenus(Map<String,MenuItemU> permissionMenus,Module module,HttpServletRequest request,AccessControl accesscontroler)  
    {  
          
        MenuQueue menus = module.getMenus();  
        String contextpath = request.getContextPath();  
        for(int i = 0 ; menus != null && i < menus.size() ; i ++)  
        {  
            MenuItem menu = menus.getMenuItem(i);  
            if (!menu.isUsed()) {  
                continue;  
            }  
            if(menu instanceof Module)  
            {  
                MenuItemU menuItemU = new MenuItemU();  
                menuItemU.setId(menu.getId());  
                menuItemU.setName(menu.getName(request));  
                menuItemU.setImageUrl(menu.getMouseclickimg(request));  
                menuItemU.setType("module");  
                permissionMenus.put(menu.getId(), menuItemU);  
            }  
            else  
            {  
                Item item = (Item)menu;  
                  
                String url = null;  
                String area = item.getArea();  
                if(area != null && area.equals("main"))  
                {  
                    url = MenuHelper.getMainUrl(contextpath, item,  
                            (java.util.Map) null);  
                }  
                else  
                {  
                    url = MenuHelper.getRealUrl(contextpath, Framework.getWorkspaceContent(item,accesscontroler),MenuHelper.sanymenupath_menuid,item.getId());  
                }  
                MenuItemU menuItemU = new MenuItemU();  
                menuItemU.setId(item.getId());  
                menuItemU.setName(item.getName(request));  
                menuItemU.setImageUrl(item.getMouseclickimg(request));  
                menuItemU.setPathU(url);  
                menuItemU.setType("item");  
                menuItemU.setDesktop_height(item.getDesktop_height());  
                menuItemU.setDesktop_width(item.getDesktop_width());  
                permissionMenus.put(item.getId(), menuItemU);  
            }  
        }  
       
           
    }  
      
2.获取一级排序菜单的方法：  
      
    MenuHelper menuHelper = MenuHelper.getMenuHelper(request);  
        MenuQueue menus = menuHelper.getMenus();  
```

**9.平台自定义资源权限控制使用方法**

参考文档：
http://yin-bp.iteye.com/blog/2147000

**10.子系统与权限资源关联方法**

我们在resource.xml文件中配置定义了一类资源时，需要将其与对应的子系统关联，关联方法如下：

Xml代码

```xml
<!-- 机构资源 -->  
    <resource id="orgunit" name="机构资源"  i18n:en_US="Organization Resource"  class="resOrgTree.jsp" default="true" allowIfNoRequiredRole="false" auto="true" system="module,cms,mbp,esb,autodeploy,dp">  
        <!--定义非未受保护的特殊资源-->     
        <!--<exclude resourceid="*"/>  
        <operation id="userorgset"/>  
        </exclude>-->  
              
        <!--  
            定义资源操作组  
            name:指定资源操作组的名称，具体的操作定义在操作组中  
        -->  
        <operationgroup groupid="orgoperations"/>  
                  
        <!--   
            机构资源全局操作  
            globalresourceid:全局操作对应的资源标识id  
            groupid:全局操作对应的操作组  
        -->  
        <globaloperationgroup globalresourceid="orgunit" groupid="gloabelorgunit"/>  
    </resource>  
```

资源resource元素的system属性中指定资源对应的系统标识，例如：
system="module,cms,mbp,esb,autodeploy,dp"其中module是默认的主系统标识，其他是子系统标识（cms,mbp,esb,autodeploy,dp），只有将资源与子系统关联后，在权限管理的授权界面中才能看到这些资源并对相应的资源进行授权。这些子系统在module.xml文件中进行配置：

Xml代码 

```xml
<system languages="zh_CN,en_US">  
    <!-- 定义系统中的子系统功能模块 属性：name－子系统中文名称 id－子系统标识 module－模块文件名称 baseuri－如果子系统部署在其他的应用 -->  
    <subsystem name="内容管理" i18n:en_US="Content Manager" id="cms" template="cms" module="module-content.xml"  
          
        baseuri="http://localhost:7000/creatorcms" />  
    <subsystem name="请求服务管理" i18n:en_US="ESB Manager" id="esb" module="module-esb.xml"  
          
        baseuri="http://localhost:7000/creatorcms" />  
          
    <subsystem name="代理商门户" i18n:en_US="代理商门户" id="dp" module="module-dp.xml"          
        baseuri="http://localhost:7000/creatorcms" />  
    <subsystem name="WEB应用统计平台" i18n:en_US="WEB应用统计平台" id="sanylog" module="module-sanylog.xml"        
        baseuri="http://localhost:7000/creatorcms" />          
          
    <subsystem name="移动门户" i18n:en_US="移动门户" id="mbp" module="module-mbp.xml"  
        baseuri="http://localhost:7000/creatorcms"   
        successRedirect="sanydesktop/index.page"  
        logoutredirect="/sanymbp/login.page"/>  
  
................  
  
</system>  
```

其中的subsystem 的id属性指定了子系统标识。

**11.菜单中引用属性文件中定义的变量**

首先定义变量属性文件，文件存放在对应的resources包路径下面，例如一个配置了url地址的文件/resources/urls.xml,其中定义了一个变量hrmappurl：

Xml代码

```xml
<properties>    
    <property name="hrmappurl" value="http://hrm.sany.com.cn/NHRM"/>  
</properties> 
```

这样就可以在菜单配置文件中导入这个urls.xml文件：
主系统module.xml

Xml代码

```xml
<system languages="zh_CN,en_US">  
      
    <!-- 可以配置一些菜单变量,  
    菜单路径配置中除了可以配置#[userAccount]类的变量，还可以配置#[p:userAccount]类型变量，p:开头的变量的值可以从属性文件中获取，属性文件的配置方法是：     
     -->  
    <property file="urls.xml"/>     
</system>
```

导入urls.xml后我们就可以在菜单地址中引用hrmappurl这个变量了，方法如下：

Xml代码

```xml
<content>#[p:hrmappurl]/html3/rms/conference.html?aaa=#[userAccount]</content>  
```

引用语法说明：#[p:hrmappurl]

变量以#[和]标识，以p:作为变量前缀。

**12.菜单对应的url引用当前用户会话属性作为参数方法**

在菜单url地址中可以直接引用当前登录用户的会话属性，引用方法如下：
content/html3/rms/conference.html?aaa=#[userAccount]</content>
已#[和]标识会话属性，在其中直接指定会话属性即可。
平台内置的具体的会话属性清单请参考文档的备注部分：
[平台登录插件开发和配置](http://yin-bp.iteye.com/blog/2113250)
当然通过扩展登录插件也可以扩展添加系统特定的会话属性。

  **13.平台中开启和关闭会话共享方法**

平台中关闭和开启session共享机制方法：
开启，修改文件resources/sessionconf.xml 属性sessionstore为：

property name="sessionstore" refid="attr:sessionstore"/>

!-- <property name="sessionstore" value="session"/>-->

关闭，修改文件resources/sessionconf.xml 属性sessionstore为：

!-- <property name="sessionstore" refid="attr:sessionstore"/> -->

property name="sessionstore" value="session"/>

更多介绍参考文档：http://yin-bp.iteye.com/blog/2177475

redis和mongodb存储会话配置参考文档：http://yin-bp.iteye.com/blog/2287102  

  **14.平台中开启和关闭统一令牌方法**

平台中关闭token和开启token机制方法：

开启，修改文件resources/tokenconf.xml 属性enableToken和属性tokenstore为：

property name="enableToken" value="true"/>

property name="tokenstore" refid="attr:tokenStoreService"/> 

关闭，修改文件resources/tokenconf.xml 属性enableToken和属性tokenstore为：

property name="tokenstore" value="mem"/>

property name="enableToken" value="false"/>  

**15.mongodb配置**

修改配置文件：/resources/mongodb.xml集群配置：修改serverAddresses中服务器地址清单

Xml代码

```xml
<properties>  
    <!-- 增加mongodb数据源配置和client工厂类 -->  
    <property name="default" factory-class="org.frameworkset.nosql.mongodb.MongoDB"  
        init-method="init" destroy-method="close" factory-method="getMongoClient">  
          
        <!-- 这里不需要配置destroy-method，因为bboss持久层在jvm退出时会自动调用数据源的close方法 -->  
        <property name="serverAddresses" >  
            10.0.15.134:27017  
            10.0.15.134:27018  
            10.0.15.38:27017  
            10.0.15.39:27017  
        </property>  
        <property name="option" >  
            QUERYOPTION_SLAVEOK  
        </property>  
              
        <property name="writeConcern" value="REPLICA_ACKNOWLEDGED(4,2000)"/>  
        <property name="readPreference" value="NEAREST"/>       
        <property name="autoConnectRetry" value="true"/>        
        <property name="connectionsPerHost" value="10"/>    
        <property name="maxWaitTime" value="120000"/>   
        <property name="socketTimeout" value="0"/>      
        <property name="connectTimeout" value="15000"/>     
        <property name="threadsAllowedToBlockForConnectionMultiplier" value="5"/>   
        <property name="socketKeepAlive" value="true"/>     
        <!-- 如果需要用户认证则在下面配置mongodb的数据库验证用户和口令以及机制 -->   
        <!-- mechanism 取值范围：PLAIN GSSAPI MONGODB-CR MONGODB-X509，默认为MONGODB-CR  -->  
        <!--<property name="credentials">  
            <list componentType="bean">   
                  
                <property class="org.frameworkset.nosql.mongodb.ClientMongoCredential"  
                    f:mechanism="MONGODB-CR"  
                    f:database="sessiondb"  
                    f:userName="bboss"                    
                    f:password="bboss"/>   
                <property class="org.frameworkset.nosql.mongodb.ClientMongoCredential"  
                    f:mechanism="MONGODB-CR"  
                    f:database="tokendb"  
                    f:userName="bboss"                    
                    f:password="bboss"/>   
                   
            </list>  
        </property> -->  
    </property>  
      
</properties>  
```

单机配置：

Xml代码

```xml
<properties>  
    <!-- 增加mongodb数据源配置和client工厂类 -->  
    <property name="default" factory-class="org.frameworkset.nosql.mongodb.MongoDB"  
        init-method="init" destroy-method="close" factory-method="getMongoClient">  
          
          
        <property name="serverAddresses" >  
             127.0.0.1:27016  
        </property>  
        <property name="option" ></property>  
              
          
        <property name="writeConcern" value="JOURNAL_SAFE"/>  
          
        <property name="readPreference" value=""/>      
        <!-- 如果需要用户认证则在下面配置mongodb的数据库验证用户和口令以及机制 -->   
        <!-- mechanism 取值范围：PLAIN GSSAPI MONGODB-CR MONGODB-X509，默认为MONGODB-CR  -->  
        <!--<property name="credentials">  
            <list componentType="bean">   
                  
                <property class="org.frameworkset.nosql.mongodb.ClientMongoCredential"  
                    f:mechanism="MONGODB-CR"  
                    f:database="sessiondb"  
                    f:userName="bboss"                    
                    f:password="bboss"/>   
                <property class="org.frameworkset.nosql.mongodb.ClientMongoCredential"  
                    f:mechanism="MONGODB-CR"  
                    f:database="tokendb"  
                    f:userName="bboss"                    
                    f:password="bboss"/>   
                   
            </list>  
        </property>-->   
    </property>  
</properties>  
```

**16.数据库地址配置**

修改配置文件：/resources/poolman.xml

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8"?>  
  
<poolman>  
  
  
  
  
    <datasource>  
  
        <dbname>bspf</dbname>  
        <loadmetadata>false</loadmetadata>  
        <enablejta>true</enablejta>  
        <jndiName>druid_datasource_jndiname</jndiName>  
        <datasourceFile>dbcp.xml</datasourceFile>  
        <autoprimarykey>false</autoprimarykey>  
        <showsql>true</showsql>  
        <keygenerate>composite</keygenerate>  
    </datasource>  
  
       
</poolman>  
```

/resources/dbcp.xml

Xml代码

```xml
<property name="datasource" class="com.frameworkset.commons.dbcp2.BasicDataSource">  
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>    
    <property name="url" value="jdbc:mysql://10.0.15.134:3306/domysql"/>    
    <property name="username" value="root"/>    
    <property name="password" value="123456"/>    
    <!--initialSize: 初始化连接-->    
    <property name="initialSize" value="5"/>    
    <property name="maxTotal" value="20"/>    
    <!--maxIdle: 最大空闲连接-->    
    <property name="maxIdle" value="20"/>    
    <!--minIdle: 最小空闲连接-->    
    <property name="minIdle" value="20"/>    
  
    <!--removeAbandoned: 是否自动回收超时连接-->    
    <property name="removeAbandonedOnBorrow" value="false"/>  
    <property name="logAbandoned" value="true"/>  
    <!--removeAbandonedTimeout: 超时时间(以秒数为单位)-->    
    <property name="removeAbandonedTimeout" value="180"/>    
    <!--maxWait: 超时等待时间以毫秒为单位 6000毫秒/1000等于6秒-->    
    <property name="maxWaitMillis" value="3000"/>    
    <property name="validationQuery" value="SELECT 1 "/>      
    <property name="testOnBorrow" value="true"/>   
</property>  
   
```

调整其中的各个项即可，不同的数据库driver，validationQuery，url都需要进行调整

**17.禁用和启用加密账号SSO功能**

平台中提供了通过加密账号进行SSO功能，这个功能适合在内网信任环境使用，具体用法参考平台中的测试jsp页面：

WebRoot\test\testmmssso.jsp

这个功能需要启用后才能使用，启用方法：

修改文件/WebRoot/WEB-INF/conf/commons/bboss-sso.xml中包含控制器属性

f:enableuseraccountsso="false"

为

f:enableuseraccountsso="true"

在不适用使用加密账号单点登录的场景，只需要将该属性设置为false即可。

**18.activiti工作流配置**

activiti工作流配置文件resources/activiti.cfg.xml数据源设置-bspf为平台默认数据源，这个数据源是在

resources/poolman.xml文件中配置

Xml代码

```xml
<property name="dataSource" factory-class="com.frameworkset.common.poolman.util.SQLManager" factory-method="getTXDatasourceByDBName">  
        <construction>  
            <property value="bspf" />  
        </construction>  
    </property>  
```

自动升级和更新流程表（在activiti第一次部署或者版本升级时可以打开，一般需要关闭）

Xml代码

```xml
<property name="databaseSchemaUpdate" value="true" />  
```

**Xml代码**

```xml
<property name="history" value="audit" />---流程任务处理日志记录级别，一般为audit  
    <property name="idGenerator" class="org.activiti.engine.impl.persistence.StrongUuidGenerator" />----工作流表记录主键生成机制，全局UUID  
    <property name="userInfoMap" class="com.frameworkset.platform.sysmgrcore.purviewmanager.PDPUserInfoMapImpl"/>---用户信息缓存组件配置  
     <property name="KPIService" class="com.sany.workflow.service.impl.PlatformKPIServiceImpl"/>--工作流KPI指标参数管理组件  
     <property name="enableMixMultiUserTask" value="true"/>--true 启用多用户单任务切换为多用户多任务（流程配置和流程实例配置切换皆可）  
```

指定工作流定义中引用的组件的ioc容器，平台中直接指定为mvc 容器：

Xml代码

```xml
<property name="beanFactory"   
              factory-class="org.frameworkset.web.servlet.support.WebApplicationContextUtils"   
              factory-method="getWebApplicationContext"/>  

```

流程版本升级时，实例升级策略配置-业务相关设置：

Xml代码 

```xml
<property name="deployPolicy">  
        <list>  
            <!--   
            指定流程实例升级到最新版本时需要更新的业务表及业务表中对应的流程定义id字段名称  
            可以通过property元素指定多个业务表   
            -->  
<!--         <property table="tpp_plan" processdefcolumn="pd_id"/>-->  
            <!--   
            指定部署流程版本时，升级流程实例或者删除流程实例回调处理函数，可以与指定的表和表字段一起使用  
            可以指定多个回调处理函数  
             -->  
<!--             <property upgradecallback="com.sany.test.TestUpgradeCallback"/>            -->  
        </list>  
    </property> 
```

使用启用流程任务统一代办触发器机制-true 启用后，所有的用户代办任务刷新到统一代办表TD_WF_RUN_TASK,false关闭

Xml代码

```xml
<property name="enablebussinesstrigger" value="false"/>  
```

**19.quartz定时任务配置**

quartz定时任务配置，配置文件resources/org/frameworkset/task/quarts-task.xml，这里只介绍平台相关的任务设置，更多quartz任务引擎的配置，请参考文档：http://yin-bp.iteye.com/blog/1776315
禁用和启用quartz定时任务启用：修改enable属性为true，平台应用启动时，启动quartz任务引擎property name="taskconfig" enable="true"禁用：修改enable属性为false，平台应用启动时，不启动quartz任务引擎property name="taskconfig" enable="false"任务配置：以主数据同步为例

**Xml代码**

```xml
<property name="hrMasterDataSync" --唯一作业名称  
                        jobid="hrMasterDataSync"--唯一作业标识                          
                        bean-name="hrSyncTask"--作业对应的组件名称，在后面进行申明，也可以直接通过bean-class直接指定任务的实现类路径  
                        method="syncAllData"--作业执行的方法  
                        cronb_time="0 30 9 * * ?" --定义任务触发时间规则  
                        used="true"--是否启用任务  
                        shouldRecover="false">  
                    </property>  
```

hrSyncTask组件声明：

Xml代码

```xml
--通过工程模式，声明一个hr.ioccontext容器中的组件masterdata.hrSyncTask的引用变量hrSyncTask，quartz任务引擎通过这个变量来引用对应的定时任务组件  
<property name="hrSyncTask"  factory-bean="hr.ioccontext" factory-method="getBeanObject">  
        <construction>  
            <property value="masterdata.hrSyncTask" />  
        </construction>  
    </property>  
--声明组件所在ioc容器  
    <property name="hr.ioccontext" factory-class="org.frameworkset.spi.DefaultApplicationContext" factory-method="getApplicationContext">  
        <construction>  
            <property value="bboss-masterdata-humanResource.xml" />  
        </construction>  
    </property>  

```

**20.启用和禁用平台分布式事件、分布式事件通讯协议配置**

参考文档：http://yin-bp.iteye.com/blog/2174863