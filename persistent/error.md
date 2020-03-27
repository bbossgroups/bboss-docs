### mysql 高版本jdbc驱动程序使用PreparedStatement.setBinaryStream方法报错及解决办法

mysql 高于版本jdbc驱动程序 使用PreparedStatement.setBinaryStream方法报错及解决办法。

mysql 使用以下版本驱动程序 在PreparedStatement中执行setBinaryStream方法时报错误：

mysql-connector-java-5.0.8-bin.jar

mysql-connector-java-5.1.13-bin.jar

错误信息如下：

Java代码

```java
com.mysql.jdbc.exceptions.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '? ?±n????#??1}?M????\'dG???zPd m7??‘*4??@?8ê§??à????‘6m?-?  
E?OH??A?—6tl?iq?' at line 1  
    at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1051)  
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3566)  
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3498)  
    at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1959)  
    at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2113)  
    at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2568)  
    at com.mysql.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:2113)  
    at com.mysql.jdbc.PreparedStatement.execute(PreparedStatement.java:1364)  
    at com.frameworkset.commons.dbcp.DelegatingPreparedStatement.execute(DelegatingPreparedStatement.java:169)  
    at com.frameworkset.common.poolman.PreparedDBUtil.innerExecute(PreparedDBUtil.java:1223)  
    at com.frameworkset.common.poolman.PreparedDBUtil.executePrepared(PreparedDBUtil.java:1578)  
    at com.frameworkset.common.poolman.PreparedDBUtil.executePrepared(PreparedDBUtil.java:924)  
    at com.frameworkset.mysql.mysql.testBlobWrite(mysql.java:157)  
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)  
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)  
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)  
    at java.lang.reflect.Method.invoke(Method.java:585)  
    at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)  
    at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)  
    at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)  
    at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:20)  
    at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:28)  
    at org.junit.internal.runners.statements.RunAfters.evaluate(RunAfters.java:31)  
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:70)  
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:44)  
    at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:180)  
    at org.junit.runners.ParentRunner.access$000(ParentRunner.java:41)  
    at org.junit.runners.ParentRunner$1.evaluate(ParentRunner.java:173)  
    at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:28)  
    at org.junit.internal.runners.statements.RunAfters.evaluate(RunAfters.java:31)  
    at org.junit.runners.ParentRunner.run(ParentRunner.java:220)  
    at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:46)  
    at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)  
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:467)  
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:683)  
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:390)  
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:197)  
```

可以使用版本mysql-connector-java-3.1.14-bin.jar，执行就不报错。