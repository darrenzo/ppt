#      Docker技术原理以及应用 



[TOC]

## 安装

###      在Windows安装Docker  

在Windows操作系统中，只需要下载官方的安装程序，即可安装Docker Desktop，启动Docker Desktop后，可以通过操作面板修改一些配置。

###  在centos7下安装Docker 

```sh
yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

yum update

yum install -y docker-ce
```

## 镜像

镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等），镜像一般分为：

- 基础操作系统镜像
- 普通镜像

### 启动并搜索镜像

```shell
# systemctl start docker
# systemctl enable docker
Redirecting to /bin/systemctl start  docker.service

# docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   5633                [OK]
ansible/centos7-ansible            Ansible on Centos7                              125                                     [OK]
jdeathe/centos-ssh                 OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   114                                     [OK]
consol/centos-xfce-vnc             Centos container with "headless" VNC session…   100                                     [OK]
centos/mysql-57-centos7            MySQL 5.7 SQL database server                   63
imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              57                                      [OK]
tutum/centos                       Simple CentOS docker image with SSH access      44
centos/postgresql-96-centos7       PostgreSQL is an advanced Object-Relational …   39
kinogmt/centos-ssh                 CentOS with SSH                                 29                                      [OK]
centos/php-56-centos7              Platform for building and running PHP 5.6 ap…   22
pivotaldata/centos-gpdb-dev        CentOS image for GPDB development. Tag names…   10
guyton/centos6                     From official centos6 container with full up…   9                                       [OK]
drecom/centos-ruby                 centos ruby                                     6                                       [OK]
darksheer/centos                   Base Centos Image -- Updated hourly             3                                       [OK]
mamohr/centos-java                 Oracle Java 8 Docker image based on Centos 7    3                                       [OK]
pivotaldata/centos                 Base centos, freshened up a little with a Do…   3
pivotaldata/centos-mingw           Using the mingw toolchain to cross-compile t…   2
miko2u/centos6                     CentOS6 日本語環境                                   2                                       [OK]
pivotaldata/centos-gcc-toolchain   CentOS with a toolchain, but unaffiliated wi…   2
indigo/centos-maven                Vanilla CentOS 7 with Oracle Java Developmen…   1                                       [OK]
mcnaughton/centos-base             centos base image                               1                                       [OK]
blacklabelops/centos               CentOS Base Image! Built and Updates Daily!     1                                       [OK]
pivotaldata/centos7-dev            CentosOS 7 image for GPDB development           0
smartentry/centos                  centos with smartentry                          0                                       [OK]
pivotaldata/centos6.8-dev          CentosOS 6.8 image for GPDB development         0

```

### 获取镜像

```shell
docker [image] pull NAME[:TAG]
```

如果未指定tag，则为默认的latest

```shell
docker pull 163.docker.com/nginx

# docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
22e816666fd6: Pull complete
079b6d2a1e53: Pull complete
11048ebae908: Pull complete
c58094023a2e: Pull complete
Digest: sha256:a7b8b7b33e44b123d7f997bd4d3d0a59fafc63e203d17efedf09ff3f6f516152
Status: Downloaded newer image for ubuntu:latest

# docker pull ubuntu:xenial
xenial: Pulling from library/ubuntu
a1298f4ce990: Pull complete
04a3282d9c4b: Pull complete
9b0d3db6dc03: Pull complete
8269c605f3f1: Pull complete
Digest: sha256:c4fcf8718cd249cfd7703563200051850828be90070358fe7bcc6e137271d070
Status: Downloaded newer image for ubuntu:xenial

```

### 列出本地镜像

```shell
# docker image ls
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
ubuntu                          xenial              b9409899fe86        10 days ago         122MB
ubuntu                          latest              cf0f3ca922e0        10 days ago         64.2MB

```

## 容器

容器是镜像的一个运行实例

windows版本的Docker Desktop可以切换运行linux容器或者windows容器，因为依赖于windows的容器技术、 Hyper-V Contianer，目前只有Windows Server 2016 或 Windows 10以上版本可用。

