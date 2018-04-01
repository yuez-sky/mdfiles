## Docker 简单入门

### Docker 安装

> [官网地址](https://www.docker.com/)

- [ ubuntu 安装 ](https://yeasy.gitbooks.io/docker_practice/content/install/ubuntu.html)
- [ mac 安装 ](https://docs.docker.com/docker-for-mac/)
- [ windows 安装](https://docs.docker.com/docker-for-windows/install/)
    

### Docker 镜像加速器
- Docker 官方提供的中国 registry mirror
- 阿里云加速器
- DaoCloud 加速器

```
# 以Mac下Docker为列, 自己注册的 DaoCloud，使用后速度明显加快
# Docker -> Perferences -> Daemon -> Register Mirrors

    http://796958e9.m.daocloud.io
```

### Docker 镜像 Image
镜像是**只读的**，容器的创建基于镜像，是可读写的。

#### 拉取镜像
- 镜像仓库地址: 格式 `<域名/IP>[:端口号]`，默认`Docker Hub`
- 仓库名: 两段式命名 `<用户名>/<软件名>`，对于`Docker Hub`，默认
`library`

```
docker pull [options] [Docker Registry Address[:port]/]仓库名[:tags]
```

```
docker pull ubuntu:16.04
```
镜像多层存储，下载时非单一文件；下载给出了每一层的前12位，下载完成给出完整的`sha256`，确保下载的一致性。

#### 通过镜像运行容器
- `-i`  保持容器的输入流
- `-t`  打开一个虚拟终端
- `--rm` 退出后删除容器
- `ubuntu:16.04`  用什么镜像来启动容器
- `bash` 放在镜像的执行的命令 

```
docker run -it --rm ubuntu:16.04 bash
```

`docker run` 运行容器, 进入后，可以使用`exit`退出容器

#### 查看镜像
    
```
docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
swoft/swoft         latest              ab82de1dcd2c        14 hours ago        470MB

```
分别对应： `仓库名`， `便签`， `镜像ID`，`创建时间`，`占用空间`

```
# docker image ls [镜像名称[:tag]] 可以列出部分镜像
docker image ls swoft/swoft

# docker image ls -f [options]  -f/--filter过滤器
docker image ls -f since=ubuntu:16.04 // 在之后的镜像
docker image ls -f before=ubuntu:16.04 // 在之前的镜像


# 展示特定格式的信息
docker image ls -q # 列出虚悬镜像的ID

# format 筛选， 参数支持 Go的模版语法
docker iamge ls --format “{{.ID}}: {{.Repository}}”

```

#### 镜像体积
- 本地镜像与Docker Hub不同，Docker Hub 是**压缩**后的体积，减少网络传输
- 本地镜像总和与实际磁盘占用可能不同， Docker镜像分层存储，可能存在**相同的基础镜像**
- 可用通过`docker system df` 查看景象，容器，数据卷的占用空间
```
    docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              1                   1                   469.9MB             0B (0%)
Containers          2                   0                   1.945kB             1.945kB (100%)
Local Volumes       0                   0                   0B                  0B
Build Cache                                                 0B                  0B
```

#### 虚悬镜像
- 由于新旧镜像同名，旧镜像名称被取消，出现仓库名，标签均为`<none>`的镜像，即无标签，无仓库名
- 无价值，通过`docker image prune`删除无用镜像

#### 中间件镜像
- 为了加速构建，重复利用资源，Docker会利用**中间件镜像**，这些
镜像不应该删除，删除后会导致上层镜像无法使用
- 当删除依赖它们的镜像后，**中间件镜像**也会连带删除
- 可以通过`docker image ls -a`

#### 删除镜像
```
docker image rm [选项] <镜像1> [<镜像2> <镜像3> ...]
```

```
docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
swoft/swoft         latest              ab82de1dcd2c        15 hours ago        470MB


docker image rm swoft/swoft:lastest # 镜像名 <仓库名>:<标签>
docker image rm ab8 # 短ID，一般前3位即可
```

##### 通过镜像摘要
```
docker image ls --digests
dockge image rm swift/swift@sha256:065fccd3d6bf9cc26e4ceafa3241b5b1aaef89acc54f8b4a59986d23f00bf56e
```

##### 通过`docker image ls`配合删除
```
docker image rm $(docker image ls -q redis)

docker image rm $(docker image ls -q -f since=ubuntu:16.04)
```

##### Untagged & Deleted
删除信息分为两类： **Untagged** 和 **Deleted**。

**Untagged** 取消镜像的标签，一个镜像可以对应多个标签，当镜像的所有标签都被取消后，该镜像可能失去了存在的意义，触发删除行为**Deleted**

如果当前镜像的某一层依然**被依赖**，则也不会被删除，即**Deleted**

如果镜像还存在容器，则也不会被删除


### Docker 容器 Contanier
> 独立运行的一个或一组应用

#### 启动
启动容器有两种方式，一种根据镜像新建容器并启动，另一种存在`stoped`的容器，直接启动，无需新建

##### 新建并启动`docker run`
```
# 命令 docker run 
docker run -it ubuntu:16.04 bash

# -i 让容器的标准输入保持打开
# -t 让docker分配一个虚拟终端，并绑定到容器的标准输入上
# bash 执行的命令
```

流程

- 本地是否存在该镜像，如果不存在，从仓库中拉去
- 利用镜像创建一个容器
- 分配一个文件系统，并在只读的镜像层外挂载一层可读写层
- 从宿主主机中配置的网桥接口桥接一个虚拟接口到容器
- 从地址池中分配一个ip地址给容器
- 执行用户制定的命令
- 执行完成后，容器被终止  

##### 直接启动`docker container start`
```
# 命令 docker container start [Container ID or Names]

docker ps -a # 查看所有的容器

docker container start webserver # 启动stoped状态下的容器
```

#### 后台运行`docker run -d`
```
docker run -d ubuntu:16.04 bash
# 加上-d参数 让容器后台运行
# 查看容器的输出信息
docker container logs [Container ID or Names]
```

#### 终止
- 命令运行完自动终止
- 手动终止`docker container stop [Container ID or Names]`


#### 进入容器
某些时候，需要进入容器进行操作，包括使用`docker attach`，`docker exec`。

##### `docker attach`
```
# 后台运行
docker run -dit ubuntu
# 查看容器状态
docker container ls
# 进入容器
docker attach [Container ID or Names]
# 进入后，如果输入exit推出，容器也会随之终止

```

##### `docker exec`
```
# 后台运行
docker run -dit ubuntu
# 查看容器状态
docker container ls
# 进入容器，没有分配伪终端，没有Linux命令提示符，但是命令执行结果可以返回
docker exex -i [Container Id or Names] bash
# 有伪终端，且退出后，容器不会终止
docker exec -it [Container Id or Names] bash
```

#### 删除容器
```
# 删除某个stoped状态下的容器， -f 参数 可以删除在运行的容器
docker container rm [Container ID or Names]

# 删除所有未使用的容器
docker container prune
```

#### 导出`docker export`
导出一个文件系统作为tar归档文件
```
docker export [OPTIONS] CONTAINER

docker export -o xxx.tar [Container Id or Names]
docker export [Container Id or Names] > xxxx.tar


# 查看正在运行的容器
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                    PORTS               NAMES
a48f77b6b5c5        swoft/swoft         "docker-php-entrypoi…"   16 hours ago        Exited (0) 16 hours ago                       loving_wing

docker export a48f77b6b5c5 > swoft.tar 
```


#### 从归档中创建镜像`docker import`
```
docker import [options] file|url|- [Registory[:tag]]

docker import swoft.tar depzy/swoft:v1

# 从我们创建的容器快照中导入到本地镜像库中
docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
depzy/swoft         v1                  6e8c59c426a2        7 seconds ago       465MB
swoft/swoft         latest              ab82de1dcd2c        30 hours ago        470M

```

### Docker仓库
需要**注册**自己的`DockerId`,注册完成后**登陆**

#### 推送到远程仓库`docker push`
```
docker push 用户名/仓库名:标签
```

#### 私有仓库



### 数据管理
#### 数据卷
一个可供一个/多个容器使用的特殊目录，绕过UFS
- 可以容器之间共享使用
- 修改立即生效
- 更新不会影响镜像
- 默认一直存在，不会自动删除

##### 创建数据卷

```
docker volume create 名称

docker volume ls

# 查看详细信息
docker volume inspect 名称 
```

##### 启动挂载数据卷的容器

```
docker run -d -P --name web \
# -v my-vol:/webapp \
--mount source=my-vol, target=/webapp \
training/webapp \
python app.py
```

##### 删除数据卷

```
docker volume rm my-vol

docke volume prune
```

#### 挂载本机文件/目录作为数据卷
`-v`，`-mount`

```
docker run -d -P --name web \
# -v /src/webapp:/opt/webapp \
--mount type=bind，source=/src/webapp，tar=/opt/webapp, readonly \
traing/webapp \
python app.py
```

`-v` 如果本机没有目录，则会自动创建一个目录
`--mount` 如果本机没有目录，会报错

`readonly` 默认可读写，加了readonly后，编程只读


---

### 参考Blog
> [ gitbook of docker](https://yeasy.gitbooks.io/docker_practice/content/image/pull.html)

> [ fast start of docker](https://www.cnblogs.com/Finley/p/8297939.html)

> [ Docker 入门与实践 ](https://hujb2000.gitbooks.io/docker-flow-evolution/content/cn/basis/aufs.html) 