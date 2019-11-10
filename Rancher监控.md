[启用监控要求](https://polars.info/doc/#/rancher启用监控?id=启用监控要求)

 Rancher 版本：2.2.4以上，最好是最新版本

 已同步所有rancher镜像到本地docker仓库

[公网部署](https://polars.info/doc/#/rancher启用监控?id=公网部署)

公网部署使用rancher默认配置，无需额外配置chart仓库,直接跳到：启用集群监控。

[内网部署system-chart仓库](https://polars.info/doc/#/rancher启用监控?id=内网部署system-chart仓库)

[1. 准备system-charts本地git仓库](https://polars.info/doc/#/rancher启用监控?id=_1-准备system-charts本地git仓库)

准备好可以通过http访问的git仓库，如果已有git托管服务，可以建好仓库以后，跳到第2步。

如果没有git托管服务，则需要先安装gitea服务，下面是安装gitea的步骤。

 安装docker镜像26.8.0.64:18444/gitea/gitea:latest

配置如下：

端口映射：22->9322

  3000->9380   

网络模式，指定hostport，确保pod所在主机的9322及9380端口未被占用 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps3.jpg) 

 

主机调度：指定主机，测试环境指定到180： stsz140615 10.25.233.180 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps4.jpg) 

 成功启动容器以后，使用地址：http://10.25.233.180:9380/ 访问gitea主页，点击注册

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps5.jpg) 

 

打开初始配置，修改 如下配置

Gitea 基本 URL：[http://10.25.233.180:9380](http://10.25.233.180:9380/) 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps6.jpg) 

 

点击立即安装，再点击注册，输入用户名密码完成注册，用户名可随意，这里示例 用户名：paic 邮箱：[paic@paic.com](mailto:paic@paic.com) 密码：123456 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps7.jpg) 

点击注册账号，即可登录，登录成功以后，点击创建仓库 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps8.jpg) 

仓库名称：system-charts，点击创建仓库 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps9.jpg) 

成功创建仓库地址：http://10.25.233.180:9380/paic/system-charts.git 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps10.jpg) 

[2. 同步rancher/system-charts库到本地git仓库](https://polars.info/doc/#/rancher启用监控?id=_2-同步ranchersystem-charts库到本地git仓库)

提供的system-charts.zip即为从rancher官方仓库clone的system-charts库

解压system-charts.zip到本地目录，命令行进入system-charts目录，并执行如下命令

git config user.name paic git config user.email paic@paic.com git remote set-url origin http://paic:123456@10.25.233.180:9380/paic/system-charts.git git push -u origin --all

请确保对应rancher版本的release分支上传成功，本文使用release-v2.2分支。

[3. rancher设置应用商店system-charts](https://polars.info/doc/#/rancher启用监控?id=_3-rancher设置应用商店system-charts)

选择->全局->工具->商店设置

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps11.jpg) 

 

选择system-library ，点击省略号，选择升级

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps12.jpg) 

 

修改url为本地的system-charts仓库地址。

http://10.25.233.180:9380/paic/system-charts.git

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps13.jpg) 

[启用集群监控](https://polars.info/doc/#/rancher启用监控?id=启用集群监控)

选择集群->工具->监控

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps14.jpg) 

 

2.2.7支持的版本为0.0.3

数据保存时间默认为12小时，可以按照监控需求和服务器资源情况调整

点击保存即可

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps15.jpg) 

 

监控部署以后，会在集群的system项目下创建命名空间cattle-prometheus，并部署相关监控服务，服务部署成功以后，监控即启用成功

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps16.jpg) 

 

可在集群界面查看监控

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps17.jpg) 

 

点击grafana图标，可打开grafana界面

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml6972\wps18.jpg) 

 