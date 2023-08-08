### bboss aop 实践（2）属性依赖注入

本系列文件之二 介绍bboss aop框架中依赖注入(ioc)功能的使用方法

本系列文章适用于bboss-aop-1.0.5，下载地址：

[https://sourceforge.net/project/showfiles.php?group_id=238653&package_id=290546&release_id=658454](https://sourceforge.net/project/showfiles.php?group_id=238653&amp;amp;amp;package_id=290546&amp;amp;amp;release_id=658454)

bboss aop提供两种方式的依赖注入：

​     属性依赖注入

​     构建函数依赖注入

另外还举例说明了bboss aop框架是怎么防止循环依赖注入的。下面逐一说明。

属性依赖注入

首先定义两个组件接口和组件实现

​    组件com.bboss.spi.reference.A和组件接口com.bboss.spi.reference.AI

​    组件com.bboss.spi.reference.B和组件接口com.bboss.spi.reference.BI

   组件A中引用了组件接口BI的一个引用，另外A还引用了一个普通的java对象

com.bboss.spi.reference.Test的变量test。

   我们通过bboss aop框架来管理组件A和组件B，并且通过属性注入的方式初始化A对BI的引用属性，初始化Test变量。

   假设上述类接口都已经定义好，组件也已经实现。

   我们直接来看看怎么来编写bboss aop配置文件：

 < ?xml version="1.0" encoding='gb2312'? >

< manager-config >

​        < manager id="a" singlable="false" >

​                <!--
​                        基于数据库的用户管理实现

​                        属性描述：

​                        type：代表数据存储的类型,例如DB，LDAP,ACTIVEDIRECTORY等等            

​                    class：实现类代码

​            -->
​            < provider type="DB"class="com.bboss.spi.reference.A" />

​            < reference fieldname="b" refid="b" />

​            < reference fieldname="test" class="com.bboss.spi.reference.Test"/>               

​    </ manager >     

​    < manager id="b" singlable="false" >

​            <!--

​                       基于数据库的用户管理实现
​                        属性描述：
​                        type：代表数据存储的类型,例如DB，LDAP,ACTIVEDIRECTORY等等
​                        class：实现类代码
​                                        -->
​                < provider type="DB"                         class="com.bboss.spi.reference.B" />

​            </ manager> 

</ manager-config >

可以将上述文件存储在一个名叫manager-provider-reference.xml文件中放在工程的src目录下，同时将该文件通过manager-provider.xml主文件导入即可，manager-provider.xml，文件添加以下行即可：

​        < managerimport file="manager-provider-reference.xml" />

配置文件写好后就可以通过com.bboss.spi.BaseSPIManager组件来获取AI的实例对象了：

​            try {

​                    AI a = (AI)BaseSPIManager.getProvider("a");

​                    System.out.println("a:" + a);

​                    System.out.println("a:" +a.getB());

​                    System.out.println("a.getTest():" +a.getTest());

​            } catch (SPIException e) {

​                    // TODO Auto-generated catch block

​                    e.printStackTrace();

​            }

  附带几个类的定义：

package com.bboss.spi.reference;

public class A implements AI{

​        private BI b;

​        private Test test;

​        public void setB(BI b) {

​                this.b = b;

​        }

​        public BI getB() {

​                return b;

​        }

​        public void setTest(Test test) {

​                this.test = test;

​        }
​        public Test getTest() {

​                return this.test;
​        }

}


package com.bboss.spi.reference;

public interface AI {

​        public void setB(BI b) ;

​        public BI getB() ;

​        public void setTest(Test test);

​        public Test getTest();

}

package com.bboss.spi.reference;

public class B  implements BI{

​        private CI c;

​        public void setC(CI c) {

​                this.c = c;

​        }

​        public CI getC() {

​                return c;

​        }

}

package com.bboss.spi.reference;

public interface BI {

​        public void setC(CI c);

​        public CI getC() ;

}

package com.bboss.spi.reference;

public class Test {

}