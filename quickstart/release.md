### bboss持久层事务管理组件TransactionManager增加两个release方法

bboss持久层事务管理组件TransactionManager增加release和releasenolog两个方法，可以在finally块中调用它们来释放事务资源，使得bboss持久层框架的编程事务管理变得更加优雅、更加轻松。本文详细介绍之。

最新代码请参考文档获取：
[bbossgroups 项目下载地址](http://yin-bp.iteye.com/blog/1080824)

1.方法声明

组件类

com.frameworkset.orm.transaction.TransactionManager

新加方法声明

Java代码

```java
/** 
     * 在final方法中调用，用来在出现异常时对事务资源进行回收，首先对事务进行回滚， 
     * 然后回收资源（不输出日志），如果事务已经提交和回滚，则不做任何操作 
     */  
    public void releasenolog()  
    /** 
     * 在final方法中调用，用来在出现异常时对事务资源进行回收，首先对事务进行回滚， 
     * 然后回收资源，并将回事日志输出到日志文件中，如果事务已经提交和回滚，则不做任何操 
 
作 
     */  
    public void release()  
```

2.这两个方法的示例

Java代码

```java
public void testTX11() throws Exception  
    {  
        TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin();  
              
            //进行一系列db操作              
            //必须调用commit              
            tm.commit();  
        }  
        catch (Exception e) {              
            throw e;              
        }   
        finally  
        {  
            tm.release();  
        }  
    }  
    public void testTX() throws Exception  
    {  
        TransactionManager tm = new TransactionManager();  
        try {  
            tm.begin(TransactionManager.RW_TRANSACTION);  
              
            //进行一系列db操作   
            tm.commit();  
        }  
        catch (Exception e) {  
              
            throw e;  
              
        }   
        finally  
        {  
            tm.releasenolog();  
        }  
    }  
```

注意

对于RW_TRANSACTION事务在finally块中调用了releasenolog方法后就可以不调用commit方法，finally块中调用的releasenolog方法会自动释放事务，但是调用commit也不会对事务造成影响，记住release方法是必须调用commit方法的，这个就是RW_TRANSACTION事务和其他事务类型的最大区别之一。