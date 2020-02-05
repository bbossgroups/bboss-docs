### bboss 与ecipse gradle buildship插件结合使用方法

 本文介绍bboss 与ecipse gradle buildship插件结合使用方法，但是建议使用gradle sts插件来在eclipse中构建和开发使用bboss框架的项目：[点击浏览](http://yin-bp.iteye.com/blog/2313145)
gradle buildship和Gradle IDE Pack（bboss官方推荐，个人感觉Gradle IDE Pack的任务面板更加符合大众的操作习惯，基于此推荐Gradle IDE Pack）是目前比较流行的两个eclipse gradle插件
环境准备：
在命令行执行以下指令(先安装好[git工具](http://dlsw.baidu.com/sw-search-sp/soft/4e/30195/Git-2.7.2-32-bit_setup.1457942412.exe)并配置好环境变量)

![](images/bboss/3e8f14a6-c21a-3ddf-995a-c66d0707827e.png)

  **下载bboss bestpractice源码**
假定源码存放目录d:/workspace/

cd d:/workspace
git clone -b master --depth 1 https://github.com/bbossgroups/bestpractice.git

**1.eclipse中安装gradle buildship插件（gradle官方推荐,bboss不推荐）并导入bestpractice工程**
**1.1 安装gradle buildship插件**  

![](images/bboss/8768b119-4818-367d-bf3b-999331537eff.png)

**1.2 通过gradle bs将bestpractice工程导入eclipse**

![](images/bboss/8701df1f-e355-33a6-b7a6-de17f2ce9ec9.png)

![](images/bboss/a4ae5e7f-1ee4-3d48-aead-46e59987f5ce.png)

![](images/bboss/1e772d40-60b8-38b4-9311-4769b058e340.png)

![](images/bboss/470955c1-0727-3803-a057-d0c0d5db9647.png)

![](images/bboss/babe0bfb-d670-39f0-bf61-8dab97094001.png)

![](images/bboss/ec33dc1a-ab55-3f46-987e-f34fa05aca8c.png)

**1.3 运行bestpractice/mvc gradle任务：部署和启动mvc web应用**

![](images/bboss/785dc1e9-199f-34b7-88ff-6d98901b0942.png)

**1.4 通过gradle sts结合gretty插件调试web应用**

打开gradle tasks面板

![](images/bboss/1ca72901-d7d1-3108-803d-138077668a37.jpg)

![](images/bboss/dd9bf144-5899-3efd-99e3-3aa150113bd2.jpg)

选择要调试的工程并启动调试端口：

![](images/bboss/6c587988-a53f-3fe2-a655-0e9994921d28.jpg)

![](images/bboss/6287f07a-96bb-30ee-bdd3-8c4ef4cf7677.jpg)

设置需要调试应用的端口：

![](images/bboss/e9dc0b14-7e39-3e31-b938-8af8f06924bb.jpg)

![](images/bboss/11897a7b-e73b-3e9a-951f-409a34618fb9.jpg)

![](images/bboss/18182c65-2bd0-3846-b4ce-490a9d88b7d3.jpg)

![](images/bboss/04f4078b-ae63-3c83-9fbf-43d30ff318c5.jpg)

这样调试应用就启动起来了，可以在浏览器中访问应用并开始调试工作了，这种调试方法比较烦锁，运行jetty容器主程序来启动和调试应用的快捷和轻量级方法。