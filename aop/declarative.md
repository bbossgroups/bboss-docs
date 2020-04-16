### bboss aop 实践(6) 声明式事务管理

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

Bboss aop作为一个轻量级的aop框架，一个非常重要的功能就是结合bboss persistent框架实现数据库声明式事务管理功能，本节就详细地介绍这个功能。

在介绍声明式事务管理功能之前，先简单介绍一下bboss persistent持久层框架提供的事务管理功能，详细的情况请参考博客文章《bboss persistent事务管理介绍》。

**bboss persistent持久层框架事务管理功能介绍**

bboss persistent框架提供了四种类型的事务管理类型，业务组件可以根据自己的需要进行相应的选择，这四种类型分别是：

a.    必须创建新的事务（NEW_TRANSACTION）

b.    有事务就加入当前事务，没有就不创建事务（MAYBE_TRANSACTION）

c.    有事务就加入当前事务，没有就创建事务（REQUIRED_TRANSACTION）（默认情况）

d.    没有事务（NO_TRANSACTION）

Bboss persistent的事务管理功能主要是由组件

com.frameworkset.orm.transaction.TransactionManager提供。那么应用程序可以根据自己的采用可编程的方式来实现自己的数据库事务管理，也可以通过声明式的方式来进行事务管理。可编程的方式方式又包含两种情况：

a.    直接使用TransactionManager组件来开启事务、提交/回滚事务

b.   采用事务管理模板组件来管理事务，bboss persistent持久层框架提供了以下组件来实现这个功能：

​    com.frameworkset.common.poolman.JDBCTemplate  不带返回值模板接口

​     com.frameworkset.common.poolman.JDBCValueTemplate  带返回值的模板接

​     com.frameworkset.common.poolman.TemplateDBUtil  提供执行上述两种模板接口的事务上下文环境，具体的使用方法请参考博客文章《bboss persistent事务管理介绍》和《bboss persistent持久层框架组件介绍》这里就不多说了。 

声明式事务管理
了解了bboss persistent持久层框架的事务管理功能后，我们就来详细地介绍bboss aop框架提供的声明式事务管理功能。bboss aop框架为声明式事务管理定义了一个数据库事务管理拦截器：

com.chinacreator.spi.interceptor.TransactionInterceptor

通过该拦截器来管理业务组件中的数据库事务，这样应用程序只需要在bboss aop的配置文件中声明相应类型的数据库事务即可，而不需要在业务组件中显示地管理数据库事务。可以在业务方法中使用所有的bboss persistent持久层框架中的所有组件来操作数据库，这些操作都将在声明的事务环境中执行。

下面是声明式事务配置的语法：

transactions(method+)   

​    method(param*,rollbackexceptions?)

​    method-attributelist{

​       name-方法名称，name和pattern不能同时出现

​       pattern-匹配方法名称的正则表达式

​       txtype-需要控制的事务类型，取值范围：

​                  NEW_TRANSACTION，

​                  REQUIRED_TRANSACTION，

​                  MAYBE_TRANSACTION，

​                  NO_TRANSACTION

​    }

​    param(pcdata)

​    param-attributelist{

​       type-

​    }

​    rollbackexceptions(exception+) 

​    exception(pcdata)

​    exception-attributelist{

​       class-

​       type-IMPLEMENTS,INSTANCEOF

}

只需要在配置文件中配置需要进行事务管理的业务方法即可，配置的内容包括：

l         方法名称和方法的参数（必须）

l         声明的方法的事务类型（可选，默认为REQUIRED_TRANSACTION）

l         回滚事务的异常(可选，不配置时表示所有的异常都会回滚事务)

除了配置了回滚异常外，还可以通过type属性配置回滚异常的是否包含异常类型的子异常类型：

  IMPLEMENTS—不包含

  INSTANCEOF—包含

声明式事务管理使用实例

定义业务组件

在组件方法中操作数据库，这个方法将在配置文件中声明事务

组件接口

package com.chinacreator.spi.transaction; 

public interface AI {   

​    public void testTXInvoke(String msg)throws Exception;   

​    public void testTXInvoke() throws Exception;

​    public void testNoTXInvoke()throws Exception;   

​    public String testTXInvokeWithReturn()throws Exception;   

​    public String testTXInvokeWithException() throws Exception;

​       public void testSameName()throws Exception;

​    public void testSameName(String msg)throws Exception;

​       public void testSameName1()throws Exception;

​    public void testSameName1(String msg)throws Exception;

​    /**

​     \* 混合异常测试，即包含实例异常，也包含子类和实例异常

​     \* 所有的异常都将导致事务回滚

​     */

​    public void testTXWithSpecialExceptions(String type) throws Exception;

​    /**

​     \* 只要是特定实例的异常就会回滚

​     \* @param type

​     \* @throws Exception

​     */

​    public void testTXWithInstanceofExceptions(String type) throws Exception;

​    /**

​     \* 只有异常本身的实例异常才触发事务的回滚

​     \* @param type

​     \* @throws Exception

​     */

​    public void testTXWithImplementsofExceptions(String type) throws Exception;   

​    public void testPatternTX1(String type) throws Exception;   

​    public void testPatternTX2(String type) throws Exception;   

​    public void testPatternTX3(String type) throws Exception;

​    public void testPatternTX4(String type) throws Exception;

​    public void testSystemException() throws Exception;

}

