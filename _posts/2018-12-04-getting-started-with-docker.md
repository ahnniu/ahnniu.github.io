---
layout:     post
title:      Docker之嵌入式开发之应用
date:       2018-12-04
summary:    Docker在web开发中用的很多, 其实对于嵌入式Linux开发, 同样也可以显著提高效率.
---

# Docker

## Docker是什么

Docker 利用 Linux 核心中的资源分脱机制，例如 cgroups，以及 Linux 核心名字空间（name space），来创建独立的软件容器（containers），属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。Docker 在容器的基础上进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护，使得其比虚拟机技术更为轻便、快捷。Docker 可以在单一 Linux 实体下运作，避免因为创建一个虚拟机而造成的额外负担。

![img](/images/post/2018-12-04/2018-04-17-Docker-in-Action-1.png)

对于虚拟机技术来说，传统的虚拟机需要模拟整台机器包括硬件，每台虚拟机都需要有自己的操作系统，虚拟机一旦被开启，预分配给他的资源将全部被占用。每一个虚拟机包括应用，必要的二进制和库，以及一个完整的用户操作系统

![img](/images/post/2018-12-04/2018-04-17-Docker-in-Action-2.png)

容器技术和我们的宿主机共享硬件资源及操作系统，可以实现资源的动态分配。容器包含应用和其所有的依赖包，但是与其他容器共享内核。容器在宿主机操作系统中，在用户空间以分离的进程运行。容器内没有自己的内核，也没有进行硬件虚拟。

具体来说与虚拟机技术对比，Docker 容器的优势：

1. 启动快
2. 资源占用少
3. 体积小

## 何时会用到Docker

可能对于我, 主要就是应对不同的开发环境配置. 另外, Docker现在也支持windows了. 对于yocto这种需要耗费巨大磁盘空间的, 还是在Linux服务器上搭建, 像micro:bit这种, 可以在Windows上安装docker, 创建一个Linux容器来做了

### 环境配置

以下是我前面遇到的问题:

- Linux开发, yocto需要用到python3, 而且仅支持Ubuntu 16.04以上的系统, 我本地是Manjaro Linux或者Ubuntu 14.04, 那么意味着我要么重新装系统, 要么硬着头皮上, 解决各种包兼容的问题
- micro:bit开发, 用到的yotta和PXT, 同样依赖确定版本的python和node.js, 另外还要另一个问题, 开发环境一直在不断更新, 比如半年前准备的开发环境已经不再适用了
- 多人协作的问题
  - 多个开发人员使用的开发环境不一致, 可能导致难以意料的问题
  - 多个开发人员都需要去准备这样的一个环境, 重复造轮子

## 安装

还是老实的看官方的文档吧, 有些其他人的博客可能写的不是很全面.

