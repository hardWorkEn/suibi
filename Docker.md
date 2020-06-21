



# Docker

> Docker学习

+ Docker概述
+ Docker安装
+ Docker命令
  + 镜像命令
  + 容器命令
  + 操作命令
+ Docker镜像
+ 容器数据卷
+ DockerFile
+ Docker网络原理
+ IDEA整合Docker
+ Docker Compose
+ Docker Swarm



# Docker安装

## Docker的基本组成

**镜像（image）：**

Docker 镜像是用于创建 Docker 容器的模板，比如 Ubuntu 系统。  

**容器（container）：**

容器是独立运行的一个或一组应用，是镜像运行时的实体。 

**仓库（repository）：**

Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。 

## 安装Docker

> 环境查看

```shell
# 系统内核是3.10以上
[root@izbp1dnzb8iugqzfxyrplrz /]# uname -r
3.10.0-693.2.2.el7.x86_64
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz /]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"
CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

帮助文档：

```shell
# 1.卸载旧的docker
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2.需要的安装包
yum install -y yum-utils
# 3.设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo	# 默认从国外的！不要用
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo		# 推荐用阿里云的
#更新yum软件包索引
yum makecache fast
# 4.安装docker引擎
yum install docker-ce docker-ce-cli containerd.io
# 5.启动docker
systemctl start docker
# 6.判断是否成功
docker version
# 7.hello-world
docker run hello-world
# 8.查看下载的hello-world镜像
[root@izbp1dnzb8iugqzfxyrplrz /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```

了解：卸载docker

```shell
# 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 删除资源
rm -rf /var/lib/docker
```

## 阿里云镜像加速

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hehu4x2l.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# Docker的常用命令

## 帮助命令

```shell
docker version		# 显示docker版本信息
docker info			# 显示docker的系统信息
docker 命令 --help   # 帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/build/

## 镜像命令

**docker images** 查看本地主机上的镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# 解释
REPOSITORY		镜像的仓库
TAG			    镜像的标签
IMAGE ID		镜像的id
CRTATED			镜像的创建时间
SIZE 			镜像的大小

# 可选项
  -a, --all             # 列出所有的镜像
  -q, --quiet           # 只显示ID
```

**docker search** 搜索镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9645                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3506                [OK]                
# 可选项，可以通过stars来过滤
--filter=STARS=3000	#搜索出来的镜像就是stars大于3000的
```

**docker pull** 下载镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker pull mysql
Using default tag: latest
# 指定版本下载
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists 
d51ce1c2e575: Already exists 
c2344adc4858: Already exists 
fcf3ceff18fc: Already exists 
16da0c38dc5b: Already exists 
b905d1797e97: Already exists 
4b50d1c6b05c: Already exists 
d85174a87144: Pull complete 
a4ad33703fa8: Pull complete 
f7a5433ce20d: Pull complete 
3dcd2a278b4a: Pull complete 
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

**docker rmi** 删除镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker rmi 9cfcce23593a	# 删除指定的容器
Untagged: mysql:5.7
Untagged: mysql@sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Deleted: sha256:9cfcce23593a93135ca6dbf3ed544d1db9324d4c40b5c0d56958165bfaa2d46a
Deleted: sha256:98de3e212919056def8c639045293658f6e6022794807d4b0126945ddc8324be
Deleted: sha256:17e8b88858e400f8c5e10e7cb3fbab9477f6d8aacba03b8167d34a91dbe4d8c1
Deleted: sha256:c04c087c2af9abd64ba32fe89d65e6d83da514758923de5da154541cc01a3a1e
Deleted: sha256:ab8bf065b402b99aec4f12c648535ef1b8dc954b4e1773bdffa10ae2027d3e00

[root@izbp1dnzb8iugqzfxyrplrz ~]# docker rmi -f $(docker images -aq)	#删除全部容器
Untagged: mysql:latest
Untagged: mysql@sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6
Deleted: sha256:be0dbf01a0f3f46fc8c88b67696e74e7005c3e16d9071032fa0cd89773771576
Deleted: sha256:086d66e8d1cb0d52e9337eabb11fb9b95960e2e1628d90100c62ea5e8bf72306
Deleted: sha256:f37c61ee1973b18c285d0d5fcf02da4bcdb1f3920981499d2a20b2858500a110
Deleted: sha256:e40b8bca7dc63fc8d188a412328e56caf179022f5e5d5b323aae57d233fb1069
Deleted: sha256:339f6b96b27eb035cbedc510adad2560132925a835f0afddbcc1d311c961c14b
Deleted: sha256:d38b06cdb26a5c98857ddbc6ef531d3f57b00e325c0c314600b712efc7ff6ab0
Deleted: sha256:09687cd9cdf4c704fde969fdba370c2d848bc614689712bef1a31d0d581f2007
Deleted: sha256:b704a4a65bf536f82e5d8b86e633d19185e26313de8380162e778feb2852011a
Deleted: sha256:c37206160543786228aa0cce738e85343173851faa44bb4dc07dc9b7dc4ff1c1
Deleted: sha256:12912c9ec523f648130e663d9d4f0a47c1841a0064d4152bcf7b2a97f96326eb
Deleted: sha256:57d29ad88aa49f0f439592755722e70710501b366e2be6125c95accc43464844
Deleted: sha256:b17c024283d0302615c6f0c825137da9db607d49a83d2215a79733afbbaeb7c3
Deleted: sha256:13cb14c2acd34e45446a50af25cb05095a17624678dbafbcc9e26086547c1d74
Untagged: hello-world:latest
Untagged: hello-world@sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

## 容器命令

**新建容器并启动**

```shell
docker run [可选参数] image
# 参数说明
--name="Name"	 容器名字，用来区分容器
-d			    后台方式运行
-it				使用交互方式运行，进入容器查看内容
-p（小P）		  指定容器的端口
-P（大P）		  随机指定端口

# 测试，启动并进入容器
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker run -it centos /bin/bash
[root@e77688629b3b /]# 
# 从容器中退回至主机
[root@e77688629b3b /]# exit 
exit
[root@izbp1dnzb8iugqzfxyrplrz ~]# 
```

**列出所有在运行的容器**

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                          PORTS               NAMES
e77688629b3b        centos              "/bin/bash"         3 minutes ago       Exited (0) About a minute ago                       bold_feistel
2ac12894b685        hello-world         "/hello"            14 minutes ago      Exited (0) 14 minutes ago                           competent_thompson
cce96f1e95fd        hello-world         "/hello"            2 hours ago         Exited (0) 2 hours ago                              infallible_blackwell
f4ba730d7fbb        hello-world         "/hello"            24 hours ago        Exited (0) 24 hours ago                             sad_mccarthy

# 可选项
-a # 列出当前正在运行的容器+历史运行过的容器
-q # 只显示容器的编号
```

**退出容器**

```shell
exit		# 直接容器停止并退出
Ctrl + P + Q # 容器不停止退出 
```

**删除容器**

```shell
docker rm 容器id		# 删除指定的容器，不能删除正在运行的容器，如果要强制删除，rm -f
docker rm -f $(docker ps -aq)	#删除所有的容器
docker ps -a -q|xargs docker rm	# 删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止当前容器
```

## 常用其他命令

**后台启动容器**

```shell
# 命令 docker run -d 镜像名！
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker run -d centos

# 问题docker ps,发现centos停止了
# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
```

**查看日志**

```shell
docker logs -f -t --tail
# 显示日志
-tf			    # 显示日志
--tail number	 # 显示日志数
```

**查看容器中的进程**

```shell
docker top 容器id

docker inspect 容器id
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker inspect 831691599b88
[
    {
        "Id": "sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f",
        "RepoTags": [
            "centos:latest"
        ],
        "RepoDigests": [
            "centos@sha256:4062bbdd1bb0801b0aa38e0f83dece70fb7a5e9bce223423a68de2d8b784b43b"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-06-17T00:22:25.47282687Z",
        "Container": "0a6b8cbdee7218d1da84145e867c8ce1c36d226a5cfca208125d08ac56f7c5af",
        "ContainerConfig": {
            "Hostname": "0a6b8cbdee72",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:72ff1748d360d0069c91508ca3ffde0d7748989c75d193eee3b0e85c62557efa",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "DockerVersion": "18.09.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:72ff1748d360d0069c91508ca3ffde0d7748989c75d193eee3b0e85c62557efa",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 215320025,
        "VirtualSize": 215320025,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/bd1b7f65930ade15afcdeb56846139ed580c4f0117965e5511a36fb82fddca02/merged",
                "UpperDir": "/var/lib/docker/overlay2/bd1b7f65930ade15afcdeb56846139ed580c4f0117965e5511a36fb82fddca02/diff",
                "WorkDir": "/var/lib/docker/overlay2/bd1b7f65930ade15afcdeb56846139ed580c4f0117965e5511a36fb82fddca02/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:eb29745b8228e1e97c01b1d5c2554a319c00a94d8dd5746a3904222ad65a13f8"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器修改配置

# 命令 
docker exec -it 容器id /bin/bash
# 测试
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker run -it centos /bin/bash
[root@a958bcf4b5b4 /]# [root@izbp1dnzb8iugqzfxyrplrz ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a958bcf4b5b4        centos              "/bin/bash"         8 seconds ago       Up 7 seconds                            brave_fermi
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker exec -it a958bcf4b5b4 /bin/bash
# 方式二
docker attach 容器id

# 比较：
docker exec  # 进入容器后开启一个新的终端
docker attach # 进入容器正在执行的终端，不会启动新的
```

**从容器内拷贝文件到主机中**

```shell
docker cp 容器id:容器内路径 	目的主机路径
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker cp a958bcf4b5b4:/home/a.java /home
```

> 启动elasticsearch
>
> docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
>
> 增加内存限制
>
> docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

## 可视化

+ portainer

```shell
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock --restart=always --name prtainer portainer/portainer
```



**什么是portainer? **

Docker图形化管理界面，提供一个后台面板！

访问测试：47.98.124.109:8080

# Docker镜像

## commit镜像

```shell
docker commit	# 提交容器成为一个新的副本

docker commit -m="描述信息" -a="作者" 容器id  目标镜像名:[版本]
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
803a9947b50d        tomcat:9.0          "catalina.sh run"   13 minutes ago      Up 13 minutes       0.0.0.0:8080->8080/tcp   stupefied_varahamihira
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker commit -a="ryqbdyq" -m="直接用的tomcat" 803a9947b50d tomcat_new:1.0.0
sha256:8c25147c16feade5960d65540dfae67991d3b709073679fae6ed38b544c48921
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
tomcat_new            1.0.0               8c25147c16fe        11 seconds ago      652MB
tomcat                9.0                 2eb5a120304e        9 days ago          647MB
tomcat                latest              2eb5a120304e        9 days ago          647MB
nginx                 latest              2622e6cca7eb        10 days ago         132MB
portainer/portainer   latest              cd645f5a4769        2 weeks ago         79.1MB
elasticsearch         7.6.2               f29a1ee41030        2 months ago        791MB
```

# 容器数据卷

## 什么是容器数据卷

**docker理念回顾**

将应用和环境打包成一个镜像！

如果数据在容器中，那么容器删除，数据就会丢失！==需求：数据可以持久化==

容器之间可以有一个数据共享的技术！Docker容器产生的数据，同步到本地！

这就是卷技术，目录的挂载，将我们容器内的目录，挂载到Linux上面！

==总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！==

## 使用数据卷

> 方式一：直接使用命令来挂载  ==-v==

```shell
docker run -it -v 主机目录:容器内目录

# 测试
[root@izbp1dnzb8iugqzfxyrplrz ~]# docker run -it -v /home/ceshi:/home centos /bin/bash
```

## 实战：安装MySQL

```shell
# 获取镜像
docker pull mysql:5.7

# 运行容器，需要做数据挂载 安装启动mysql，需要配置密码的，这是要注意的点
官方测试： docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

[root@izbp1dnzb8iugqzfxyrplrz ceshi]# docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
```

假设我们将容器删除，发现我们挂载到本地的数据卷依旧没有丢失，这就实现了容器数据持久化功能！

## 具名挂载和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
[root@izbp1dnzb8iugqzfxyrplrz data]# docker run -d -P -v /etc/nginx nginx

# 查看所有的volume的情况
[root@izbp1dnzb8iugqzfxyrplrz data]# docker volume ls
DRIVER              VOLUME NAME
local               445d3c224a80179b2cc1b533126d23f5a852ff54d51c2fc5f8bde7a5e3e777d8
local               828970eb2cc93f50187217ca2fdeb3ec8445908e8f6ae982d6dfa2fc0618effd
local               e558e82db8f7177a73645f57b48ed452486a210f2ede5043b66db8abd8c9338e
# 这里发现，这种就是匿名挂载，我们在-v只写了容器内的路径，没有写容器外的路径


# 具名挂载
[root@izbp1dnzb8iugqzfxyrplrz data]# docker run -d -P --name nginx01 -v juming-nginx:/etc/nginx nginx

[root@izbp1dnzb8iugqzfxyrplrz data]# docker volume ls
DRIVER              VOLUME NAME
local               445d3c224a80179b2cc1b533126d23f5a852ff54d51c2fc5f8bde7a5e3e777d8
local               828970eb2cc93f50187217ca2fdeb3ec8445908e8f6ae982d6dfa2fc0618effd
local               e558e82db8f7177a73645f57b48ed452486a210f2ede5043b66db8abd8c9338e
local               juming-nginx

# 通过 -v 卷名:容器内路径
# 查看卷路径
[root@izbp1dnzb8iugqzfxyrplrz data]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2020-06-20T18:22:52+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有的docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volume/xxxxx/_data`目录下

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况下在使用的==具名挂载==

```shell
# 如何区别是匿名挂载还是具名挂载还是指定路径挂载？
-v 容器内路径	# 匿名挂载
-v 卷名：容器内路径		# 具名挂载
-v 容器外路径：容器内路径	# 指定路径挂载
```

## 初始Dockerfile

Dockerfile就是用来构建docker镜像的构建文件！命令脚本。

通过这个脚本可以生成镜像

```shell
# 创建一个dockerfile文件，名字可以随机，建议dockerfile
# 文件中内容
FROM centos
VOLUME ["volume01","volume02"]
CMD echo "-----------end-----------"
CMD /bin/bash
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz docker-test-volume]# docker build -f /home/docker-test-volume/dockerfile1 -t ryqbdyq/centos:1.0.0 .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
latest: Pulling from library/centos
6910e5a164f7: Pull complete 
Digest: sha256:4062bbdd1bb0801b0aa38e0f83dece70fb7a5e9bce223423a68de2d8b784b43b
Status: Downloaded newer image for centos:latest
 ---> 831691599b88
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in f4e3cd85ab1a
Removing intermediate container f4e3cd85ab1a
 ---> aa2697722347
Step 3/4 : CMD echo "------------end-----------"
 ---> Running in 406b17d95f72
Removing intermediate container 406b17d95f72
 ---> a2f1fd32d16f
Step 4/4 : CMD /bin/bash
 ---> Running in 23d3d2d1ec25
Removing intermediate container 23d3d2d1ec25
 ---> 9003aca80d33
Successfully built 9003aca80d33
Successfully tagged ryqbdyq/centos:1.0.0
[root@izbp1dnzb8iugqzfxyrplrz docker-test-volume]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
ryqbdyq/centos        1.0.0               9003aca80d33        19 seconds ago      215MB
```

这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像！

假设构建镜像时候没有挂在卷，要手动镜像挂载， -v 卷名：容器内路径

## 数据卷容器

多个mysql同步

# DockerFile

## DockerFile介绍

Dockerfile就是用来构建docker镜像的构建文件！

构建步骤：
1、编写一个dockerfile文件

2、docker build 构建成为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像（DockerHub、阿里云镜像仓库）

## DockerFile构建过程

**基础知识：**

1、每个保留关键字（指令）都是必须是大写字母

2、指令从上到下顺序执行

3、# 表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交

![1592653325164](C:\Users\ADMINI~1\AppData\Local\Temp\1592653325164.png)

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品

Docker容器：容器就是镜像运行起来提供服务的



## DockerFile的指令

```shell
FROM 			# 基础镜像，一切从这里开始构建
MAINTAINER 		 # 镜像是谁写的，姓名+邮箱
RUN				# 镜像构建的时候需要执行的命令
ADD				# 步骤：tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR			 # 镜像的工作目录
VOLUME			 # 挂载的目录
EXPOSE			 # 暴露端口配置
CMD 			 # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		  # 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD			 # 当构建一个被继承dockerfile 这个时候就会运行ONBUILD指令
COPY			 # 类似ADD ，将我们文件拷贝到镜像中
ENV				 # 构建的时候设置环境变量
```

## 实战测试

> 创建一个自己的centos

```shell
# 1、编写dockerfile文件
[root@izbp1dnzb8iugqzfxyrplrz dockerfile]# cat mudockerfile-centos 
FROM centos
MAINTAINER ryqbdyq<1312626670@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

# 2、通过这个文件构建镜像
[root@izbp1dnzb8iugqzfxyrplrz dockerfile]# docker build -f mudockerfile-centos -t mycentos:0.1 .

Step 10/10 : CMD /bin/bash
 ---> Running in a685155e3216
Removing intermediate container a685155e3216
 ---> e94407c9f115
Successfully built e94407c9f115
Successfully tagged mycentos:0.1

# 3、测试运行
```

> CMD 和 ENTRYPOINT 区别

```shell
CMD 			 # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		  # 指定这个容器启动的时候要运行的命令，可以追加命令
```

## 实战：Tomcat镜像

1、准备镜像文件 	tomcat压缩包，jdk压缩包

![1592709701506](C:\Users\ADMINI~1\AppData\Local\Temp\1592709701506.png)

2、编写dockerfile文件，官方命名`Dockerfile`，build会自动寻找这个文件，就不需要-f指定了

```shell
FROM centos
MAINTAINER ryqbdyq<ryqbdyq@aliyun.com>

COPY readme.txt /usr/local/readme.txt
ADD jdk-8u65-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.35.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_65
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.35
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.35
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.35/bin/startup.sh && tail -f /usr/local/apache-tomcat-9.0.35/bin/logs/catalina.out
```

3、构建镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker build -t diytomcat .
```



## 发布自己的镜像

> DockerHub

1、地址https://hub.docker.com/ 注册自己的账号

2、确定这个账号可以登录

3、在我们服务器上提交自己的镜像

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker login --help
Usage:	docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
  
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker login -u chenchao666
Password: 
Error response from daemon: Get https://registry-1.docker.io/v2/: unauthorized: incorrect username or password
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker login -u chenchao666
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

4、登录完毕就可以提交镜像了

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker push tomcat_new
The push refers to repository [docker.io/library/tomcat_new]
e5f4e06aa4b8: Preparing 
123a7175f991: Preparing 
68b9387df273: Preparing 
a1c4399f9b22: Preparing 
4f866e977815: Preparing 
f73b2345c404: Waiting 
f5181c7ef902: Waiting 
2e5b4ca91984: Waiting 
527ade4639e0: Waiting 
c2c789d2d3c5: Waiting 
8803ef42039d: Waiting 
denied: requested access to the resource is denied	# 拒绝
```

==特别注意==

推送镜像的规范是：`docker push 注册用户名/镜像名`

tag命令修改为规范的镜像：` # docker tag 镜像id  chenchao666/tomcat:1.0`

![1592716618984](C:\Users\ADMINI~1\AppData\Local\Temp\1592716618984.png)



> 发布到阿里云镜像服务上

1、登录阿里云

2、找到容器镜像服务

3、创建命名空间

![1592716885644](C:\Users\ADMINI~1\AppData\Local\Temp\1592716885644.png)

4、创建容器镜像

![1592716976887](C:\Users\ADMINI~1\AppData\Local\Temp\1592716976887.png)

5、浏览

![1592717067872](C:\Users\ADMINI~1\AppData\Local\Temp\1592717067872.png)==特别注意==

需要根据阿里云规范重新打一个tag

```shell
docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/cc_docker_rep/docker-test:[镜像版本号]
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker tag fa95c08148d4 registry.cn-hangzhou.aliyuncs.com/cc_docker_rep/docker-test:1.0.2
```

然后推送

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker push registry.cn-hangzhou.aliyuncs.com/cc_docker_rep/docker-test:1.0.2
```

## 小结

![1592717685704](C:\Users\ADMINI~1\AppData\Local\Temp\1592717685704.png)



# Docker网络

## 理解Docker0

![1592718621925](C:\Users\ADMINI~1\AppData\Local\Temp\1592718621925.png)

```shell
# 问题：docker 是如何处理容器网络访问的？
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址 ip addr ，发现容器启动的时候得到一个eth0@if85 ip地址，docker分配的
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
84: eth0@if85: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# 思考，linux能不能ping 通容器内部
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.062 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.053 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.039 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.048 ms
64 bytes from 172.17.0.2: icmp_seq=5 ttl=64 time=0.050 ms
64 bytes from 172.17.0.2: icmp_seq=6 ttl=64 time=0.036 ms
可以ping通docker容器内部
```

> 原理

1、我们每启动一个docker容器，docker就会给docker容器分配一个ip，只要安装了docker，就会有一个网卡docker0。桥接模式，使用的技术是evth-pair技术！

![1592737560675](C:\Users\ADMINI~1\AppData\Local\Temp\1592737560675.png)

```shell
 # 我们发现这个容器带来网卡，都是一对一对的
 # evth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
 # 正因为有这个特性，evth-pair充当一个桥梁，连接各种虚拟网络设备的
```

绘制一个网络模型图

![1592738015107](C:\Users\ADMINI~1\AppData\Local\Temp\1592738015107.png)

结论：tomcat01和tomcat02是公用的一个路由器，docker0

所有容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的IP



## --link

> 思考一个场景，我们编写了一个微服务，database url=ip:，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以用名字来进行访问容器

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 如何解决呢？
# 通过--link可以解决网络连通问题
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
ea20773cfbd677a82ae7982a297d089923086a85a631cf30300905d8bca97a79
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.092 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.061 ms

# 反向可以ping通吗？tomcat02 ping tomcat03？
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known			# ping不通.....
```

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker exec -it ea20773cfbd6 cat  /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	tomcat02 502825bfc9a1
172.17.0.4	ea20773cfbd6

```

本质探究：--link就是我们在hosts配置中增加了一个 172.17.0.3	tomcat02  	502825bfc9a1

现在已经不建议使用--link了！

自定义网络！不适用docker0！

docker0问题：不支持容器名连接访问！

## 自定义网络

```shell
# 查看所有的网络
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
17392b26d2cd        bridge              bridge              local
ba3c2b0b9f9d        host                host                local
6a036869904c        none                null                local

```

**网络模式**

bridge：桥接（默认）

none：不配置网络

hosts：和宿主机共享网络

> 自定义网络

```shell
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
78b72a26b2c35ff7527ccf171173f281d3b64d3d7b04c4ad0b90b04a4cb6f7cc
[root@izbp1dnzb8iugqzfxyrplrz ryqbdyq]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
17392b26d2cd        bridge              bridge              local
ba3c2b0b9f9d        host                host                local
78b72a26b2c3        mynet               bridge              local
6a036869904c        none                null                local
```