### 创建容器

命令：

```shell
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

一般来说，docker容器在启动并执行`COMMAND`参数的内容完毕之后，容器会自动终止

```shell
# docker run ubuntu echo 'hello'
hello
```

如果在容器内启动了终端，则终端退出后，容器也会自动终止

```shell
# docker run -it ubuntu:xenial /bin/bash
root@319638f69900:/# cat /etc/issue
Ubuntu 16.04.6 LTS \n \l

root@319638f69900:/# 

```

在上面的例子中，`/bin/bash`为容器启动后执行的命令，`-i`让容器的标准输入保持打开，`-t`让docker分配一个伪终端并绑定到容器的标准输入上。

另外，常用的参数还有

- -d：后台运行容器
- -v：挂载主机的文件卷到容器内
- -p：映射本地主机端口
- --name：容器名称
- --restart：no | on-failure | unless-stopped | always
- --memory：内存限制

### 查看已有的容器

```shell
# docker ps --all
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS                    NAMES
09e252890d27        ubuntu              "/bin/bash"              14 seconds ago       Exited (0) 6 seconds ago                                 xenodochial_gauss
b4fbb95fc520        ubuntu              "echo hello"             About a minute ago   Exited (0) About a minute ago                            jolly_bhabha
319638f69900        ubuntu:xenial       "/bin/bash"              28 minutes ago       Up 28 minutes                                            jovial_zhukovsky

```

### 删除容器

```shell
# docker container rm b4fbb95fc520
b4fbb95fc520
# docker rm 09e252890d27
09e252890d27
# docker rm -f 319638f69900
# 生产环境不用
319638f69900
```

### 进入容器

当容器为启动状态时，才可以进入容器

```shell
docker attach CONTAINER_ID

docker exec -it CONTAINER_ID bash
```

使用exec时，在输入时exit，不会导致容器退出，所以一般推荐使用exec

docker run命令不带cmd参数时的表现

| 参数 |            结果            |
| :--: | :------------------------: |
| -idt |       一直在后台运行       |
| -it  |        出现终端输入        |
|  -d  |        容器直接退出        |
|  -t  | 无法正常输入，容器不会退出 |
|  -i  |  可以正常输入，没有提示符  |

### 数据卷

数据卷是一个特殊的目录，一般只供容器使用，一般存在主机的`/var/lib/docker/volumes/`，在容器内对数据卷的修改，实际修改的是对应的数据卷的目录里的内容。

生成数据卷的方式：

```shell
# 1.执行docker run命令时未指定挂载目录
docker run ubuntu

# 2.在dockerfile中声明了VOLUME
FROM ubuntu

VOLUME ["/data"]
```

此时生成的数据卷称为匿名卷。

也可以通过以下方法生成具名卷：

```shell
docker volume create my-vol
```

一种简单的利用数据卷的方法

```shell
docker run -v /mydata --name db alpine

docker run -d --volumes-from db --name db1 alpine
docker run -d --volumes-from db --name db2 alpine
```



### 基于仓库commit

```shell
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

- -a 作者
- -c 创建镜像时执行的dockerfile指令
- -m 提交信息
- -p 提交时容器暂停

## 仓库

集中存放镜像的地方，又分为私有仓库以及公有仓库。

官方的Registry——Docker Hub

国内的镜像服务器：网易云镜像库

## 为镜像添加SSH服务

### 基本流程

