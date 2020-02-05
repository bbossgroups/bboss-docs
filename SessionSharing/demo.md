### bboss session版本构建和demo部署运行介绍

#### **1.获取最新版本源码**

假定源码存放目录d:/workspace/security
在命令行执行以下指令(先安装好[git工具](http://dlsw.baidu.com/sw-search-sp/soft/4e/30195/Git-2.7.2-32-bit_setup.1457942412.exe)并配置好环境变量)
cd d:/workspace
git clone -b master --depth 1 https://github.com/bbossgroups/sessiondemo.git

#### **2.构建版本**

先装好[gradle](https://docs.gradle.org/current/userguide/installation.html)并配置好环境变量，然后在命令行执行：
cd d:/workspace/sessiondemo
gradle publish

#### **3.运行demo**

方式1 参考bboss平台视频，在eclipse中启动和运行两个demo：
[视频1](http://www.bbossgroups.com/vidio/download.htm?fileName=%E5%B9%B3%E5%8F%B0jetty%E6%8F%92%E4%BB%B6%E5%AE%89%E8%A3%85%E5%8F%8A%E5%BC%80%E5%8F%91%E8%B0%83%E8%AF%95%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.wmv)
[视频2](http://www.bbossgroups.com/vidio/download.htm?fileName=%E5%B9%B3%E5%8F%B0jetty%E6%8F%92%E4%BB%B6%E5%AE%89%E8%A3%85%E5%8F%8A%E5%BC%80%E5%8F%91%E8%B0%83%E8%AF%95%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E%EF%BC%88%E7%BB%AD%EF%BC%89.wmv)