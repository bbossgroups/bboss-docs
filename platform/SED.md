## boss平台开发环境搭建介绍

bboss平台开发环境搭建和代码生成工具使用介绍，可以参考新版平台的视频教程：

视频下载地址：[下载](http://www.bbossgroups.com/tool/download.htm?fileName=%E5%9F%BA%E4%BA%8Ebboss%E6%96%B0%E7%89%88%E5%B9%B3%E5%8F%B0%E9%A1%B9%E7%9B%AE%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA%E5%92%8C%E4%BB%A3%E7%A0%81%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8%E8%A7%86%E9%A2%91%E6%95%99%E7%A8%8B.rar)
或者从bboss开发群文件共享中获取，加入交流群：21220580
加群提示问题答案:gradle

下面是视频的内容介绍，作为视频的参考说明。

### **1、准备工作**

安装开发环境
jdk,idea
git和gradle并配置相关环境变量，参考文档：http://yin-bp.iteye.com/blog/2313145
新建工作目录 d:/workdir
cd d:/workdir

### **2.获取bboss平台源码**

cd d:/workdir
git clone -b master --depth 1 https://github.com/bbossgroups/bboss-cms.git
或者（oschina网速快）
git clone -b master --depth 1 https://git.oschina.net/bboss/bboss-cms.git

### **3.构建平台**

cd bboss-cms
gradle install
gradle :pdp:releaseArchives
构建完毕后得到两个发布包
dbinit-system-5.0.3.6.zip 数据库初始化工具
pdp-5.0.3.6.war 平台发布包

### **4.搭建平台开发工程**

4.1 建立一个mysql数据库 -mybboss
4.2 下载工程生成工具
cd d:/workdir
git clone -b master --depth 1 https://github.com/bbossgroups/genproject.git
或者（oschina网速快）
git clone -b master --depth 1 https://git.oschina.net/bboss/genproject.git

4.3 构建genproject
cd genproject
gradle tasks --all
gradle releaseRuntimeZip
得到根据第三步生成的两个包生成开发环境的工具：
genproject-4.10.9.zip
请参考视频搭建环境,修改配置文件配置数据库和工程名称及存放目录：
config-gradle.properties

配置好后，执行命令生成平台开发工程：
setup-gradle.bat

### **5.运行开发工程**

本视频以idea开发工具，eclipse的使用方法和视频请访问：

http://yin-bp.iteye.com/blog/2313145

5.1 导入idea
5.2 运行pdp子工程
账号：admin 口令：123456
5.3 debug工程
具体看视频

到此我们的基于bboss平台的开发工程就搭建好了，接下来我们来做一个模板，用代码生成工具自动生成代码

### **6.利用代码生成工具生成一个简单的模块**

6.1 下载代码生成工具
cd d:/workdir
git clone -b master --depth 1 https://github.com/bbossgroups/bboss-gencode.git
或者（oschina网速快）
git clone -b master --depth 1 https://git.oschina.net/bboss/bboss-gencode.git
6.2 构建代码生成工具
cd bboss-gencode
gradle :gencode-web-app:releaseRuntimeZip

6.3 生成模块代码并集成代码到开发工程
启动代码生成工具
配置数据源
准备一张表
表单配置
代码集成
界面风格选择default
6.4 查看运行效果

具体操作看视频