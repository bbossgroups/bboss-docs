# Sql模板变量设置字段sql数据类型介绍
sql模板变量#[xxxxx]支持设置数据字段sql类型，通过type属性设置数值类型，使用案例：

   ```sql
   #[collecttime,type=timestamp]
   ```
完整的sql
   ```xml   
       <property name="insertSQLpostgresql">
           <![CDATA[INSERT INTO batchtest1 (id, name, author, content, title, optime, oper, subtitle, collecttime,testInt,ipinfo)
                   VALUES ( #[id],#[operModule],  ## 来源dbdemo索引中的 operModule字段
                            #[author], ## 通过datarefactor增加的字段
                            #[logContent], ## 来源dbdemo索引中的 logContent字段
                            #[title], ## 通过datarefactor增加的字段
                            #[logOpertime], ## 来源dbdemo索引中的 logOpertime字段
                            #[logOperuser],  ## 来源dbdemo索引中的 logOperuser字段
                            #[subtitle], ## 通过datarefactor增加的字段
                            #[collecttime,type=timestamp], ## 通过datarefactor增加的字段
                            #[testInt], ## 通过datarefactor增加的字段
                            #[ipinfo]) ## 通过datarefactor增加的地理位置信息字段
   ]]></property>  
       
   ```

type属性值范围：

   ```java
         string,int,long,double,float,short,date,timestamp,bigdecimal,boolean,byte,blobfile,blob,clobfile,clob,object
   ```

   