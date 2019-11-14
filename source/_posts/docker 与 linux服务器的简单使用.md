# docker 与 linux服务器的简单使用

> 前言：实验室的机器学习实验需要远程操控linux服务器进行模型GPU加速训练，同时要部署在docker上面以实现标准化，在使用过程中整理了一些笔记，记录如下

## Linux服务器操作

**远程连接服务器**

```bash
$ ping hostURL 
#根据域名查询服务器的ip，如$ping hitdblabgpu.jios.org

$ ssh -p 10001 hadoop@172.17.251.22 
#远程连接服务器,-p标识带端口号10001，hadoop@为登陆的用户，后面跟的是服务器的ip
```

有些时候需要在远程服务器运行本地写好的代码，则需要**拷贝本地文件到服务器**上，这时候需要在本地打开shell或cmd，使用

```shell
$ scp -P 服务器port 本地路径 hadoop@服务器ip:目标服务器上的路径
#如 scp -P 10001 E:\NAS\BlockQNN-Keras.zip root@172.17.251.22:/home/hadoop/fgsNas
#如是目录 scp -r -P 10001 E:\NAS\fgsNAS root@172.17.251.22:/home/
```

**拷贝服务器文件到本地**

```shell
$ scp -P 服务器port hadoop@服务器ip:目标服务器上的路径 本地路径 
```

> 注意有些时候要复制到有root权限的用户上, 否则服务器会拒绝

## docker操作

**docker一些基础操作**

```shell
$ docker images #查看本机上的镜像
$ docker ps -a #查看所有包括关闭的容器(的ID)

$ docker run -it ubuntu /bin/bash # 加-d可以后台运行
#用镜像ubantu启动一个容器(实例) 运行命令是/bin/bash -i: 交互式操作 -t: 终端

$ docker start 容器ID #打开某个关闭的容器

$ docker attach 容器ID #进入到某个容器,退出(exit)时停止容器
$ docker exec -it 容器ID /bin/bash ##进入到某个容器,退出(exit)时容器后台运行

#导出和导入容器
$ docker export 1e560fca3906 > ubuntu.tar 
# 导出容器 容器ID 快照到本地文件 xxx.tar
$ cat docker/ubuntu.tar | docker import - test/ubuntu:v1 
# 将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1

#删除容器和镜像
$ docker rm 容器ID/镜像ID #有时容器(镜像对应实例)正在运行,强制删除加-f
```

**拷贝（服务器的）本地文件到docker容器中**

```shell
$ docker cp 本地路径 容器ID:容器路径
#docker cp /home/hadoop/fgsNAS/BlockQNN-Keras ad06be06076d:/home/fgsNAS/
#docker cp /home/fgsNAS d3ade3b86bb5:/home/ 拷贝整个目录不用+r
```



## 如何让服务器离线保持运行

> 如果只是单纯的使用终端来连接服务器进行训练，这样的话自己本地的电脑不能关机，一旦关机那么服务器上的训练也就停止了，所以尝试使用了screen命令来解决这个问题 

Screen中有会话的概念，用户可以在一个screen会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样，并拥有各自的编号、输入、输出和窗口缓存。一旦你在连接服务器时建立了screen窗口，即使你本地终端关闭，任务也依然在进行，而且你再次连接服务器时，可以根据screen窗口的名字，重新恢复那个窗口

**screen基本操作**

```shell
$ screen -S 窗口名字 # 新建screen窗口
$ ctrl+a d # detach，暂时离开当前screen(后台),回到还没进 screen 时的状态
$ screen -ls # 列出所有的screen窗口
$ screen -r 窗口名字 # 当退出了screen窗口后重新进入窗口
$ screen -X -S  窗口名字 # 关闭某个窗口
```

> 注意：要检查服务器断开后session是否还在，即screen -ls，记得要先切换到当时建立screen的用户(比如我的是root建立的, hadoop账户screen -ls就看不到root建立的session)