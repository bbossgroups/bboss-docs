### bboss persistent事务管理介绍 （四）

- ​      **不带返回值的模板方法使用实例**

TemplateDBUtil.*executeTemplate*(             

 **new** JDBCTemplate(){                  

​              /**                  

*整个execute()方法的执行都会被包含在一个数据库事务中                  

*当有异常抛出时TemplateDBUtil.executeTemplate()方法就

*会自动回滚整个数据库事务，                   

*当整个方法正常结束后，事务就会自动被提交。                   

*通过模板工具提供的便利，开发人员不需要编写自己的事务代码就

*可以顺利地实现数据库的事务性操作 

​                  */

​               **public** **void** execute() **throws** Exception {                     

DBUtil dbUtil = **new** DBUtil();                                         

PreparedDBUtil db = **null**;                   

  **try** {                       

  **for** (**int** i = 0; i < 10; i++) {                           

 db = **new** PreparedDBUtil();                                

db.preparedInsert(

​                                 "insert into td_reg_bank_acc_bak (create_acc_time,starttime,endtime) values(?,?,?)");       

​                         Date today = **new** Date(**new** java.util.Date().getTime());                                

db.setDate(1, **new** java.util.Date());                                

db.setDate(2, **new** java.util.Date());                                

db.setDate(3, **new** java.util.Date());                                

db.executePrepared();                         

​    }                    

 }                     

**catch**(Exception e)                   

  {                        

 **throw** e;//抛出异常，将导致整个数据库事务回滚                    

 }                   

  **try**                    

 {                         

dbUtil.executeInsert("insert into td_reg_bank_acc_bak (clob1,clob2) values('aa','bb')");                    

 }                   

  **catch**(Exception e)                    

 {                        

 **throw** e;//抛出异常，将导致整个数据库事务回滚                   

  }                                        

//方法顺利执行完成，事务自动提交                 

​       }                         

  }           

);