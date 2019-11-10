docker images :显示docker镜像

docker rmi -f $(docker images -qa) :删除所有docker镜像（有i 的就是删除images）

交互式容器:

docker run -it --name 自定义名字  xxx  :新建并启动容器xxx并自己取名字

 

docker ps  :显示所有docker进程  ：

-a:正在运行+历史上运行过的；

-l:显示最近创建的容器

-n:显示最近n个创建的容器（n :自己自定义）比如： ps -n  2

-q:静默模式，只显示容器编号

--no--trunc：不断截取输出

exit: 退出容器  停止退出

ctrl+P+Q  :容器不停止退出

docker start XXX  :启动xxx容器

docker restart XXX"重启XXX容器

docker stop 容器id或容器名：停止容器

docker kill XXX:强制停止容器

docker rm 容器id:删除容器   

-f :强制删除

一次性删除多个容器： docker rm -f $(docker ps -a -q)或

 docker ps -a -q |xargs docker rm

\-------------------------------------------------------------------------------------------

\-------------------------------------------------------------------------------------------

\-------------------------------------------------------------------------------------------

启动守护式容器

docker run -d :后代运行容器

 

==============================================================

***\*查看运行容器\****

docker ps

***\**查看所有容器\**\***

docker ps -a

***\*进入容器\****

其中字符串为容器ID:

docker exec -it d27bd3008ad9 /bin/bash

***\*1.停用全部运行中的容器:\****

docker stop $(docker ps -q)

 

***\*2.删除全部容器：\****

docker rm $(docker ps -aq)

***\*3.一条命令实现停用并删除容器：\****

docker stop $(docker ps -q) & docker rm $(docker ps -aq)