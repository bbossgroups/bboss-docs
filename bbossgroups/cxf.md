### bbossgroups cxf Webservice服务管理框架

下面具体来介绍如果通过 bboss aop 框架来管理和发布基于 apache cxf webservice 服务框架的 webservice 服务。

#### 1.1     Webservice 协议配置

对应的配置文件为：

/bbossaop/resources/org/frameworkset/spi/manager-rpc-webservices.xml

文件在 /bbossaop/resources/org/frameworkset/spi/manager-rpc-service.xml 中导入。

<!--

​           导入 webservice 服务配置

​         -->

​       < managerimport file = *"org/frameworkset/spi/manager-rpc-webservices.xml"* />

Webservice 协 议配置包含 ws rpc 协议配置、发布的 webservice 服务配置、 cxf webservice 客服端连接参数配置四部分。分别介绍如下。

#### 1.2     发布 webservice 服务 servlet 配置

rpc 服务器端还 需要在 web.xml 文件中配置发布 webservice 服务的 servlet ：

< servlet >

​       < display-name > cxf </ display-name >

​       < servlet-name > cxf </ servlet-name >

​       < servlet-class > org.apache.cxf.transport.servlet.RPCCXFServlet </ servlet-class >

​       < load-on-startup > 1 </ load-on-startup >

​    </ servlet >

​    < servlet-mapping >

​       < servlet-name > cxf </ servlet-name >

​       < url-pattern > /cxfservices/* </ url-pattern >

​    </ servlet-mapping >

#### 1.3     Cxf webservice 服务控制开 关  

​    < property name = *"rpc.webservice.enable"* value = *"true"* />   

标记是否启用 webservice 服务，为 false 时， 启动 cxf 的 serverlet 将不会加载和发布配置的 webservice 服务（这些 webservice 服务配置在 *cxf.webservices.config* *属性中* ）。如果采用 webservice 协议作为远程服务调用的协议时，     *rpc.webservice.enable* *开关必须配置为* *true* *。*

#### 1.4     webservice 服务配置

​        < property name = *"cxf.webservices.config"* >

​       < list >

​       <!--

​           webservice RPC 服务 , 用来实现业务组件之间的远程服务调用

​           属性说明：

​           name 服务的唯一标识名称

​           singleable 服务组件 的单列模式

​           servicePort 发布的服务端口

​           class 服务的实现类

​           mtom 服务是否 支持附件传输

​              true ：支持， false ：不支持      

​         -->

​           < property name = *"rpc.webservice.RPCCall"*

​                    singlable = *"true"*

​                    servicePort = *"RPCCallServicePort"*              

​                    class = *"org.frameworkset.spi.remote.webservice.RPCCall"* />  

​       </ list >

​    </ property >

#### 1.5     Cxf 客服端连接参数配置

​    < property name = *"cxf.client.config"* enable = *"true"* >

​       < map >

​           < property label = *"connectionTimeout "* name = *"connectionTimeout"*

​              value = *"30000"* class = *"long"* >

​              < description > <![CDATA[ Specifies the amount of time, in milliseconds, that the client will attempt to establish a connection before it times out. The default is 30000 (30 seconds).

0 specifies that the client will continue to attempt to open a connection indefinitely. ]]></ description >

​           </ property >

​           < property label = *"receiveTimeout"* name = *"receiveTimeout"*

​              value = *"60000"* class = *"long"* >

​              < description > <![CDATA[ Specifies the amount of time, in milliseconds, that the client will wait for a response before it times out. The default is 60000.

0 specifies that the client will wait indefinitely. ]]></ description >

​           </ property >

​           < property label = *"autoRedirect"* name = *"autoRedirect"*

​              value = *"false"* class = *"boolean"* >

​              < description > <! [CDATA[ Specifies if the client will automatically follow a server issued redirection. The default is false. ]] ></ description >

​           </ property >

​           < property label = *"maxRetransmits"* name = *"maxRetransmits"*

​              value = *"-1"* class = *"int"* >

​              < description > <! [CDATA[ Specifies the maximum number of times a client will retransmit a request to satisfy a redirect. The default is -1 which specifies that unlimited retransmissions are allowed. ]] ></ description >

​           </ property >

​           < property label = *"allowChunking"* name = *"allowChunking"*

​              value = *"true"* class = *"boolean"* >

​             < description > <![CDATA[ Specifies whether the client will send requests using chunking. The default is true which specifies that the client will use chunking when sending requests.

Chunking cannot be used used if either of the following are true:

http-conf:basicAuthSupplier is configured to provide credentials preemptively.

AutoRedirect is set to true.

In both cases the value of AllowChunking is ignored and chunking is disallowed.

See note about chunking below. ]]></ description >

​           </ property >

#### 1.6     客服端网络代理服务参数配置

​       <!--        

​        <property label="proxyServer" name="proxyServer"-->

​      <!--              

​           value="172.16.17.1" class="String">-->

​          <!--              

​          < description> <! [CDATA[Specifies the URL of the proxy server through which requests are routed. ]] ></ description>-->

​        <!--           

​         </ property>-->

​         <!--          

​           <property label="proxyServerPort" name="proxyServerPort"-->

​        <!--              

​           value="808" class="int ">-->

​        <!--              

​         < description> <! [CDATA[Specifies the port number of the proxy server through which requests are routed. ]]></ description>-->

​        <!--          

​         </ property>-->

​          <!--          

​           <property label="proxyServerType" name="proxyServerType"-->

​       <!--              

value="SOCKS" >-->

​              <!--

​                  指定属性注入时的属性编辑和转换器

​             

​                -->

​           <!--              

​         < editor class="org.frameworkset.spi.remote.webservice.ProxyServerTypeEditor" />-->

​         <!--              

​         < description > <![CDATA[Specifies the type of proxy server used to route requests. Valid values are: -->

​        <!--              

​          HTTP(default) -->

​         <!--              

​          SOCKS -->

​         <!--              

​           ]]></ description >-->

​          <!--          

​           </ property >-->

​       </ map >

​    </ property >

#### 1.7     Ssl 参数配置 - 没有经过测试

​    < property   name = *"ws.ssl.config"* enable = *"false"* >

​       < map >

​           < property name = *"keyManagers"* keyPassword = *"password"* >

​              < list >

​                  < property name = *"keyStore1"* type = *"JKS"* password = *"password"* file = *"src/test/java/org/apache/cxf/systest/http/resources/Morpit.jks"* />

​              </ list >

​           </ property >

​          

​           < property name = *"trustManagers"* keyPassword = *"password"* >

​              < list >

​                  < property name = *"keyStore1"* type = *"JKS"* password = *"password"* file = *"src/test/java/org/apache/cxf/systest/http/resources/Truststore.jks"* />

​              </ list >

​           </ property >

​          

​           < property name = *"cipherSuitesFilter"* >

​              < list >

​                  < property value = *".\*_EXPORT1024_.\*"* />

​                   < property value = *".\*_WITH_DES_.\*"* />

​                   < property value = *".\*_WITH_NULL_.\*"* />

​                   < property value = *".\*_DH_anon_.\*"* />

​              </ list >

​           </ property >

​          

​           < property name = *"userName"* value = *"Betty"* />

​             

​           < property name = *"password"* value = *"password"* />

​       <!--

​           < http:tlsClientParameters >

<p class="MsoNormal" style="margin: 0cm 0cm 0pt; text-

