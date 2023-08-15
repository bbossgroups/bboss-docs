### 最新版本mysql jdbc驱动包获取表定义信息空指针异常处理方法

  在使用最新的mysql-connector-java 6.1.1时，获取表定义信息会抛出空指针异常：
[2016-12-15 10:19:28][DEBUG][com.frameworkset.common.poolman.util.JDBCPool] load table[td_sm_dicttype]'s metadata.
java.lang.NullPointerException
at com.frameworkset.common.poolman.util.JDBCPool.buildTableMetaData(JDBCPool.java:1705)
at com.frameworkset.common.poolman.util.JDBCPool.getTableMetaDataFromDatabase(JDBCPool.java:1851)
at com.frameworkset.common.poolman.util.JDBCPool.getTableMetaData(JDBCPool.java:201)
at com.frameworkset.common.poolman.util.JDBCPool.getColumnMetaData(JDBCPool.java:217)
at com.frameworkset.common.poolman.sql.PrimaryKey.<init>(PrimaryKey.java:304)
at com.frameworkset.common.poolman.management.BaseTableManager.getPoolTableInfos(BaseTableManager.java:182)
at com.frameworkset.common.poolman.management.BaseTableManager.initTableInfo(BaseTableManager.java:474)
at com.frameworkset.common.poolman.management.PoolManBootstrap.start(PoolManBootstrap.java:196)
at com.frameworkset.common.poolman.management.PoolManBootstrap.start(PoolManBootstrap.java:99)
at com.frameworkset.common.poolman.util.SQLManager.assertLoaded(SQLManager.java:142)
at com.frameworkset.common.poolman.util.SQLManager.requestConnection(SQLManager.java:304)
at com.frameworkset.platform.sysmgrcore.manager.SysmanagerInit.init(SysmanagerInit.java:39)
at com.frameworkset.platform.config.ConfigManager.startSystems(ConfigManager.java:126)
at com.frameworkset.platform.config.ConfigManager.init(ConfigManager.java:90)
at com.frameworkset.platform.config.ConfigManager.getInstance(ConfigManager.java:138)
at com.frameworkset.platform.security.SYSAuthenticateFilter.<init>(SYSAuthenticateFilter.java:39)
at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
at java.lang.Class.newInstance(Class.java:442)
at org.eclipse.jetty.servlet.ServletContextHandler$Context.createFilter(ServletContextHandler.java:1051)
at org.eclipse.jetty.servlet.FilterHolder.doStart(FilterHolder.java:104)
at org.eclipse.jetty.util.component.AbstractLifeCycle.start(AbstractLifeCycle.java:64)
at org.eclipse.jetty.servlet.ServletHandler.initialize(ServletHandler.java:768)
at org.eclipse.jetty.servlet.ServletContextHandler.startContext(ServletContextHandler.java:265)
at org.eclipse.jetty.webapp.WebAppContext.startContext(WebAppContext.java:1242)
at org.eclipse.jetty.server.handler.ContextHandler.doStart(ContextHandler.java:717)
at org.eclipse.jetty.webapp.WebAppContext.doStart(WebAppContext.java:494)
at org.eclipse.jetty.util.component.AbstractLifeCycle.start(AbstractLifeCycle.java:64)
at org.eclipse.jetty.server.handler.HandlerWrapper.doStart(HandlerWrapper.java:95)
at org.eclipse.jetty.server.Server.doStart(Server.java:282)
at org.eclipse.jetty.util.component.AbstractLifeCycle.start(AbstractLifeCycle.java:64)
at net.sourceforge.eclipsejetty.starter.embedded.JettyEmbeddedAdapter.start(JettyEmbeddedAdapter.java:67)
at net.sourceforge.eclipsejetty.starter.common.AbstractJettyLauncherMain.launch(AbstractJettyLauncherMain.java:84)
at net.sourceforge.eclipsejetty.starter.embedded.JettyEmbeddedLauncherMain.main(JettyEmbeddedLauncherMain.java:42)

解决办法：在mysql的连接串中指定参数nullCatalogMeansCurrent=true,
<property name="url"><![CDATA[jdbc:mysql://localhost:3306/bboss?serverTimezone=UTC&useSSL=false&nullCatalogMeansCurrent=true]]></property>

可能还需要指定时区serverTimezone参数否则也会报其他错误：这里指定为serverTimezone=UTC  