组件实现

package com.chinacreator.spi.transaction; 

import java.sql.SQLException; 

import com.frameworkset.common.poolman.DBUtil; 

public class A1 implements AI{ 

​    public void testTXInvoke(String msg) throws Exception {

​       System.out.println("A1:" + msg);            

​       DBUtil db = new DBUtil();

​       System.out.println("db.getNumIdle():" +db.getNumIdle());

​       System.out.println("db.getNumActive():" +db.getNumActive());

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXInvoke(" + msg +")')");

​       db.executeInsert("insert into test1(A) values('testTXInvoke(" + msg +")')");

​       System.out.println("1db.getNumActive():" +db.getNumActive());

​       System.out.println("1db.getNumIdle():" +db.getNumIdle());      

​    }

​    public void testTXInvoke() throws SQLException {

​       System.out.println("A1.testTXInvoke():no param");

​       DBUtil db = new DBUtil();

​       System.out.println("db.getNumIdle():" +db.getNumIdle());

​       System.out.println("db.getNumActive():" +db.getNumActive());

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXInvoke()')");

​       db.executeInsert("insert into test1(A) values('testTXInvoke')");

​       System.out.println("1db.getNumActive():" +db.getNumActive());

​       System.out.println("1db.getNumIdle():" +db.getNumIdle());      

​    }   

​    public void testNoTXInvoke()

​    {

​       System.out.println("A1:NoTXInvoke");

​       DBUtil db = new DBUtil();

​       try {

​           String id = db.getNextStringPrimaryKey("test");

​           db.executeInsert("insert into test(id,name) values('"+id+"','testTXInvoke()')");

​           db.executeInsert("insert into test1(A) values('testTXInvoke()')");

​       } catch (SQLException e) {

​           // TODO Auto-generated catch block

​           e.printStackTrace();

​       }      

​    }

​    public String testTXInvokeWithReturn() {

​       System.out.println("call A1.testTXInvokeWithReturn()");

​       DBUtil db = new DBUtil();

​       try {

​           String id = db.getNextStringPrimaryKey("test");

​           db.executeInsert("insert into test(id,name) values('"+id+"','testTXInvokeWithReturn()')");

​           db.executeInsert("insert into test1(A) values('testTXInvokeWithReturn()')");

​       } catch (SQLException e) {

​           // TODO Auto-generated catch block

​           e.printStackTrace();

​       }

​       return "return is A1";

​    }

   

​    /**

​     \* 只要抛出异常，事务就回滚

​     */

​    public String testTXInvokeWithException() throws Exception {

​       System.out.println("call A1.testTXInvokeWithException()");

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXInvokeWithException()')");

​       if(true)

​           throw new Exception1("A1 throw a exception");

​       return "A1 exception find.";

​    }

 

​    public void testSameName() throws SQLException {

​       System.out.println("call A1.testSameName()");

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");      

​       db.executeInsert("insert into test(id,name) values('"+id+"','testSameName()')");

​    }

 

​    public void testSameName(String msg) throws SQLException {

​       System.out.println("call A1.testSameName("+msg+")");

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testSameName("+msg+")')");

​      

​    }

 

​    public void testSameName1() throws SQLException {

​       System.out.println("call A1.testSameName1()");

​      

​       DBUtil db = new DBUtil();

​      

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testSameName1()')");

​      

​    }

 

​    public void testSameName1(String msg) throws SQLException {

​       System.out.println("call A1.testSameName1(String msg):" + msg);

​       DBUtil db = new DBUtil();

​      

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testSameName1("+msg+")')");

​      

​    }

 

​    public int testInt(int i) {

​       System.out.println("call A1.testInt(int i)：" + i);

​       return i;

​      

​    }

   

​    public int testIntNoTX(int i) {

​       System.out.println("call A1.testIntNoTX(int i)：" + i);

​       return i;    

​    }

   

​    /**

​     \* 混合异常测试，即包含实例异常，也包含子类和实例异常

​     \* 所有的异常都将导致事务回滚

​     */

​    public void testTXWithSpecialExceptions(String type) throws Exception

​    {

​      DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXWithSpecialExceptions("+type+")')");

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务回滚

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​      

​       //事务回滚

​       if(type.equals("exception1"))

​       {

​           throw new Exception1("IMPLEMENTS exception1");

​       }

​       /**

​        \* 事务不会回滚，没有进行配置

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​      

​    }

   

​    /**

​     \* 只要是特定实例的异常就会回滚

​     \* @param type

​     \* @throws Exception

​     */

​    public void testTXWithInstanceofExceptions(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXWithInstanceofExceptions(" + type + ")')");

​      

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务回滚

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

   

​    /**

​     \* 只有异常本身的实例异常才触发事务的回滚

​     \* @param type

​     \* @throws Exception

​     */

​    public void testTXWithImplementsofExceptions(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testTXWithImplementsofExceptions(" + type + ")')");

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务不会回滚，提交

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

   

​    public void testPatternTX1(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testPatternTX1(" + type + ")')");

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务不会回滚，提交

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

   

​    public void testPatternTX2(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testPatternTX2(" + type + ")')");

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​     //事务不会回滚，提交

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

   

​    public void testPatternTX3(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testPatternTX3(" + type + ")')");

​       //事务回滚

​       if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务不会回滚，提交

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

​    public void testPatternTX4(String type) throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testPatternTX4(" + type + ")')");

​       //事务回滚

​      if(type.equals("IMPLEMENTS"))

​       {

​           throw new RollbackInstanceofException("IMPLEMENTS RollbackInstanceofException");

​       }

​      

​       //事务不会回滚，提交

​       if(type.equals("INSTANCEOF"))

​       {

​           throw new SubRollbackInstanceofException("INSTANCEOF RollbackInstanceofException");

​       }

​       /**

​        \* 事务不会回滚，提交

​        */

​       if(type.equals("notxexception"))

​       {

​           throw new Exception3("notxexception Exception3");

​       }

​    }

   

​    /**

​     \* 针对系统级别的异常，事务自动回滚

​     \* 本方法声明了事务回滚异常

​     \* < method name="testSystemException">

​              < rollbackexceptions >

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions >

​           </ method >

​          方法中抛出了系统级别的空指针异常，将导致事务回滚

​     */

​    public void testSystemException() throws Exception

​    {

​       DBUtil db = new DBUtil();

​       String id = db.getNextStringPrimaryKey("test");

​       db.executeInsert("insert into test(id,name) values('"+id+"','testSystemException()')");

//     throw new java.lang.NullPointerException("空指针异常。事务回滚");

​      

​    }

} 

业务异常定义

package com.chinacreator.spi.transaction;

 

public class Exception1 extends Exception {

​    public Exception1(String msg)

​    {

​       super(msg);

​    }

 

} 

package com.chinacreator.spi.transaction; 

public class Exception3 extends Exception{

​    public Exception3(String msg)

​    {

​       super(msg);

​    }

} 

配置业务组件，配置业务方法的声明式事务

建立xml配置文件manager-transaction.xml,存放在包路径com.chinacreator.spi.transaction下，文件的内容如下： 

< ? xml version="1.0" encoding='gb2312'? >

<!--

声明式事务方法配置文件，包含正则表达式指定的方法名称

-->

< manager-config >

​    < manager id="tx.a" singlable="true"  >    

​       < provider type="DB"         class="com.chinacreator.spi.transaction.A1" />

​       <!-- <provider type="ldap" used="true"

​           class="com.chinacreator.spi.tx.A2" /> -->

​       <!-- 

​           在下面的节点对provider的业务方法事务进行定义

​           只要将需要进行事务控制的方法配置在transactions中即可

​       -->

​       <transactions>

​           <!--

​              定义需要进行事务控制的方法

​              属性说明：

​              name-方法名称，可以是一个正则表达式，正则表达式的语法请参考jakarta-oro的相关文档，如果使用

​              正则表达式的情况时，则方法中声明的方法参数将被忽略，但是回滚异常有效。

​              pattern-方法名称的正则表达式匹配模式，模式匹配的顺序受配置位置的影响，如果配置在后面或者中间，

​                     那么会先执行之前的方法匹配，如果匹配上了就不会对该模式方法进行匹配了，否则执行匹配操作。

​                     如果匹配上特定的方法名称，那么这个方法就是需要进行事务控制的方法

​                     例如：模式testInt.*匹配接口中以testInt开头的任何方法

​              txtype-需要控制的事务类型，取值范围：

​            NEW_TRANSACTION，

​              REQUIRED_TRANSACTION，

​              MAYBE_TRANSACTION，

​              NO_TRANSACTION

​           -->

​           < method name="testTXInvoke" txtype="NEW_TRANSACTION">

​              < param type="java.lang.String" />

​           </ method >            

​           < method name="testTXInvoke" txtype="REQUIRED_TRANSACTION" />

​          

​           < method name="testTXInvokeWithReturn" txtype="REQUIRED_TRANSACTION" />

​          

​           < method name="testTXInvokeWithException" txtype="MAYBE_TRANSACTION" />    

​          

​           < method name="testSameName" txtype="NO_TRANSACTION" />

​          

​           < method name="testSameName1" >

​             < param type="java.lang.String" />

​           </ method >

​           <!--             

​                  定义使事务回滚的异常,如果没有明确声明需要回滚事务的异常，那么当有异常发生时，事务管理框架将自动回滚当前事务

​                  class-异常的完整类路径

​                  type-是否检测类型异常的子类控制标识，

​                  IMPLEMENTS只检测异常类本身，忽略异常类的子类；

​                  INSTANCEOF检查异常类本省及其所有子类

​                 

​              -->

​           < method name="testTXWithSpecialExceptions" >

​              < rollbackexceptions >

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="INSTANCEOF"/>

​                  <exception class="com.chinacreator.spi.transaction.Exception1"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions >

​              < param type="java.lang.String" />

​           </ method >

​           <method name="testTXWithInstanceofExceptions">

​              <rollbackexceptions>

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="INSTANCEOF"/>

​              </ rollbackexceptions >

​              < param type="java.lang.String" />

​           </ method >    

​           < method name="testTXWithImplementsofExceptions">

​              < rollbackexceptions >

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions>

​              < param type="java.lang.String" />

​           </ method >

​           <!--

​                 如果涉及的方法名称是一个正则表达式的匹配模式，则无需配置方法参数

​                  如果指定的方法没有参数则无需指定参数

​                  注意参数出现的位置顺序和实际方法定义的参数顺序保持一致

​                  模式为* 表示匹配所有的方法

​              -->             

​           <!--

​              通过模式方法进行声明式事务控制，同时声明了需要回滚事务的异常

​              pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法

​            -->           

​           <!--< method pattern="testPatternTX[1-9.]*">

​              < rollbackexceptions >

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions >

​              < param type="java.lang.String" />

​           </ method >-->

​          <!--   通过模式方法进行声明式事务控制

​              pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法      

​               -->  

​           <method pattern="testPatternTX[1-9.]*">             

​              <param type="java.lang.String"/>

​           </method>

​          

​           <!--

​              系统级别的异常java.lang.NullPointException，导致事务回滚

​            -->

​           < method name="testSystemException" >   

​              < rollbackexceptions >

​                  <exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"

​                  type="IMPLEMENTS"/>

​              </ rollbackexceptions >

​           </ method >

​               <!--

​              系统级别的异常java.lang.NullPointException将自动导致事务回滚，不管是否配置了回滚异常

​            -->

​       </ transactions >

​    </ manager >

</ manager-config >

将manager-transaction.xml配置到主文件manager-provider.xml中：

< managerimport file="com/chinacreator/spi/transaction/manager-transaction.xml" /> 

配置完毕后就可以使用业务组件了，声明式事务将作用于业务方法。 

使用带声明式事务的业务组件
public static void testTRANSACTION_TYPE()

​    {

​       try

​       {

​           AI a = (AI)BaseSPIManager.getProvider("tx.a");

​          

​           try

​           {

​              //事务一 REQUIRED_TRANSACTION

​              a.testTXInvoke();

​           }

​           catch(Exception e)

​           {

​              e.printStackTrace();

​           }

​           try

​           {

​              //事务二 NEW_TRANSACTION 内部失败

​              a.testTXInvoke("hello.");

​           }

​           catch(Exception e)

​           {

​              e.printStackTrace();

​           }

​          

​           try

​           {

​              //事务三 REQUIRED_TRANSACTION,

​              a.testTXInvokeWithReturn();

​           }

​           catch(Exception e)

​           {

​              e.printStackTrace();

​           }

​                   

​           try

​           {

​              //事务四 MAYBE_TRANSACTION

​              a.testTXInvokeWithException();

​           }

​           catch(Exception e)

​           {

​              e.printStackTrace();

​           }

​           try

​           {

​              //事务五 NO_TRANSACTION，没有事务环境，内部执行成功则成功，否则失败

​              a.testSameName();

​           }

​           catch(Exception e)

​           {

​              e.printStackTrace();

​           }

​       }

​       catch(Exception e)

​       {

​           e.printStackTrace();

​       }         

​    }

这里简单地举例说明怎么配置业务组件的事务以及简单地访问带事务的业务组件方法，更详细的实例请访问以下地址下载：

[https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290546&release_id=658454](https://sourceforge.net/project/showfiles.php?group_id=238653&amp;amp;amp;package_id=290546&amp;amp;amp;release_id=658454)

到此通过bboss aop框架实现数据库声明式事务管理的功能就介绍完了，相信大家应该有一个初步的了解了，有关数据库操作和事务管理更详细的信息请参考博客中关于bboss persistent持久层框架的其他文章。