```shell
# docker run -it ubuntu:xenial /bin/bash
root@5b2a61316dcf:/# apt-get update
root@5b2a61316dcf:/# apt-get install -y openssh-server
root@5b2a61316dcf:/# mkdir -p /var/run/sshd
root@5b2a61316dcf:/# echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config
root@5b2a61316dcf:/# /usr/sbin/sshd -D &

# 回到宿主主机
# 创建一个ssh密钥
root@5b2a61316dcf:/# exit
[root@vibrant-bounty-1 ~]# mkdir /root/.ssh
[root@vibrant-bounty-1 ~]# ssh-keygen -t rsa

# 接下来重新将宿主主机内的公钥（/root/.ssh/id_rsa.pub）信息复制到容器内
[root@vibrant-bounty-1 ~]# docker ps -a
5b2a61316dcf        ubuntu:xenial       "/bin/bash"              13 hours ago        Exited (127) 2 minutes ago                            objective_wilbur
[root@vibrant-bounty-1 ~]# docker start 5b2a61316dcf
[root@vibrant-bounty-1 ~]# docker attach 5b2a61316dcf
root@5b2a61316dcf:/# apt install -y vim
root@5b2a61316dcf:/# mkdir /root/.ssh
root@5b2a61316dcf:/# vi /root/.ssh/authorized_keys

# 重启ssh服务
root@5b2a61316dcf:/# service ssh restart

# 创建一个启动脚本
root@5b2a61316dcf:/# vi /run.sh
#!/bin/bash
/usr/sbin/sshd -D

# chmod +x /run.sh

# 查看容器的IP
root@5b2a61316dcf:/# ifconfig
172.17.0.3

# 此时我们在宿主主机内登陆容器
[root@vibrant-bounty-1 ~]# ssh root@172.17.0.3
# 输入密码并登陆成功
Enter passphrase for key '/root/.ssh/id_rsa':

# 此时我们将远程主机的公钥也加进来
root@5b2a61316dcf:/# vi /root/.ssh/authorized_keys

# 重启ssh服务
root@5b2a61316dcf:/# service ssh restart

# 退出容器
root@5b2a61316dcf:/# exit

# 基于此容器创建一个新的镜像
[root@vibrant-bounty-1 ~]# docker commit -a 'lianggn' -m 'my ssh ubuntu' 5b2a61316dcf my-ssh-ubuntu:1.0.0

[root@vibrant-bounty-1 ~]# docker run -d -p 8822:22 my-ssh-ubuntu:1.0.0 /run.sh

# 此时我们可以在远程登陆容器
$ ssh -p 8822 root@216.24.182.234

```

### Dockerfile版本

|    指令     |                说明                |
| :---------: | :--------------------------------: |
|     ARG     |    定义创建镜像过程中使用的变量    |
|    FROM     |      指定所创建镜像的基础镜像      |
|    LABEL    |   为生成的镜像添加元数据标签信息   |
|   EXPOSE    |      声明镜像内服务监听的端口      |
|     ENV     |            指定环境变量            |
| ENTRYPOINT  |       指定镜像的默认入口命令       |
|   VOLUME    |        创建一个数据卷挂载点        |
|    USER     |    指定运行容器时的用户名或UID     |
|   WORKDIR   |            配置工作目录            |
|   ONBUILD   | 创建子镜像时指定自动执行的操作指令 |
| STOPSIGNAL  |          指定退出的信号值          |
| HEALTHCHECK |   配置所启动容器如何进行健康检查   |
|    SHELL    |         指定默认shell类型          |
|     RUN     |            运行指定命令            |
|     CMD     |    启动容器时指定默认执行的命令    |
|     ADD     |           添加内容到镜像           |
|    COPY     |           复制内容到镜像           |

```shell
## /ssh-dockerfile/dockerfile

# 设置基础镜像
FROM ubuntu:xenial

# 添加镜像元数据
LABEL version="1.0.0"
LABEL author="liangguining"

# 安装ssh服务
RUN apt-get update \
    && apt-get install -y openssh-server \
    && mkdir -p /var/run/sshd \
    && echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config \
    && mkdir -p /root/.ssh

# 增加启动脚本
ADD run.sh /run.sh

# 增加ssh key
ADD authorized_keys /root/.ssh/authorized_keys

# 配置执行权限
RUN chmod +x /run.sh

# 开放端口
EXPOSE 22

CMD ["/run.sh"]
```

