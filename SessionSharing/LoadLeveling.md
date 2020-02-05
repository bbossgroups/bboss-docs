### apache 2.4.12 负载均衡配置

apache 2.4.12 负载均衡配置比较简单，修改httpd.conf文件相关内容即可，

**首先修改httpd.conf，启用负载均衡相关模块**

LoadModule proxy_module    modules/mod_proxy.so

LoadModule proxy_balancer_module modules/mod_proxy_balancer.so

LoadModule proxy_http_module  modules/mod_proxy_http.so

LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so

**然后在httpd.conf文件末尾增加以下内容**：

ProxyRequests Off

ProxyPass **/shmp** balancer://proxy

ProxyPassReverse /shmp balancer://proxy

<Proxy balancer://proxy>

BalancerMember http://127.0.0.1:8081/shmp loadfactor=1

BalancerMember http://127.0.0.1:8082/shmp loadfactor=1

/Proxy

  其中的shmp表示应用的上下文。

通过以上配置可以实现简单的负载均衡功能，BalancerMember 个数可以任意扩展，不管后台的应用服务器是tomcat还是weblogic还是其他的应用服务器，这里配置也没有启用session stiky之类的机制，因为我们可以结合bboss会话共享框架，轻松实现集群应用之间的session共享，详细资料可参考博客文档：  

[bboss会话共享demo使用指南](http://yin-bp.iteye.com/blog/2087308)

[bboss会话共享培训文档](http://yin-bp.iteye.com/blog/2177475)

[bboss session共享使用方法介绍](http://yin-bp.iteye.com/blog/2064662)