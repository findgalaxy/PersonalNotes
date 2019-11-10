***\*Rancher容器部署\****

 

***\*1.部署nexus\****

 

***\*1.拉取nexus镜像\****

 

不同版本nexus有少许差异，利用docker可以便捷的拉取最新版的docker

 

//将docker数据持久化到容器外部，即使删除容器数据依然可以保存下来 $ docker volume create --name nexus-data  //5000端口为拉取镜像时HTTP连接代理端口 //8081端口为nexus主页访问端口 $ docker run -d -p 4000:5000 -p 18081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3

 

***\*2.访问nexus\****

 

新版的nexus初始密码保存在容器中：

 

docker exec -it nexus bash //将一串字符串密码粘贴出来，用户名admin cat nexus-data/admin.password

 

***\*3.配置私有仓库\****

 

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps35.jpg) 

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps36.jpg) 

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps37.jpg) 

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps38.jpg) 

 

配置docker

 

vi /etc/docker/daemon.json  添加 {"insecure-registries":["192.168.29.136:4000"]}

 

***\*4.上传镜像到私库\****

 

[如何打包镜像可以参考](https://www.yuque.com/docs/share/e6118e1d-bb7f-477b-85c1-18edfb670ffe)

 

docker login 192.168.50.146:4000 //镜像自己准备，镜像打tag，私库的路径+私有仓库名+镜像名+版本号 docker push 192.168.29.136:4000/hcf/sentiment-analysis-fronted:1.0

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps39.jpg) 

***\*2.部署rancher\****

 

***\*①配置docker\****

 

yum install -y yum-utils device-mapper-persistent-datalvm2 yum-cofig-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo yum install docker-ce systemctl start docker

 

***\*②从GitHub拉取rancher部署脚本\****

 

yum -y install wget wget https://github.com/rancher/rancher/releases/download/v2.2.6/rancher-save-images.sh wget https://github.com/rancher/rancher/releases/download/v2.2.6/rancher-images.txt

 

***\*③运行脚本\****

 

chmod +x rancher-save-images.sh chmod +x rancher-images.txt ./rancher-save-images.sh --image-list ./rancher-images.txt

 

***\*④创建rancher目录映射，运行镜像\****

 

mkdir -p /home/rancher_home/rancher mkdir -p /home/rancher_home/auditlog docker run -d --restart=unless-stopped -v /home/rancher_home/rancher:/var/lib/rancher -v /home/rancher_home/auditlog:/var/log/auditlog -p 9080:80 -p 9043:443 rancher/rancher:v2.2.6

 

***\*⑤访问rancher主页\****

 

[https://192.168.xxx.xxx:9043](https://192.168.xxx.xxx:9043/)

 

//查看本机IP地址 yum install net-tools -y ifconfig

 

用户名admin

 

密码admin@123

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps40.jpg) 

***\*⑥添加集群\****

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps41.jpg) 

 

后续步骤不详细解释

 

***\*⑦配置私有库凭证\****

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps42.jpg) 

***\*1.选择镜像库凭证\****

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps43.jpg) 

***\*2.添加凭证\****

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps44.jpg) 

***\*⑧在工作负载中部署服务\****

 

注意：

 

 Docker镜像路径加上前缀

 缩放/升级策略选择先停止旧pod，在启动新pod

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps45.jpg) 

 

部署成功

 

![img](file:///C:\Users\月舞天河\AppData\Local\Temp\ksohtml12696\wps46.jpg) 

***\*清理节点：\****

通过管理面板删除的节点，会自动触发节点清除，等清除结束以后，需要重启节点

如果自动清除失败，可执行手工清除命如下： https://rancher.com/docs/rancher/v2.x/en/cluster-admin/cleaning-cluster-nodes/

\#删除所有容器 docker rm -f $(docker ps -qa) #删除所有卷 docker volume rm $(sudo docker volume ls -q) #删除临时文件系统 for mount in $(mount | grep tmpfs | grep '/var/lib/kubelet' | awk '{ print $3 }') /var/lib/kubelet /var/lib/rancher; do umount $mount; done #删除遗留文件 rm -rf /etc/ceph \    /etc/cni \    /etc/kubernetes \    /opt/cni \    /opt/rke \    /run/secrets/kubernetes.io \    /run/calico \    /run/flannel \    /var/lib/calico \    /var/lib/etcd \    /var/lib/cni \    /var/lib/kubelet \    /var/lib/rancher/rke/log \    /var/log/containers \    /var/log/pods \    /var/run/calico 

 