```shell
# 创建启动脚本
[root@vibrant-bounty-1 ~]# touch /ssh-dockerfile/run.sh
[root@vibrant-bounty-1 ~]# vi /ssh-dockerfile/run.sh
#!/bin/bash
/usr/sbin/sshd -D

# 增加ssh key
[root@vibrant-bounty-1 ~]# touch /ssh-dockerfile/authorized_keys
[root@vibrant-bounty-1 ~]# vi /ssh-dockerfile/authorized_keys

# 创建镜像
[root@vibrant-bounty-1 ~]# docker build -t ssh-dockerfile /ssh-dockerfile

# 启动容器
[root@vibrant-bounty-1 ~]# docker run -d -p 8830:22 --name 'ssh-docker-1' ssh-dockerfile
8fffd954f222d2cfffe19a4e1d7280d5638f063470f71858887bd86786eb0771

[root@vibrant-bounty-1 ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
8fffd954f222        ssh-dockerfile        "/run.sh"                16 seconds ago      Up 14 seconds       0.0.0.0:8830->22/tcp     ssh-docker-1

```

## 基于已有的镜像创建一个web服务

### 创建nginx服务

```shell
[root@vibrant-bounty-1 ~]# mkdir /nginx-dockerfile
[root@vibrant-bounty-1 ~]# touch /nginx-dockerfile/dockerfile
[root@vibrant-bounty-1 ~]# touch /nginx-dockerfile/run.sh
```

```shell
## /nginx-dockerfile/dockerfile
FROM ssh-dockerfile

LABEL author="liangguinig"
LABEL version="1.0.0"

RUN apt-get update \
    && apt-get install -y nginx \
    && rm -rf /etc/nginx/sites-enabled/

COPY run.sh /run.sh

COPY nginx.conf /etc/nginx/conf.d/default.conf

RUN chmod +x /run.sh

VOLUME ["website"]

WORKDIR /etc/nginx

EXPOSE 80

CMD ["/run.sh"]

```

```shell
## /nginx-dockerfile/run.sh
#!/bin/bash
/usr/sbin/nginx &
/usr/sbin/sshd -D

```

```shell
## /nginx-dockerfile/nginx.conf
server {

    listen       80;

    server_name  localhost;

    access_log /website/access.log;

    error_log /website/error.log;

    location / {

        root   /website/www/;

        index  index.html;
    }
}
```

启动

```shell
[root@vibrant-bounty-1 ~]# docker build -t nginx-dockerfile /nginx-dockerfile
[root@vibrant-bounty-1 ~]# docker run -d -p 8888:80 -p 8038:22 -v /docker-data/website:/website --name 'nginx-dockerfile' nginx-dockerfile
```

### 创建静态页面容器

```shell
## /page-dockerfile/dockerfile
FROM ssh-dockerfile

VOLUME ["www"]
```

启动

```shell
[root@vibrant-bounty-1 ~]# docker build -t page-dockerfile /page-dockerfile
[root@vibrant-bounty-1 ~]# docker run -d -p 8889:80 -p 8039:22 -v /docker-data/website/www:/www --name 'page-dockerfile' page-dockerfile
```

此时，这个简单的web服务由两个容器完成，nginx容器负责分发资源，静态页面容器负责管理页面资源。

## Docker compose

### 安装docker-compose

```shell
[root@vibrant-bounty-1 ~]# sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   617    0   617    0     0   1253      0 --:--:-- --:--:-- --:--:--  1254
100 15.4M  100 15.4M    0     0  8327k      0  0:00:01  0:00:01 --:--:-- 18.0M
[root@vibrant-bounty-1 ~]# chmod +x /usr/local/bin/docker-compose
[root@vibrant-bounty-1 ~]# ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
[root@vibrant-bounty-1 ~]# docker-compose --version
docker-compose version 1.24.1, build 4667896b

```

### web容器的docker compose版本

```yaml
version: '3'
services:
  nginx:
    build: /nginx-dockerfile/
    ports:
      - '11180:80'
      - '11122:22'
    depends_on:
      - page
    volumes:
      - /docker-data/website:/website
  page:
    build: /page-dockerfile/
    ports:
      - '11222:22'
    volumes:
      - /docker-data/website:/website
```

