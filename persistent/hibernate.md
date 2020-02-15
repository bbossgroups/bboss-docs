### bboss 事务框架托管hibernate事务保存更新操作失效问题解决办法

bboss 事务框架托管hibernate事务保存更新操作失效问题解决办法

采用bboss 事务框架托管hibernate事务时在执行tm.commit()之前需要调用一下hibernate session对象的flush方法，否则会导致hibernate的更新保存失效的问题：

Java代码

```java
TransactionManager tm =  new TransactionManager();    
try    
{    
    tm.begin();    
    //业务操作1，采用bboss持久层  
    //业务操作2，采用hibernate  
    //工作流程操作，基于activiti 5.9工作流引擎，采用mybatis  
    session.flush();//务必事务提交之前执行该语句，否则hibernate的更新保存操作无效  
    tm.commit();    
    DBUtil.debugStatus();    
}    
catch(Throwable e)    
{    
    try {    
        tm.rollback();    
    } catch (RollbackException e1) {    
            
        e1.printStackTrace();    
    }    
}    
```

各位可能会吐槽为什么会有这么多框架然到一起，呵呵，这是历史遗留问题，为了保证事务一致性，只好借助于bboss的通用事务管理框架了。