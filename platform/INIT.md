### 平台系统管理用户和机构以及用户角色初始化操作指南

目录[-]

- 步骤一：建一个机构

- 步骤二：建立机构岗位关系

- 步骤三：建立用户

- 步骤四：建立用户机构岗位关系

- 步骤五：建立用户机构关系

- 步骤六：建立用户角色关系

  

  系统管理中的基础表主要有以下表：

  td_sm_job  岗位表，存储岗位用；

  td_sm_userjoborg  用户机构岗位表，存储用户、机构、岗位的三维关系表；

  td_sm_orgjob 机构岗位表，存储机构和岗位关系的表；

  td_sm_user 用户表，存储用户基本信息的表；

  td_sm_organization 机构表，存储机构基本信息的表；

  td_sm_orguser 机构用户表，存储机构和用户关系的表；

授权表为:

td_sm_userrole 存储用户和角色间关系的表；

字典表

td_sm_dictdata 存储字典数据的表；

td_sm_dicttype 存储字典类型的表；

往系统管理导入用户，主要分为以下几步：

## 步骤一：建一个机构

首先必须建立一个机构，插入的sql语句为：

(ORG_ID,org_sn,org_name,parent_id,path,layer,children,code,jp,qp,creatingtime,creator,orgnumber,orgdesc,remark1,

remark2,remark3,remark4,remark5,chargeorgid,ispartybussiness,satrapjobid,org_level,  

org_xzqm,org_tree_level,isdirectlyparty,isforeignparty,isjichaparty,isdirectguanhu) values(5,1,'hilaryname',4,'',1,'','','jianpin','quanpin','',1,'bianhao','miaoshu','','',1,'','xianshimingcheng',

红色字体为要修改的地方，以下表格做说明：

| 红色字体         | 字段说明              | 默认值           |
| ---------------- | --------------------- | ---------------- |
| 5                | 机构id                |                  |
| 1                | 机构序号              | 1                |
| hilaryname       | 机构名称              |                  |
| 4                | 机构的父id值          |                  |
| jianpin          | 机构简拼              | 空               |
| quanpin          | 机构全拼              | 空               |
| bianhao          | 机构编号              |                  |
| miaoshu          | 机构描述              | 空               |
| xianshimingcheng | 机构的显示名称        |                  |
| 3                | 机构所在层次，从1开始 |                  |
| 31               | 机构的行政区号        |                  |
| 0\|1\|1\|1       | 机构所在的递归层次    | 特别在下面做说明 |

没有默认值的，都是必填项。对于机构所在的递归层次，插入数据的时候可以填为空值，然后通过程序com.chinacreator.sysmgrcore.purviewmanager.db.OrgQuery.java中的方法

updateOrg(“0”)来自动填充该层次值。

 

存机构信息-td_sm_organization 有几个字段和字典表有关系，这个需要分析一下

## 步骤二：建立机构岗位关系

 存机构和在职岗位关系 （存机构和在职岗位关系）

 insert into td_sm_orgjob(org_id,job_id,job_sn) values(5,1,999)

 红色字体为要修改的地方，以下为说明：

| 红色字体 | 字段说明 | 默认值 |
| -------- | -------- | ------ |
| 5        | 机构的id | 无     |

##  步骤三：建立用户

建一个用户，其语句为：

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | insert into td_sm_user(user_id,user_sn,user_name,user_password,user_realname,user_pinyin,user_sex,user_hometel,user_worktel,user_worknumber,user_mobiletel1,user_mobiletel2,user_fax,user_oicq,user_birthday,user_email,user_address,user_postalcode,user_idcard,user_isvalid,user_regdate,user_logincount,user_type,remark1,remark2,remark3,remark4,remark5,past_time,dredge_time,lastlogin_date,worklength,politics,istaxmanager,logon_ip,cert_sn)values(5,1,'hilary',123456,'shiming','pinyin','M',123,123,'',123,123,123,123,to_date('1996-12-03','yyyy-mm-dd'),'1@a.com','dizhi',123,123,2,to_date('1996-12-03','yyyy-mm-dd'),0,0,'',123,'','asd','sdf','','','','','',0,'','') |

 用户类型（0），用户当前状态（2），性别（F，M）都与字典表数据相关。

 红色字体为要修改的地方，以下表格里做说明：

| 红色字体   | 字段说明              | 默认值                  |
| ---------- | --------------------- | ----------------------- |
| 5          | 登录用户id            |                         |
| 1          | 用户所属的序号        | 1                       |
| 'hilary'   | 用户名                |                         |
| 123456     | 用户密码              | 默认123456              |
| 'shiming'  | 用户真是名称          |                         |
| 'pinyin'   | 用户的拼音            | 空                      |
| 'M'        | 用户性别              | 男为F，女为M，默认为男  |
| 123        | 用户家庭电话          | 空                      |
| 123        | 用户办公室电话        | 空                      |
| 123        | 用户移动电话1         | 空                      |
| 123        | 用户移动电话2         | 空                      |
| 123        | 用户传真              | 空                      |
| 123        | 用户OICQ              | 空                      |
| 1996-12-03 | 用户生日              | 空                      |
| '1@a.com'  | 用户email地址         | 空                      |
| 'dizhi'    | 用户地址              | 空                      |
| 123        | 用户邮编              | 空                      |
| 123        | 用户idcard            | 空                      |
| 2          | 用户当前状态          | 默认为2，即开通状态     |
| 1996-12-03 | 用户注册日期          | 当前日期                |
| 0          | 用户登录次数          | 默认为0                 |
| 0          | 用户类型              | 默认为0，表示是系统用户 |
| 'asd'      | 用户移动号码1的归属地 | 空                      |
| 'sdf'      | 用户移动号码2的归属地 | 空                      |

## 步骤四：建立用户机构岗位关系

存储用户对应的机构岗位关系（在职岗位）-td_sm_userjoborg

插入语句为：  

insert into td_sm_userjoborg(user_id,job_id,org_id,same_job_user_sn,job_sn,job_starttime,JOB_FETTLE)

values(5,1,5,3,999,to_date('2001-12-02','yyyy-mm-dd'),1)

 

红色字体为要修改的地方，以下表格里做说明：

| 红色字体   | 字段说明                             | 默认值 |
| ---------- | ------------------------------------ | ------ |
| 5          | 用户id                               |        |
| 5          | 机构id                               |        |
| 3          | 同一岗位下用户所属的序号，应为递增的 |        |
| 2001-12-02 | 用户该工作开始的日期                 |        |

## 步骤五：建立用户机构关系

对于每一个用户，都必须有一个主机构与其关联。

 存储用户主机构 -td_sm_orguser 

 insert into td_sm_orguser(org_id,user_id) values(5,5)

 红色字体为要修改的地方，以下表格里做说明：

| 红色字体 | 字段说明 | 默认值 |
| -------- | -------- | ------ |
| 5        | 机构id   |        |
| 5        | 用户id   |        |

## 步骤六：建立用户角色关系

这一步可以不执行。如果需要授予用户某个角色，则执行。

将已有的角色授权给用户-td_sm_userrole

 insert into td_sm_userrole(user_id,role_id,resop_origin_userid) values(1,1,1)

红色字体为要修改的地方，以下表格里做说明：

| 红色字体 | 字段说明 | 默认值 |
| -------- | -------- | ------ |
| user_id  | 用户id   |        |
| role_id  | 角色id   |        |

S