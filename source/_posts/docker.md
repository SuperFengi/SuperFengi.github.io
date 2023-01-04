---
title:  Docker学习
categories: wiki
toc: true
date: 2022/12/30
tags: docker
---
跟着狂神哥学docker

# 概述

## docker的由来

Docker的思想来自于集装箱，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。

## docker与传统虚拟机的区别

传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；

而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。

## 相关网站

Docker官网：http://www.docker.com

Docker中文网站：https://www.docker-cn.com

Docker Hub官网：https://hub.docker.com 

# docker的基本组成

## 构架图

![image-20221228154551534](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221228154551534.png)

## 镜像

Docker 镜像（Image）就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。 就好似 Java 中的 类和对象，类就是镜像，容器就是对象。

## 容器

Docker 利用容器（Container）独立运行的一个或一组应用。容器是用镜像创建的运行实例。

它可以被启动、开始、停止、删除。每个容器都是相互隔离的，保证安全的平台。

可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

## 仓库

仓库（Repository）是集中存放镜像文件的场所。

仓库(Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

## 底层原理

Docker是一个**Client-Server**结构的系统，Docker守护进程运行在主机上， 然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。 容器，是一个运行时环境，就是我们前面说到的集装箱。

### docker更快的原因

1、docker有着比虚拟机更少的抽象层。由亍docker不需要Hypervisor实现硬件资源虚拟化,运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。

2、docker利用的是宿主机的内核,而不需要Guest OS。因此,当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载Guest OS,返个新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返个过程,因此新建一个docker容器只需要几秒钟。

# docker命令

## 镜像命令

### docker images

```she
# 列出本地主机上的镜像
[root@d3f4u1t ~]# docker images
REPOSITORY    TAG      IMAGE ID       CREATED       SIZE
hello-world  latest  bf756fb1ae65   4 months ago   23.3kB

# 解释
REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的ID
CREATED 镜像创建时间
SIZE 镜像大小

# 同一个仓库源可以有多个 TAG，代表这个仓库源的不同版本，我们使用REPOSITORY：TAG 定义不同
的镜像，如果你不定义镜像的标签版本，docker将默认使用 lastest 镜像！

# 可选项
-a： 列出本地所有镜像
-q： 只显示镜像id
--digests： 显示镜像的摘要信息
```

### docker search

```she
# 搜索镜像
[root@d3f4u1t ~]# docker search mysql
NAME DESCRIPTION STARS
OFFICIAL
mysql MySQL is a widely used, open-source relation… 9484
[OK]

# docker search 某个镜像的名称 对应DockerHub仓库中的镜像

# 可选项
--filter=stars=50 ： 列出收藏数不小于指定值的镜像。
```

### docker pull

```she
# 下载镜像
[root@d3f4u1t ~]# docker pull mysql
Using default tag: latest # 不写tag，默认是latest最新版本
latest: Pulling from library/mysql
54fec2fa59d0: Already exists # 分层下载
bcc6c6145912: Already exists
951c3d959c9d: Already exists
05de4d0e206e: Already exists
319f0394ef42: Already exists
d9185034607b: Already exists
013a9c64dadc: Already exists
42f3f7d10903: Pull complete
c4a3851d9207: Pull complete
Digest:
sha256:61a2a33f4b8b4bc93b7b6b9e65e64044aaec594809f818aeffbff69a893d1944 #
签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实位置
# 指定版本下载
[root@d3f4u1t ~]# docker pull mysql:5.7
....
```

### docker rmi

```she
# 删除镜像
docker rmi -f 镜像id # 删除单个
docker rmi -f 镜像名:tag 镜像名:tag # 删除多个
docker rmi -f $(docker images -qa) # 删除全部
```

## 容器命令

有镜像才能创建容器，这里使用 centos 的镜像来测试，就是虚拟一个 centos 

### **新建容器并启动**

```she
# 命令
docker run [OPTIONS] IMAGE [COMMAND][ARG...]

# 常用参数说明
--name="Name" # 给容器指定一个名字
-d # 后台方式运行容器，并返回容器的id！
-i # 以交互模式运行容器，通过和 -t 一起使用
-t # 给容器重新分配一个终端，通常和 -i 一起使用
-P # 随机端口映射（大写）
-p # 指定端口映射（小结），一般可以有四种写法
ip:hostPort:containerPort
ip::containerPort
hostPort:containerPort (常用)
containerPort

# 测试
[root@d3f4u1t ~]# docker images
REPOSITORY  TAG      IMAGE ID      CREATED       SIZE
centos     latest  470671670cac  3 months ago    237MB

# 使用centos进行用交互模式启动容器，在容器内执行/bin/bash命令！
[root@d3f4u1t ~]# docker run -it centos /bin/bash
[root@dc8f24dd06d0 /]# ls # 注意地址，已经切换到容器内部了！
bin etc lib lost+found mnt proc run srv tmp var
dev home lib64 media opt root sbin sys usr
```

### 列出所有运行的容器

```she
# 命令
docker ps [OPTIONS]

# 常用参数说明
-a # 列出当前所有正在运行的容器 + 历史运行过的容器
-l # 显示最近创建的容器
-n=? # 显示最近n个创建的容器
-q # 静默模式，只显示容器编号。
```

### 退出容器

```she
exit # 容器停止退出
ctrl+P+Q # 容器不停止退出
```

### 启动时停止容器

```she
docker start (容器id or 容器名) # 启动容器
docker restart (容器id or 容器名) # 重启容器
docker stop (容器id or 容器名) # 停止容器
docker kill (容器id or 容器名) # 强制停止容器
```

### 删除容器

```she
docker rm 容器id # 删除指定容器
docker rm -f $(docker ps -a -q) # 删除所有容器
docker ps -a -q|xargs docker rm # 删除所有容器
```

## 其他命令

### 后台启动容器

```she
# 命令
docker run -d 容器名

# 例子
docker run -d centos # 启动centos，使用后台方式启动

# 问题： 使用docker ps 查看，发现容器已经退出了！
# 解释：Docker容器后台运行，就必须有一个前台进程，容器运行的命令如果不是那些一直挂起的命
令，就会自动退出。
# 比如，你运行了nginx服务，但是docker前台没有运行应用，这种情况下，容器启动后，会立即自
杀，因为他觉得没有程序了，所以最好的情况是，将你的应用使用前台进程的方式运行启动。
```

### 查看日志

```she
# 命令
docker logs -f -t --tail 容器id
# eg:
[root@d3f4u1t ~]# docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"

[root@d3f4u1t ~]# docker ps
CONTAINER ID IMAGE
c8530dbbe3b4 centos

# -t 显示时间戳
# -f 打印最新的日志
# --tail 数字 显示多少条！

[root@d3f4u1t ~]# docker logs -tf --tail 10 c8530dbbe3b4
2020-05-11T08:46:40.656901941Z kuangshen
2020-05-11T08:46:41.658765018Z kuangshen
2020-05-11T08:46:42.661015375Z kuangshen
2020-05-11T08:46:43.662865628Z kuangshen
2020-05-11T08:46:44.664571547Z kuangshen
2020-05-11T08:46:45.666718583Z kuangshen
2020-05-11T08:46:46.668556725Z kuangshen
2020-05-11T08:46:47.670424699Z kuangshen
2020-05-11T08:46:48.672324512Z kuangshen
2020-05-11T08:46:49.674092766Z kuangshen
```

### 查看容器中运行的进程信息，支持ps命令参数。

```she
# 命令
docker top 容器id

# 测试
[root@d ~]# docker top c8530dbbe3b4
UID    PID   PPID   C   STIME   TTY   TIME        CMD
root  27437  27421  0   16:43    ?   00:00:00   /bin/sh -c ....
```

### 查看容器镜像的元数据

```she
# 命令
docker inspect 容器id

# 测试
[root@d3f4u1t ~]# docker inspect c8530dbbe3b4
[
{
# 完整的id，这里上面的容器id，就是截取的这个id前几位
"Id":
"c8530dbbe3b44a0c873f2566442df6543ed653c1319753e34b400efa05f77cf8",
"Created": "2020-05-11T08:43:45.096892382Z",
"Path": "/bin/sh",
"Args": [
"-c",
"while true;do echo kuangshen;sleep 1;done"
],
# 状态
"State": {
"Status": "running",
"Running": true,
"Paused": false,
"Restarting": false,
"OOMKilled": false,
"Dead": false,
"Pid": 27437,
"ExitCode": 0,
"Error": "",
"StartedAt": "2020-05-11T08:43:45.324474622Z",
"FinishedAt": "0001-01-01T00:00:00Z"
},
// ...........
]
```

### 进入正在运行的容器

```she
# 命令1
docker exec -it 容器id bashShell

# 测试1
[root@d3f4u1t ~]# docker ps
CONTAINER ID IMAGE COMMAND CREATED
STATUS PORTS NAMES
c8530dbbe3b4 centos "/bin/sh -c 'while t…" 12 minutes
ago Up 12 minutes happy_chaum
[root@d3f4u1t ~]# docker exec -it c8530dbbe3b4 /bin/bash
[root@c8530dbbe3b4 /]# ps -ef
UID PID PPID C STIME TTY TIME CMD
root 1 0 0 08:43 ? 00:00:00 /bin/sh -c while true;do
echo kuangshen;sleep
root 751 0 0 08:56 pts/0 00:00:00 /bin/bash
root 769 1 0 08:56 ? 00:00:00 /usr/bin/coreutils --
coreutils-prog-shebang=s
root 770 751 0 08:56 pts/0 00:00:00 ps -ef

# 命令2
docker attach 容器id

# 测试2
[root@d3f4u1t ~]# docker exec -it c8530dbbe3b4 /bin/bash
[root@c8530dbbe3b4 /]# ps -ef
UID PID PPID C STIME TTY TIME CMD
root 1 0 0 08:43 ? 00:00:00 /bin/sh -c while true;do
echo kuangshen;sleep
root 856 0 0 08:57 pts/0 00:00:00 /bin/bash
root 874 1 0 08:57 ? 00:00:00 /usr/bin/coreutils --
coreutils-prog-shebang=s
root 875 856 0 08:57 pts/0 00:00:00 ps -ef

# 区别
# exec 是在容器中打开新的终端，并且可以启动新的进程
# attach 直接进入容器启动命令的终端，不会启动新的进程
```

### 从容器中拷贝内容到主机上

```she
# 命令
docker cp 容器id:容器内路径 目的主机路径

# 测试
# 容器内执行，创建一个文件测试
[root@c8530dbbe3b4 /]# cd /home
[root@c8530dbbe3b4 home]# touch f1
[root@c8530dbbe3b4 home]# ls
f1
[root@c8530dbbe3b4 home]# exit
exit

# linux复制查看，是否复制成功
[root@d3f4u1t ~]# docker cp c8530dbbe3b4:/home/f1 /home
[root@d3f4u1t ~]# cd /home
[root@d3f4u1t home]# ls
f1
```

## 总结

![image-20221228211456019](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221228211456019.png)

常用命令：

```she
attach: Attach to a running container # 当前 shell 下attach 连接指定运行镜像

build: Build an image from a Dockerfile # 通过 Dockerfile 定制镜像

commit: Create a new image from a container changes # 提交当前容器为新的镜像

cp: Copy files/folders from the containers filesystem to the host path
#从容器中拷贝指定文件或者目录到宿主机中

create: Create a new container # 创建一个新的容器，同run，但不启动容器

diff: Inspect changes on a container's filesystem # 查看 docker 容器变化

events: Get real time events from the server # 从 docker 服务获取容器实时事件

exec: Run a command in an existing container # 在已存在的容器上运行命令

export: Stream the contents of a container as a tar archive # 导出容器的内容流作为一个 tar 归档文件[对应 import ]

history: Show the history of an image # 展示一个镜像形成历史

images: List images # 列出系统当前镜像

import: Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export]

info: Display system-wide information # 显示系统相关信息

inspect: Return low-level information on a container # 查看容器详细信息

kill: Kill a running container # kill 指定 docker 容器

load: Load an image from a tar archive # 从一个 tar 包中加载一个镜像[对应 save]

login: Register or Login to the docker registry server # 注册或者登陆一个docker 源服务器

logout: Log out from a Docker registry server # 从当前 Dockerregistry 退出

logs: Fetch the logs of a container # 输出当前容器日志信息

port: Lookup the public-facing port which is NAT-ed to PRIVATE_PORT #查看映射端口对应的容器内部源端口

pause: Pause all processes within a container # 暂停容器

ps: List containers # 列出容器列表

pull: Pull an image or a repository from the docker registry server #从docker镜像源服务器拉取指定镜像或者库镜像

push: Push an image or a repository to the docker registry server #推送指定镜像或者库镜像至docker源服务器

restart: Restart a running container # 重启运行的容器

rm: Remove one or more containers # 移除一个或者多个容器

rmi: Remove one or more images # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]

run: Run a command in a new container # 创建一个新的容器并运行一个命令

save: Save an image to a tar archive # 保存一个镜像为一个tar 包[对应 load]

search: Search for an image on the Docker Hub # 在 docker hub 中搜索镜像

start: Start a stopped containers # 启动容器

stop: Stop a running containers # 停止容器

tag: Tag an image into a repository # 给源中镜像打标签

top: Lookup the running processes of a container # 查看容器中运行的进程信息

unpause: Unpause a paused container # 取消暂停容器

version: Show the docker version information # 查看 docker 版本号

wait: Block until a container stops, then print its exit code # 截取容器停止时的退出状态值
```

# docker镜像详解

## 何为镜像

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

## 加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs(boot fifile system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs (root fifile system) ，在bootfs之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

## 分层结构

docker镜像采用分层结构，最大的好处莫过于资源共享。比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

理解：

所有的 Docker 镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于 Ubuntu Linux 16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加 Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

![image-20221230225349259](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221230225349259.png)

## 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部

这一层就是我们通常说的容器层，容器之下的都叫镜像层

# 容器数据卷

## 何为容器数据卷

卷就是目录或者文件，存在一个或者多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过 Union File System ， 提供一些用于持续存储或共享数据的特性：

卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂载的数据卷。

特点：

1、数据卷可在容器之间共享或重用数据

2、卷中的更改可以直接生效

3、数据卷中的更改不会包含在镜像的更新中

4、数据卷的生命周期一直持续到没有容器使用它为止

## 使用数据卷

### 方式一：命令行

```she
# 命令
docker run -it -v 宿主机绝对路径目录:容器内目录 镜像名

# 测试
[root@d3f4u1t ~]# docker run -it -v /home/ceshi:/home centos /bin/bash
```

挂载分为匿名挂载和具名挂载

```she
# 匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx
# 匿名挂载的缺点，就是不好维护，通常使用命令 docker volume维护
docker volume ls

# 具名挂载
-v 卷名:/容器内路径
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx nginx

# 查看挂载的路径
[root@d3f4u1t ~]# docker volume inspect nginxconfig
[
{
"CreatedAt": "2020-05-13T17:23:00+08:00",
"Driver": "local",
"Labels": null,
"Mountpoint": "/var/lib/docker/volumes/nginxconfig/_data",
"Name": "nginxconfig",
"Options": null,
"Scope": "local"
}
]

# 怎么判断挂载的是卷名而不是本机目录名？
不是/开始就是卷名，是/开始就是目录名
# 改变文件的读写权限
# ro: readonly
# rw: readwrite

# 指定容器对我们挂载出来的内容的读写权限
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:rw nginx
```



### 方式二：dockerfile

```she
# 1、我们在宿主机 /home 目录下新建一个 docker-test-volume文件夹
[root@d3f4u1t home]# mkdir docker-test-volume

# 说明：在编写DockerFile文件中使用 VOLUME 指令来给镜像添加一个或多个数据卷
VOLUME["/dataVolumeContainer1","/dataVolumeContainer2","/dataVolumeContainer
3"]

# 出于可移植和分享的考虑，我们之前使用的 -v 主机目录:容器目录 这种方式不能够直接在
DockerFile中实现。
# 由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有宿主机上都存在这样的特定目录.

# 2、编写DockerFile文件
[root@d3f4u1t docker-test-volume]# pwd
/home/docker-test-volume
[root@d3f4u1t docker-test-volume]# vim dockerfile1
[root@d3f4u1t docker-test-volume]# cat dockerfile1
# volume test
FROM centos
VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
CMD echo "-------end------"
CMD /bin/bash

# 3、build后生成镜像，获得一个新镜像 d3f4u1t/centos
docker build -f /home/docker-test-volume/dockerfile1 -t kuangshen/centos . 
#注意最后有个.

# 4、启动容器
[root@d3f4u1tn docker-test-volume]# docker run -it 0e97e1891a3d /bin/bash 
#启动容器
[root@f5824970eefc /]# ls -l
total 56
lrwxrwxrwx 1 root root 7 May 11 2019 bin -> usr/bin
drwxr-xr-x 2 root root 4096 May 11 11:55 dataVolumeContainer1 # 数据卷目录
drwxr-xr-x 2 root root 4096 May 11 11:55 dataVolumeContainer2 # 数据卷目录
drwxr-xr-x 5 root root 360 May 11 11:55 dev
drwxr-xr-x 1 root root 4096 May 11 11:55 etc
drwxr-xr-x 2 root root 4096 May 11 2019 home
.....
# 问题:通过上述步骤，容器内的卷目录地址就已经知道了，但是对应的主机目录地址在哪里呢？

# 5、我们在数据卷中新建一个文件
[root@f5824970eefc dataVolumeContainer1]# pwd
/dataVolumeContainer1
[root@f5824970eefc dataVolumeContainer1]# touch container.txt
[root@f5824970eefc dataVolumeContainer1]# ls -l
total 0
-rw-r--r-- 1 root root 0 May 11 11:58 container.txt

# 6、查看下这个容器的信息
[root@d3f4u1t ~]# docker inspect 0e97e1891a3d
# 查看输出的Volumes
"Volumes": {
"/dataVolumeContainer1": {},
"/dataVolumeContainer2": {}
},

# 7、这个卷在主机对应的默认位置
```

## 数据卷容器

命名的容器挂载数据卷，其他容器通过挂载这个（父容器）实现数据共享，挂载数据卷的容器，称之为数据卷容器。

**容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止。**

**存储在本机的文件则会一直保留**

# dockerfile

## 何为dockerfile

dockerfifile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。

构建步骤：

1、编写DockerFile文件

2、docker build 构建镜像

3、docker run

## 构建dockerfile

**基础知识：**

1、每条保留字指令(命令)都必须为大写字母且后面要跟随至少一个参数

2、指令按照从上到下，顺序执行

3、# 表示注释

4、每条指令都会创建一个新的镜像层，并对镜像进行提交

**流程：**

1、docker从基础镜像运行一个容器

2、执行一条指令并对容器做出修改

3、执行类似 docker commit 的操作提交一个新的镜像层

4、Docker再基于刚提交的镜像运行一个新容器

5、执行dockerfifile中的下一条指令直到所有指令都执行完成！

## dockerfile指令

### 关键字

```she
FROM # 基础镜像，当前新镜像是基于哪个镜像的
MAINTAINER # 镜像维护者的姓名混合邮箱地址
RUN # 容器构建时需要运行的命令
EXPOSE # 当前容器对外保留出的端口
WORKDIR # 指定在创建容器后，终端默认登录的进来工作目录，一个落脚点
ENV # 用来在构建镜像过程中设置环境变量
ADD # 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
COPY # 类似ADD，拷贝文件和目录到镜像中！
VOLUME # 容器数据卷，用于数据保存和持久化工作
CMD # 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最
后一个生效！
ENTRYPOINT # 指定一个容器启动时要运行的命令！和CMD一样
ONBUILD # 当构建一个被继承的DockerFile时运行命令，父镜像在被子镜像继承后，父镜像的ONBUILD被触发
```

![image-20221230231454355](https://picture-1312836458.cos.ap-beijing.myqcloud.com/img/image-20221230231454355.png)

### CMD 和 ENTRYPOINT 的区别

我们之前说过，两个命令都是指定一个容器启动时要运行的命令

**CMD**：Dockerfile 中可以有多个CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换

**ENTRYPOINT**： docker run 之后的参数会被当做参数传递给 ENTRYPOINT，之后形成新的命令组合
