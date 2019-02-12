[TOC]

## Kubernetes：部署环境、持续集成

- Container
- Cluster、Node、Pod
- Service、Label、Selector
- Volume
- Depliyment、Rolling Update
  - ReplicaSet
  - Canary
  - Blue-Green
- Continuous Delivery Pipeline
  - Jenkins

## DaoCloud加速器

1. 官网地址：https://www.daocloud.io/mirror#accelerator-doc
2. 账号：sweetyu2603   密码：sy20122603

## 版本 version

```
docker --info
docker --version
docker-compose --version
docker-machine --version
```

## 仓库 repository

```
仓库：docker [login|pull|push|search]
私人仓库：registry.domain.com:5000/repository:<tag_name>
公共仓库：username/repository<:tag_name>
```

## 容器 container

```
docker [run|start|stop|restart|kill|rm|pause|unpause]
docker [ps|inspect|top|attach|events|logs|wait|export|port]
docker [commit|cp|diff]
```

## 镜像 image

```
docker [images|rmi|tag|build|history|save|import]
```

## 常用库

```
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d (镜像id)
docker run  --name redis -p 6379:6379 -v F:/Data:/data  -d (镜像id) redis-server --appendonly yes
docker exec -it 7a6dbd754a68 redis-cli -h 172.16.0.6 -p 6379 -a crs-nv1bxvs7:sy123456
docker run --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=root -d (镜像id)
docker run --name jena -p 3030:3030 -e ADMIN_PASSWORD=root -d (镜像id)
docker run --name neo4j -p 7473-7474:7473-7474 -p 7687:7687  --privileged=true -v 本地地址:/var/lib/neo4j/import -d (镜像id)
docker run -p 27017:27017 -v $PWD/db:/data/db -d mongo (镜像id)
```

## 重启网卡

```
systemctl restart network
nmcli con show
nmcli con up xxx
vi /etc/sysctl.conf  或 vi /usr/lib/sysctl.d/00-system.conf
    添加如下代码：net.ipv4.ip_forward=1 
sysctl net.ipv4.ip_forward
```

## 关闭防火墙

```
systemctl stop firewalld
systemctl disable firewalld
reboot -h
```

## docker主机

```
docker-machine restart default 
docker-machine env default 
docker-machine ls
docker-machine stop default
docker-machine kill default
docker-machine rm default
docker-machine create --virtualbox-disk-size "50000" --virtualbox-memory "4096" -d virtualbox default
```

## Toolbox

```
使用Toolbox上的docker镜像作为后台数据库，在前端进行连接时，需要填写固定本地IP（192.168.99.99），而不是locahost
```

## Volume

- volumes do not provide shared between all containers, or a lifecycle management either.
- Kubernetes, volumes allow containers in part to share data, and to be stateful.
    ```
    docker volume ls
    docker volume rm $(docker volume ls -qf dangling=true)
    ```

## DockerFile

指令 | 说明
---|---
FROM | 指定所创建镜像的基础镜像
MAINTAINER | 指定维护者信息
RUN | 运行命令
CMD | 指定启动容器时默认执行的命令
LABEL | 指定生成镜像的元数据标签信息
EXPOSE | 声明镜像内服务所监听的端口
ENV | 指定环境变量
ADD | 复制指定的<src>路径下的内容到容器中的<dest>路径下，<src>可以为URL；如果为tar文件，会自动解压到<dest>路径下
COPY | 复制本地主机的<src>路径下的内容到容器中的<dest>路径下，推荐使用
ENTRYPOINT | 指定镜像的默认入口【仅一个】，docker run --entrypoint
VOLUME | 创建数据挂载点，VOLUME ["/data"]
USER | 指定运行容器时的用户名或UID
WORKDIR | 配置工作目录
ARG | 指定镜像内使用的参数，可用docker build --build-arg<name>=<value>来指定参数值
ONBUILD | 配置当前所创建的镜像作为其他镜像的基础镜像时，所执行的创建操作的命令
STOPSIGNAL | 容器退出的信号
HEALTHCHECK | 如何进行健康检查
SHELL | 指定默认SHELL类型，SHELL ["executable","parameters"]，默认为 ["bin/sh","-c"]

> 注意:
> 1. HEALTHCHECK NONE：禁止基础镜像中的健康检查
> 1. HEALTHCHECK [OPTIONS] CMD command：根据所执行命令返回值是否为0判断
> [OPTION]支持：
>     - --inerval = DURATION  (默认为：30s)：多久检查一次；
>     - --timeout = DURATION  (默认为：30s)：每次检查等待结果的超时时间；
>     - --retries = N  (默认为：3)：如果失败了，重试几次才最终确定失败。

1. dockerfile 示例：
    ```
    # Command format: Instruction [arguments / command ] ..

    # Base image to use, this nust be set as the first line
    FROM ubuntu

    # Maintainer
    MAINTAINER docker_user docker_user@email.com

    # RUN <command> 或 RUN ["executable","param1","param2"]
    RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list
    RUN apt-get update && apt-get install -y nginx
    RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

    # Commands when creating a new container
    CMD /usr/sbin/nginx

    LABEL version="1.0"
    LABEL description="A nginx image \ that label-values can span multiple lines."
    ```

2. 创建镜像
    ```
    docker build -t build_repo/image_name /dir/docker_file
    ```

3. .dockerignore文件：让Docker忽略匹配模式路径下的目录和文件
    ```
    # comment
        */tmp*
        */*/tmp*
        tmp?
        ~*
    ```
