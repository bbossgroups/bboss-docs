### bboss aop 远程服务介绍-网络环境

bboss 项目下载地址：

https://sourceforge.net/project/showfiles.php?group_id=238653

网络的要求就是要求bboss aop中的远程管理组件能够在各个服务器之间进行通讯，根据采用的不同网络协议，说明如下：

Udp协议：确保能够进行udp通讯，如果你的机器之间装有防火墙之类的软件，必须允许udp数据包的传递。

Tcp协议：确保能够进行tcp通讯，如果你的机器之间装有防火墙之类的软件，必须允许tcp数据包的传递。

另外，为了确保远程服务调用能够正确执行，还要求远程服务方法的返回值和参数必须实现java.io.Serializable接口。