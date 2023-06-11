### bboss 持久层sql xml配置文件编写和加载方法介绍

  通过bboss持久数操作数据库首先要配置数据源，参考文档：
[bboss持久层多数据源配置及多数据库事务控制使用方法](http://yin-bp.iteye.com/blog/2065059)

[bboss 持久层配置apache dbcp，proxool,c3p0,Druid等数据源方法](http://yin-bp.iteye.com/blog/1620354)  

#### **1.sql xml文件编写**

首先在项目中导入bboss 持久层包：
**maven坐标**

Xml代码

```xml
<dependency>   
    <groupId>com.bbossgroups</groupId>   
    <artifactId>bboss-persistent</artifactId>   
    <version>6.0.9</version>   
</dependency>  
```

**gradle坐标**

Java代码 

```java
compile 'com.bbossgroups:bboss-persistent:6.0.9'  
```

sql xml文件存放在的工程的具体的包路径中，例如：

com/pdp/masterdata/hr/dao/HumanDataSql.xml

sql xml文件配置遵循bboss ioc 语法，sql语句语法分为原生sql和模板变量sql，原生sql样列：

Sql代码

```sql
select * from tableinfo where name like ? 
```

模板变量sql中可以包含bboss自定义模板变量和基于velocity语法动态sql，样列：
Sql代码

```sql
update td_sm_organization   
            set #if($orgName && !$orgName.equals(""))  
                    org_name=#[orgName],  
                #end  
                #if($parentId && !$parentId.equals(""))  
                    parent_id=#[parentId],  
                #end  
                #if($orgnumber && !$orgnumber.equals(""))  
                    orgnumber=#[orgnumber],  
                #end  
                #if($orgdesc && !$orgdesc.equals(""))  
                    orgdesc=#[orgdesc],  
                #end  
                #if($chargeorgid && !$chargeorgid.equals(""))  
                    chargeorgid=#[chargeorgid],  
                #end  
                #if($remark3 && !$remark3.equals(""))  
                    remark3=#[remark3],  
                #end  
                #if($remark5 && !$remark5.equals(""))  
                    remark5=#[remark5],  
                #end  
                #if($orgTreeLevel && !$orgTreeLevel.equals(""))  
                    org_tree_level=#[orgTreeLevel],  
                #end  
                #if($orgLevel && !$orgLevel.equals(""))  
                    org_level=#[orgLevel],  
                #end  
                #if($orgXzqm && !$orgXzqm.equals(""))  
                    org_xzqm=#[orgXzqm],  
                #end  
                org_id=#[orgId]  
            where org_id=#[orgId]  
```

动态sql更多介绍，可访问文档：http://yin-bp.iteye.com/blog/2181720

sql文件示例com/pdp/masterdata/hr/dao/HumanDataSql.xml：

Xml代码

```xml
<?xml version="1.0" encoding="UTF-8"?>  
  
<properties>  
      
    <property name="tdSmUserSave">  
        <![CDATA[ 
            insert into td_sm_user 
                (user_id, user_sn, user_name, user_password, user_realname, 
                user_pinyin, user_sex, user_hometel, user_worktel, user_worknumber, 
                user_mobiletel1, user_mobiletel2, user_fax, user_oicq, user_birthday, 
                user_address, user_postalcode, user_idcard, user_isvalid, 
                user_regdate, user_logincount, user_type, remark1, remark2, remark3, 
                remark4, remark5, past_time, dredge_time, lastlogin_date, worklength, 
                politics, istaxmanager, logon_ip, cert_sn) 
            values (#[userId], #[userSn],  
                #if($userName && !$userName.equals("")) 
                    #[userName], 
                #else  
                    #[userWorknumber], 
                #end  
                #[userPassword], #[userRealname], #[userPinyin], #[userSex], #[userHometel],  
                #[userWorktel], #[userWorknumber],  
                #[userMobiletel1], #[userMobiletel2], #[userFax], #[userOicq],  
                #[userBirthday], #[userAddress], #[userPostalcode],  
                #[userIdcard], #[userIsvalid], #[userRegdate], #[userLogincount],  
                #if($userName && !$userName.equals("")) 
                    '1', 
                #else  
                    '2', 
                #end 
                 #[remark1], #[remark2], #[remark3], #[remark4], #[remark5],  
                #[pastTime], #[dredgeTime], #[lastloginDate], #[worklength], #[politics],  
                #[istaxmanager], #[logonIp], #[certSn]) 
        ]]>  
    </property>  
      
    <property name="tdSmUserUpdate">  
        <![CDATA[ 
            update td_sm_user 
            set #if($userName && !$userName.equals("")) 
                    user_name=#[userName], 
                #else  
                    user_name=#[userWorknumber], 
                #end 
                user_password=#[userPassword], user_realname=#[userRealname], 
                user_pinyin=#[userPinyin], user_sex=#[userSex], user_hometel=#[userHometel],  
                user_worktel=#[userWorktel], user_mobiletel1=#[userMobiletel1], user_mobiletel2=#[userMobiletel2], 
                user_fax=#[userFax], user_oicq=#[userOicq], user_birthday=#[userBirthday], 
                user_email=#[userEmail], user_address=#[userAddress], user_postalcode=#[userPostalcode],  
                user_idcard=#[userIdcard], user_isvalid=#[userIsvalid], user_regdate=#[userRegdate],  
                user_logincount=#[userLogincount], 
                #if($userName && !$userName.equals("")) 
                    user_type='1', 
                #else  
                    user_type='2', 
                #end  
                remark1=#[remark1], remark2=#[remark2],  
                remark3=#[remark3], remark4=#[remark4], remark5=#[remark5], past_time=#[pastTime],  
                dredge_time=#[dredgeTime], lastlogin_date=#[lastloginDate], worklength=#[worklength], 
                politics=#[politics], istaxmanager=#[istaxmanager], logon_ip=#[logonIp], cert_sn=#[certSn] 
            where user_worknumber=#[userWorknumber] 
        ]]>  
    </property>  
      
    <property name="tdSmUserSelectByWorkNumber">  
        <![CDATA[ 
         
            select user_id as userId from td_sm_user where user_worknumber=? 
        ]]>  
    </property>  
      
    <property name="tdSmOrgSave">  
        <![CDATA[ 
            insert into td_sm_organization  
                (org_id, org_sn, org_name, parent_id, orgnumber, orgdesc,  
                chargeorgid, remark3, remark5, org_tree_level, org_level, org_xzqm)  
            values (#[orgId], #[orgSn], #[orgName], #[parentId], #[orgnumber], #[orgdesc],  
            #[chargeorgid], #[remark3], #[remark5], #[orgTreeLevel], #[orgLevel], #[orgXzqm]) 
        ]]>  
    </property>  
      
    <property name="tdSmOrgUpdate">  
        <![CDATA[ 
            update td_sm_organization  
            set #if($orgName && !$orgName.equals("")) 
                    org_name=#[orgName], 
                #end 
                #if($parentId && !$parentId.equals("")) 
                    parent_id=#[parentId], 
                #end 
                #if($orgnumber && !$orgnumber.equals("")) 
                    orgnumber=#[orgnumber], 
                #end 
                #if($orgdesc && !$orgdesc.equals("")) 
                    orgdesc=#[orgdesc], 
                #end 
                #if($chargeorgid && !$chargeorgid.equals("")) 
                    chargeorgid=#[chargeorgid], 
                #end 
                #if($remark3 && !$remark3.equals("")) 
                    remark3=#[remark3], 
                #end 
                #if($remark5 && !$remark5.equals("")) 
                    remark5=#[remark5], 
                #end 
                #if($orgTreeLevel && !$orgTreeLevel.equals("")) 
                    org_tree_level=#[orgTreeLevel], 
                #end 
                #if($orgLevel && !$orgLevel.equals("")) 
                    org_level=#[orgLevel], 
                #end 
                #if($orgXzqm && !$orgXzqm.equals("")) 
                    org_xzqm=#[orgXzqm], 
                #end 
                org_id=#[orgId] 
            where org_id=#[orgId]  
        ]]>  
    </property>  
      
    <property name="tdSmOrgSelect">  
        <![CDATA[ 
            select org_id as orgId, org_sn as orgSn, parent_id  as parentId              
            from td_sm_organization where org_id=#[orgId] 
        ]]>  
    </property>  
      
    <property name="tdSmOrgAllData">  
        <![CDATA[ 
            select org_id as orgId, org_sn as orgSn, parent_id  as parentId              
            from td_sm_organization order by org_id 
        ]]>  
    </property>  
      
    <property name="tdSmJobSave">  
        <![CDATA[ 
            insert into td_sm_job 
                (job_id, job_name, job_desc) 
            values (#[jobId], #[jobName], #[jobDesc]) 
        ]]>  
    </property>  
      
    <property name="tdSmJobUpdate">  
        <![CDATA[ 
            update td_sm_job  
            set job_name=#[jobName] , job_desc=#[jobDesc]  
            where job_id=#[jobId]  
        ]]>  
    </property>  
      
    <property name="tdSmJobSelect">  
        <![CDATA[ 
            select * from td_sm_job where job_id=#[jobId] 
        ]]>  
    </property>  
      
    <property name="tdSmOrgJobSave">  
        <![CDATA[ 
            insert into td_sm_orgjob 
                (job_id, org_id, job_sn) 
            values (#[jobId], #[orgId], #[jobSn]) 
        ]]>  
    </property>  
      
    <property name="tdSmOrgJobUpdate">  
        <![CDATA[ 
            update td_sm_orgjob  
            set org_id=#[orgId], job_sn=#[jobSn] 
            where job_id=#[jobId]  
        ]]>  
    </property>  
      
    <property name="tdSmOrgJobSelect">  
        <![CDATA[ 
            select * from td_sm_orgjob where job_id=#[jobId] 
        ]]>  
    </property>  
      
    <property name="tdSmOrgUserSave">  
        <![CDATA[ 
            insert into td_sm_orguser 
                (user_id, org_id) 
            values (#[userId], #[orgId]) 
        ]]>  
    </property>  
      
    <property name="tdSmOrgUserUpdate">  
        <![CDATA[ 
            update td_sm_orguser  
            set org_id=#[orgId] 
            where user_id=#[userId]  
        ]]>  
    </property>  
      
    <property name="tdSmOrgUserSelect">  
        <![CDATA[ 
            select * from td_sm_orguser where user_id=#[userId] 
        ]]>  
    </property>  
      
    <property name="tdSmUserJobOrgSave">  
        <![CDATA[ 
            insert into td_sm_userjoborg 
                (user_id, org_id, job_id, job_starttime) 
            values (#[userId], #[orgId], #[jobId], #[jobStartTime]) 
        ]]>  
    </property>  
      
    <property name="tdSmUserJobOrgUpdate">  
        <![CDATA[ 
            update td_sm_userjoborg  
            set org_id=#[orgId], job_id=#[jobId], job_starttime=#[jobStartTime] 
            where user_id=#[userId]  
        ]]>  
    </property>  
      
    <property name="tdSmUserJobOrgSelect">  
        <![CDATA[ 
            select * from td_sm_userjoborg where user_id=#[userId] 
        ]]>  
    </property>  
      
    <property name="selectTdSmUserKey">  
        <![CDATA[ 
            select user_id,USER_WORKNUMBER,user_type from td_sm_user  
        ]]>  
    </property>  
      
    <property name="selectTdSmOrgKey">  
        <![CDATA[ 
            select org_id,org_tree_level from td_sm_organization order by org_tree_level  
        ]]>  
    </property>  
      
    <property name="selectTdSmJobKey">  
        <![CDATA[ 
            select job_id from td_sm_job 
        ]]>  
    </property>  
      
    <property name="selectTdSmOrgUserKey">  
        <![CDATA[ 
            select distinct(user_id) from td_sm_orguser 
        ]]>  
    </property>  
      
    <property name="selectTdSmOrgJobUserKey">  
        <![CDATA[ 
            select distinct(user_id) from td_sm_userjoborg 
        ]]>  
    </property>  
</properties>  

```

#### **2.加载配置文件初始化bboss 持久层通用dao**

bboss sql配置文件遵循bboss ioc 语法，dao加载sql文件：

Java代码 

```java
com.frameworkset.common.poolman.ConfigSQLExecutor dao= new com.frameworkset.common.poolman.ConfigSQLExecutor("com/pdp/masterdata/hr/dao/HumanDataSql.xml");  
//指定dbname  
List<HashMap> datas = dao.queryListWithDBName(HashMap.class,dbname, "selectTdSmOrgKey");  
            for(int i = 0; datas != null && i < datas.size(); i ++)  
            {  
                        System.out.println(datas.get(i));  
            }  
        } catch(SQLException e) {  
            e.printStackTrace();  
        }  
  
//不指定dbname  
List<HashMap> datas = dao.queryList(HashMap.class, "selectTdSmOrgKey");  
            for(int i = 0; datas != null && i < datas.size(); i ++)  
            {  
                        System.out.println(datas.get(i));  
            }  
        } catch(SQLException e) {  
            e.printStackTrace();  
        }  
```

加载配置文件的dao的具体api使用，可以参考文档：http://yin-bp.iteye.com/blog/1112997