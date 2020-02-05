### bboss gradle工程导入eclipse介绍

本文适用于bboss所有模块和bboss平台

环境准备：
在命令行执行以下指令(先安装好[git工具](https://github-production-release-asset-2e65be.s3.amazonaws.com/23216272/e121de66-7e14-11e7-8946-891e0e576248?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20170816%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20170816T081243Z&X-Amz-Expires=300&X-Amz-Signature=8a77b08c1ef7e673c2c16f65ae471f334945eba242a60cd1228f940144e0202e&X-Amz-SignedHeaders=host&actor_id=526867&response-content-disposition=attachment%3B filename%3DGit-2.14.1-64-bit.exe&response-content-type=application%2Foctet-stream)并配置好环境变量)

![](images/bboss/3e8f14a6-c21a-3ddf-995a-c66d0707827e1.png)

#### 1.下载bboss源码**

假定源码存放目录d:/workspace/

cd d:/workspace
git clone -b master --depth 1 https://github.com/bbossgroups/bboss.git

继续后续步骤之前先在自己的电脑上装好gradle运行环境,下载完毕后配置好gradle环境变量：
[下载gradle](https://gradle.org/releases)  

![](images/bboss/1e28d95c-4884-3057-a6ea-8e2f178abc0f.png)

设置gradle环境变量

![](images/bboss/e9a82561-3edf-3dd9-a6c1-b4676817a398.png)

![](images/bboss/7f1c4f3b-783d-37bb-b7f4-274ea693b76a.png)

如果还需要指定maven的本地库，则需要配置M2_HOME环境变量：

![](images/bboss/40647d96-5017-3a8d-a7db-b7ba7b099463.jpg)

在eclipse的gradle sts插件中设置gradle：

![](images/bboss/8c327b3a-61f4-369b-9690-7f8a6031fc3a.png)

#### 2 .eclipse中安装Gradle IDE Pack插件并导入bboss工程（bboss官方推荐）**

**2.1 在eclipse marketplace中查找gradle，选择Gradle IDE Pack插件install即可**
 ![](images/bboss/06412d8a-cad2-3f07-b78d-831b2768e865.png) 

  **2.2 通过eclipse Gradle IDE Pack插件将bboss导入eclipse**

如果gradle eclipse执行完毕后，在eclipse中，如果已经安装了gradle插件，直接通过gradle将刚才生成的工程导入elipse即可：  

![](images/bboss/6e2ba14e-030f-3851-bf79-a064c216cb90.jpg)

![](images/bboss/67fc5ac1-5837-317b-8830-05dd9af5d351.jpg)

**2.3 在eclipse运行gradle任务**

![](images/bboss/3abbd765-8f9d-3e6b-9ef0-8a690f175aa0.jpg)

每个子gradle工程发布的版本一般都存放在对应项目的build/libs目录下面：

![](images/bboss/59f5344e-bbdc-39fb-92ca-0a4941375917.png)