- [Ubuntu安装指南](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
- [Windows](https://docs.docker.com/docker-for-windows/install/)

> **Note**: The `lsb_release -cs` sub-command below returns the name of your Ubuntu distribution, such as `xenial`. Sometimes, in a distribution like Linux Mint, you might need to change `$(lsb_release -cs)` to your parent Ubuntu distribution. For example, if you are using `Linux Mint Rafaela`, you could use `trusty`.

- [镜像加速器](https://yeasy.gitbooks.io/docker_practice/install/mirror.html)

### 无需每次使用sudo

默认情况下，`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。

建立 `docker` 组：

```bash
$ sudo groupadd docker
```

将当前用户加入 `docker` 组：

```bash
$ sudo usermod -aG docker $USER
```

退出当前终端并重新登录，进行如下测试。

### 更改Docker数据存储的位置

Docker所下载的image, 生成的containers, 甚至包括持久化数据用的vollume, 默认都会保存到 /var/lib/docker目录下, 而我目前所使用的2.58, /目录只有100G, 现在只有30G空间了, 编译yocto肯定不可行了, 需要把位置移动到/home下面.

```bash
# 停止docker服务
# sudo sysemctl stop docker
$ sudo service docker stop
# 更改配置文件
# 配置文件是默认不存在的, 默认位置如下, 当然也可以换成其他路径
# 填入以下内容, 指向新的位置
$ sudo nano /etc/docker/daemon.json
```

```jason
{
  "graph":"/mnt/cryptfs/docker"
}
```

```bash
# 启动服务即可
$ sudo service docker start
```

参考: [How do I change the default docker container location?](https://stackoverflow.com/questions/32070113/how-do-i-change-the-default-docker-container-location)

### 数据持久化操作

container只是为我们提供了一个系统环境, 这个系统环境生成的数据, 有时间需要持久的保存下来, 比如代码. 方法有很多种:

- 一种如上面说的, 更改docker数据存储的位置, 只有我们不把container删除, 那么数据会一直存在. 我们可以有很多种方法把数据导出来
- 另外就是用vollume的概念了

#### 共享目录

在docker run的时候, 使用 --vollume设置一个内外的共享目录, 有点类似虚拟机的那种共享目录, 当然, 在Host与container都可以操作的, 会自动同步.

我不太喜欢这种方式. 如果是代码放到container中, 我们也是可以直接编辑的, 犯不着放外面, 因为在服务器上, 还不是要SFTP编辑. 当然, 可能docker运作在本地可能好些. 另外的问题, 就是文件系统是否兼容. Windows NTFS与Linux EXT4不知道是否兼容.

#### 创建vollume持久化磁盘

这种方式就是使用docker vollme先创建一个数据磁盘, 然后再docker run的时候, 把这个磁盘或多个mount到某个目录上. 这个好处在于没有Windows与Linux等各种文件系统兼容的问题了. 另外, container被删除了, vollume当然还会存在. 这种方式, 对于服务器上跑服务还不错. 但是注意:

> docker vollume的默认存储还是再/var/lib/docker下

参考: [官方文档 - Use volumes](https://docs.docker.com/storage/volumes/)

## Docker 基本概念

Docker 主要包含三个基本概念，分别是镜像、容器和仓库，理解了这三个概念，就理解了 Docker 的整个生命周期。以下简要总结一下这三点，详细介绍可以移步[Docker 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/basic_concept/)对应章节。

- **镜像**：Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。
- **容器**：容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间容器可以被。创建、启动、停止、删除和暂停等等，说到镜像与容器之间的关系，可以类比面向对象程序设计中的类和实例。
- **仓库**：镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。一个 Docker Registry 中可以包含多个仓库；每个仓库可以包含多个标签；每个标签对应一个镜像，其中标签可以理解为镜像的版本号。

### 镜像

 Docker Hub上面除了官方提供了一些元系统外, 还有很多优秀的镜像, 这样我们其实不必要每次都从一个干净的Linux系统重新开始. 比如yocto这块, 有人提供的yocto镜像, 基本吧yocto所依赖的环境和软件都安装配置好了.

#### yocto镜像

其实, 网上准备的感觉都不怎么样

- [我用來編譯 Yocto 的 Docker 環境](https://coldnew.github.io/135d6bd2/), 对应的镜像:
  - [Github:coldnew/docker-yocto](https://github.com/coldnew/docker-yocto)
  - [DockerHub:coldnew/yocto-build](https://hub.docker.com/r/coldnew/yocto-build/)
- [Building with Yocto on macOS](https://zatoichi-engineer.github.io/2017/10/02/yocto-on-osx.html), 使用的镜像包括yocto, android和opnewrt的:
  - [Github:gmacario/easy-build](https://github.com/gmacario/easy-build)
  - [DockerHub:gmacario/build-yocto](https://hub.docker.com/r/gmacario/build-yocto/)

> 我写了一个NXP i.MX8的yocto环境, 也仅仅是安装了必要的组件. 详细请移步到我的github项目:
> [ahnniu/docker-yocto-imx](https://github.com/ahnniu/docker-yocto-imx)

## Docker Getting Start

### docker pull

首先先下载一个image

```bash
$ docker pull gmacario/build-yocto
```

### docker run

docker run, 相当于从指定的image实例化出一个容器出来

```bash
$ docker run gmacario/build-yocto echo hi
#hi
```

> **注意:** docker run每次执行都会实例化一个新的容器出来, 而且每次新实例化的都是一个全新的image, 因此, docker run只需要执行一次即可, 后续我们使用使用`docker container start`来启动容器使用

docker run 这里, 可以指定很多参数, 比如映射与Host的共享目录, 映射端口, 是否需要交互, 指定别名等, 详细的, 可以看这个命令的帮助

#### docker run的流程是执行命令, 完毕后stop

docker run的流程应该是实例化一个container, 然后执行指定的命令, 然后stop container. 那么

> 如果我们没有指定命令, 或者指定一个错误的命令, 都是立即stop, 只不过是exit(0)还是什么, 即使我们重新start这个container 同样也会马上exit后stop, 这样对于需要长期使用的, 就不是那么方便了
>
> 另外, 如果指定bash , 一定要带上 -it的参数, 可以跟上-d 表示后台, 这样bash这个命令才不会马上exit

```bash
$ docker run -p 2222:22 -d -it --name imx8-yocto ubuntu:16.04 bash
```

> 参考 [Docker container stop automatically after running](https://webkul.com/blog/docker-container-will-automatically-stop-run/)

### docker container

在概念这块, 已经提到, container已经是一个实例化后的容器, 相当于一个独立的虚拟机(初始环境同image). 对container的操作, 类似于对虚拟机的操作: 开机, 关机, 查看机器等

**start**

```bash
$ docker container start container_id
```



### docker exec

这里, 就是真正的使用了, 执行虚拟机上的命令/程序, 或交互形式, 或守护进程等

**交互方式打开bash**

```bash
$ docker exec -it container_id bash
```



### docker 端口映射

关于端口映射这块, 我尝试了接近1天的时间, 正确的方法只有在docker run的时候传递要映射的端口(无论container是否已经具备这个端口的功能, 比如ssh还没有安装)

> 在image已经实例化为container的时候, 就不要再考虑添加端口映射了, 方法只能重新实例(run)一个新的实例, 如果当前container中已经有一些工作, 可以docker commit基于现在的情况生成一个新的image, 然后在新的image上实例container

```bash
$ docker run -p 2222:22 -p 4000:80 --name myserver -d image
# 可以在宿主机host中查看端口的情况
$ sudo iptables -L -n
```

> 可以先绑定端口映射, 再实例化好的container中安装软件, 配置服务一样有效, 另外, 一切在container已经实例化好后, 企图使用iptables去添加端口映射都是不理智的, 1) iptables在host重启后需重新配置 2) 如果container restart后, ip地址会变化的

#### 使得container支持ssh远程登录

**Host操作**

```bash
# 实例化一个container
$ docker run -p 2222:22 -it image bash
# 然后会进入到container的bash操作
```

**Container操作**

```bash
$ sudo apt-get update
$ sudo apt-get install openssh-server
# sudo service ssh status / start /stop / restart
$ sudo service ssh start
# 开机自动启动
$ sudo systemctl enable ssh
# 如有需要, 可以编辑ssh的配置文件, 比如支持root用户, 注意, 需要设置用户的密码用于ssh登录
# $ sudo passwd user
$ sudo nano /etc/ssh/sshd_config
$ sudo service ssh restart
```

然后就可以在远程ssh登录了

```bash
$ ssh build@192.168.2.58 -p 2222
```

如果Host支持了SSH服务, 那么可以在远程,sublime text安装SFTP插件, 远程编辑服务器上的代码了

## 常用Docker命令

### docker image

```bash
$ docker image

Usage:  docker image COMMAND

Manage images

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.

```

### docker run

**举例**

```bash
$ docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash
```

- `-p`参数：容器的 3000 端口映射到本机的 8000 端口。

- `-it`参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。

- `koa-demo:0.0.1`：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。

- `/bin/bash`：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。

**以下是常用的一些命令**

```bash
docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
  -d, --detach                         Run container in background and print container ID
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --help                           Print usage
  -h, --hostname string                Container host name
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
  -t, --tty                            Allocate a pseudo-TTY
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container

```

### docker container

#### docker container stop

关机, 正常关机

#### docker container kill

有点类似强制关机, 可能container正在运行程序, 可能会有些文件的丢失.

#### docker container rename

重命名, 因为id不好记



```bash
$ docker container --help

Usage:  docker container COMMAND

Manage containers

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  inspect     Display detailed information on one or more containers
  kill        Kill one or more running containers
  logs        Fetch the logs of a container
  ls          List containers
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  prune       Remove all stopped containers
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  run         Run a command in a new container
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker container COMMAND --help' for more information on a command.

```



## Dockerfile

我们可以使用Dockerfile, 基于一个源镜像, 定制自己所需要的镜像, 相比直接在原container上commit的好处在于:

- 透明, 做了什么事情一目了然, docker commit就没这么清楚了
- 文本文件, 可使用Git管理, 后续升级维护方便
- 可以用到一些最佳实践, 比如最大限度的减小生成image的大小, 当然, 要看Dockerfile的写法
- 可以无缝集成DockerHub, 分享自己的image

参考: [官方文档 - Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

先看几个概念:

### Images and layers

参考:

- [官方文档 - Images and layers](https://docs.docker.com/storage/storagedriver/#images-and-layers)
- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage)

Images都是一层一层的, 层感觉有点类似Git Patch, 主要是一些变化(新增, 删除, 修改), 层多了, 自然这个image就会变得很大. 层是怎么产生的呢:

Each instruction creates one layer:

- `FROM` creates a layer from the `ubuntu:15.04` Docker image.
- `COPY` adds files from your Docker client’s current directory.
- `RUN` builds your application with `make`.
- `CMD` specifies what command to run within the container.

上面这几个命令, 都会创建一个新的layer. FROM是直接用的一个现成的, 这里尤其要注意执行RUN命令, 尽量减少RUN出现的次数.

![Layers of a container based on the Ubuntu image](/images/post/2018-12-04/container-layers.jpg)

#### RUN

几点注意:

#### 不要更新系统

避免使用 `RUN apt-get upgrade` and `dist-upgrade`

#### 如何使用apt-get

Always combine `RUN apt-get update` with `apt-get install` in the same `RUN` statement. 也就是说每次使用apt-get install 都要在一条RUN中首先apt-get update. 原因就是RUN命令每执行一次就会生成一个layer. 造成的后果, apt-get install使用的源可能是很旧的.

另外一点就是, 每一行写一个package

```bash
RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    build-essential \
    curl \
    dpkg-sig \
    libcap-dev \
    libsqlite3-dev \
    mercurial \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.* \
 && rm -rf /var/lib/apt/lists/*
```

when you clean up the apt cache by removing `/var/lib/apt/lists` it reduces the image size, since the apt cache is not stored in a layer.

#### 使用&&把多条命令放到一个RUN中

目的是减少生成的layer, 使得尺寸小些

```bash
RUN curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > /usr/bin/repo \
 && chmod a+x /usr/bin/repo
```





## Reference

- [Docker 学习新手笔记：从入门到放弃](https://hijiangtao.github.io/2018/04/17/Docker-in-Action/)
- [最全 Docker 介绍与教程，一文全掌握](https://mp.weixin.qq.com/s/TAg_YdZsITSqKWbhJr9Leg)
- [Docker 简介,gitbook](https://yeasy.gitbooks.io/docker_practice/introduction/)
- [官方文档](https://docs.docker.com/get-started)
- [我用來編譯 Yocto 的 Docker 環境](https://coldnew.github.io/135d6bd2/)
- [Building with Yocto on macOS](https://zatoichi-engineer.github.io/2017/10/02/yocto-on-osx.html)