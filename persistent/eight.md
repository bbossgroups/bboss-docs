### bboss persistent事务管理介绍 （八）

< method name="testTXWithSpecialExceptions" >             

 < rollbackexceptions >                  

< exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"                  type="INSTANCEOF" />                  

< exception class="com.chinacreator.spi.transaction.Exception1"                  type="IMPLEMENTS" />              

</ rollbackexceptions >              

< param type="java.lang.String" />           

</ method >                     

 < method name="testTXWithInstanceofExceptions" >             

 < rollbackexceptions >                  

< exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"                  type="INSTANCEOF" />             

 </ rollbackexceptions >              

< param type="java.lang.String" />           

</ method >                     

< method name="testTXWithImplementsofExceptions" >

< rollbackexceptions >                  

< exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"                  type="IMPLEMENTS" />               

</ rollbackexceptions >              

< param type="java.lang.String" />           

</ method >           

​        <!--                                   

如果涉及的方法名称是一个正则表达式的匹配模式，则无需配置方法参数                  

如果指定的方法没有参数则无需指定参数                  

注意参数出现的位置顺序和实际方法定义的参数顺序保持一致                  

模式为 *  表示匹配所有的方法              

​     -->

​         <!--              

通过模式方法进行声明式事务控制，同时声明了需要回滚事务的异常             

 pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法           

​    -->                       

​         <!--

< method pattern="testPatternTX[1-9.]*">              

< rollbackexceptions >                  

< exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"                  type="IMPLEMENTS" />              

</ rollbackexceptions >              

< param type="java.lang.String" />           

</ method>-->                   

​       <!--   

通过模式方法进行声明式事务控制              

pattern【testPatternTX[1-9.]*】表示以testPatternTX开头的所有方法                    

-->             

< method pattern="testPatternTX[1-9.]*" >                            

 < param type="java.lang.String" />           

</ method >

​           <!--              

系统级别的异常java.lang.NullPointException，导致事务回滚            

-->           

< method name="testSystemException" >                 

< rollbackexceptions >                  

< exception class="com.chinacreator.spi.transaction.RollbackInstanceofException"                  type="IMPLEMENTS" />              

</ rollbackexceptions >           

</ method>               

​      <!--              

系统级别的异常java.lang.NullPointException，导致事务回滚        

​    -->       

</ transactions >    

</ manager >

</ manager-config>