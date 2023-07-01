# Docker学习

> [(20条消息) Docker快速入门总结笔记_Code0cean的博客-CSDN博客_docker快速入门](https://blog.csdn.net/huangjhai/article/details/118854733)
>
> [(20条消息) Docker进阶篇_Barry Yan的博客-CSDN博客](https://blog.csdn.net/Mr_YanMingXin/article/details/119597321)

## Docker 概叙

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版），我们用社区版就可以了。官网：https://docs.docker.com/

## Docker安装

1. 卸载旧版本

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

```

2. 下载安装包

   ``` shell
   yum install -y yum-utils
   ```

3. 设置镜像仓库

```shell
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  #国外的地址
    
    # 设置阿里云的Docker镜像仓库
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  
```

4. 更新索引

```shell
yum makecache fast
```

5. 安装匹配

docker-ce 是社区版，docker-ee 企业版

```shell
 yum install docker-ce docker-ce-cli containerd.io
```

6. 启动docker

```shell
systemctl start docker
# 查看当前版本号，是否启动成功
docker version
# 设置开机自启动
systemctl enable docker
```

7. docker version查看

![image-20220930164956847](D:/图/3150/image-20220930164956847.png)

8. 配置阿里云镜像加速

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://axvfsf7e.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker常用命令

### 帮助命令

```shell
docker version          #查看docker的版本信息
docker info             #查看docker的系统信息,包括镜像和容器的数量
docker 命令 --help       #帮助命令(可查看可选的参数)
docker COMMAND --help
```

命令的帮助文档地址:https://docs.docker.com/engine/reference/commandline/docker/

### 镜像命令

1. **docker images** 查看本地主机的所有镜像

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   11 months ago   13.3kB

#解释:
1.REPOSITORY  镜像的仓库源

2.TAG  镜像的标签

3.IMAGE ID 镜像的id

4.CREATED 镜像的创建时间

5.SIZE 镜像的大小


# 可选参数

-a/--all 列出所有镜像

-q/--quiet 只显示镜像的id
```

2. **docker search** 搜索镜像

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10308     [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3819      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   754                  [OK]
percona                           Percona Server is a fork of the MySQL relati…   517       [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   86
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   79
centurylink/mysql                 Image containing mysql. Optimized to be link…   60                   [OK]


#可选参数

Search the Docker Hub for images

Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output
      
      
#搜索收藏数大于3000的镜像
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10308     [OK]
mariadb   MariaDB is a community-developed fordockerk of MyS…   3819      [OK]
```

3. **docker pull 镜像名[:tag]** 下载镜像

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker pull mysql
Using default tag: latest            #如果不写tag默认就是latest
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete          #分层下载,docker image的核心-联合文件系统
fedd960d3481: Pull complete
7ab947313861: Pull complete
64f92f19e638: Pull complete
3e80b17bff96: Pull complete
014e976799f9: Pull complete
59ae84fee1b3: Pull complete
ffe10de703ea: Pull complete
657af6d90c83: Pull complete
98bfb480322c: Pull complete
6aa3859c4789: Pull complete
1ed875d851ef: Pull complete
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #下载来源的真实地址  #docker pull mysql等价于docker pull docker.io/library/mysql:latest
```

指定版本

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
6ec7b7d162b2: Already exists
fedd960d3481: Already exists
7ab947313861: Already exists
64f92f19e638: Already exists
3e80b17bff96: Already exists
014e976799f9: Already exists
59ae84fee1b3: Already exists
7d1da2a18e2e: Pull complete
301a28b700b9: Pull complete
529dc8dbeaf3: Pull complete
bc9d021dc13f: Pull complete
Digest: sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

4. docker rmi 删除镜像

```shell
#1.删除指定的镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  镜像id
#2.删除多个镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  镜像id 镜像id 镜像id
#3.删除全部的镜像id
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker rmi -f  $(docker images -aq)
```

### 容器命令

如拉取一个centos镜像

```shell
docker pull centos
```

运行容器说明

```shell
docker run [可选参数] image
# -restart=always 
#参数说明
--name="名字"           指定容器名字
-d                     后台方式运行
-it                    使用交互方式运行,进入容器查看内容
-p                     指定容器的端口
(
-p ip:主机端口:容器端口  配置主机端口映射到容器端口
-p 主机端口:容器端口
-p 容器端口
)
-P                     随机指定端口(大写的P)

```

运行并进入容器centos

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker run -it centos /bin/bash
[root@bd1b8900c547 /]# ls      
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

退出容器命令：

```shell
#exit 停止并退出容器（后台方式运行则仅退出）
#Ctrl+P+Q  不停止容器退出
[root@bd1b8900c547 /]# exit
exit
[root@iZwz99sm8v95sckz8bd2c4Z ~]#
```

列出运行过的容器命令：

```shell
#docker ps 
     # 列出当前正在运行的容器
-a   # 列出所有容器的运行记录
-n=? # 显示最近创建的n个容器
-q   # 只显示容器的编号
```

删除容器命令：

```shell
docker rm 容器id                 #删除指定的容器,不能删除正在运行的容器,强制删除使用 rm -f
docker rm -f $(docker ps -aq)   #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器
```

启动和停止容器命令：

```shell
docker start 容器id          #启动容器
docker restart 容器id        #重启容器
docker stop 容器id           #停止当前运行的容器
docker kill 容器id           #强制停止当前容器
```

### 其他命令

日志查看

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)

常用：
docker logs -tf 容器id
docker logs --tail number 容器id #num为要显示的日志条数


#docker容器后台运行，必须要有一个前台的进程，否则会自动停止
#编写shell脚本循环执行，使得centos容器保持运行状态
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker run -d centos /bin/sh -c "while true;do echo hi;sleep 5;done"
c703b5b1911ff84d584390263a35707b6024816e1f46542b61918a6327a570dc
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
c703b5b1911f   centos    "/bin/sh -c 'while t…"   13 seconds ago   Up 10 seconds             pedantic_banach
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker logs -tf --tail 10 c703b5b1911f
2020-12-27T03:34:07.255599560Z hi
2020-12-27T03:34:12.257641517Z hi
2020-12-27T03:34:17.259706294Z hi
2020-12-27T03:34:22.261693707Z hi
2020-12-27T03:34:27.262609289Z hi
2020-12-27T03:34:32.267862677Z hi
2020-12-27T03:34:37.270382873Z hi
2020-12-27T03:34:42.272414182Z hi
2020-12-27T03:34:47.274823243Z hi
2020-12-27T03:34:52.277419274Z hi
```

查看容器中进程信息

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker top c703b5b1911f
```

查看容器的元数据

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker inspect 容器id
```

进入当前正在运行的容器

方法一:

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker exec -it c703b5b1911f /bin/bash
[root@c703b5b1911f /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@c703b5b1911f /]# ps -ef      
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 03:31 ?        00:00:00 /bin/sh -c while true;do echo hi;sleep 5;done
root       279     0  0 03:54 pts/0    00:00:00 /bin/bash
root       315     1  0 03:56 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 5
root       316   279  0 03:56 pts/0    00:00:00 ps -ef
```

方法二:

```shell
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker attach c703b5b1911f
```

docker exec 进入容器后开启一个新的终端，可以在里面操作

docker attach 进入容器正在执行的终端，不会启动新的进程

拷贝操作

```shell
#拷贝容器的文件到主机中
docker cp 容器id:容器内路径  目的主机路径

#拷贝宿主机的文件到容器中
docker cp 目的主机路径 容器id:容器内路径
```

## 可视化平台安装

Portaniner是Docker的图形化管理工具，类似的工具还有Rancher(CI/CD再用)

下载运行Portaniner镜像并运行，设置本机映射端口为8088

```shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

## Docker镜像

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-7vrgLpCg-1628648977534)(Docker学习笔记(中)].assets/image-20210805151449536.png)](https://img-blog.csdnimg.cn/a7db9520ca41485180295d7259e4289a.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1lhbk1pbmdYaW4=,size_16,color_FFFFFF,t_70)

### 什么是镜像

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需要的所有内容，包括代码，运行时（一个程序在运行或者在被执行的依赖）库，环境变量和配置文件。

### 镜像加载原理

Docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统是UnionFS联合文件系统。

UnionFS (联合文件系统）:Union文件系统( UnionFS )是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtualfilesystem)。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性:一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs (root file system)，在bootfs之上。包含的就是典型Linux.系统中的/dev, /proc, /bin,/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu , Centos等等。

### 分层理解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210718123636415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5namhhaQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071812372978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5namhhaQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071812374035.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5namhhaQ==,size_16,color_FFFFFF,t_70)

### 提交镜像

```shell
使用docker commit 命令提交容器成为一个新的版本

docker commit -m=“提交的描述信息”  -a="作者" 容器id 目标镜像名:[TAG] 
```

```shell
[root@iZ1608aqb7ntn9Z ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED        SIZE
tomcat                latest    46cfbf1293b1   13 days ago    668MB
.....
[root@iZ1608aqb7ntn9Z ~]# docker commit --help

Usage:  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

Create a new image from a container's changes

Options:
  -a, --author string    Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -c, --change list      Apply Dockerfile instruction to the created image
  -m, --message string   Commit message
  -p, --pause            Pause container during commit (default true)
[root@iZ1608aqb7ntn9Z ~]# docker commit -m "Ymx tomcat" -a "ymx" 46cfbf1293b1 ymxtomcat:1.0
Error response from daemon: No such container: 46cfbf1293b1
[root@iZ1608aqb7ntn9Z ~]# docker commit -m "Ymx tomcat" -a "ymx" tomcat  ymxtomcat:1.0
sha256:ee3100b86b4939d52415da7a62c91d987d91be3ea4776f0ae3d2024b94fed6b4
[root@iZ1608aqb7ntn9Z ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
ymxtomcat             1.0       ee3100b86b49   5 seconds ago   668MB
tomcat                latest    46cfbf1293b1   13 days ago     668MB
......
```

## 容器数据卷

![在这里插入图片描述](https://img-blog.csdnimg.cn/ab60216eda9145be95fa90e015b648c7.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1lhbk1pbmdYaW4=,size_16,color_FFFFFF,t_70)

### 什么是容器数据卷

为了实现数据持久化，使容器之间可以共享数据。可以将容器内的目录，挂载到宿主机上或其他容器内，实现同步和共享的操作。即使将容器删除，挂载到本地的数据卷也不会丢失。

### 使用容器数据卷

```shell
docker run -it -v 主机内目录:容器内目录 镜像名/id
```

#### 直接命令挂载

```shell
docker run -it -v 主机内目录:容器内目录 镜像名/id
```

将容器内目录挂载到主机内目录上，通过**docker inspect [容器名或ID]**命令查看该容器即可以看到挂载信息：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5d6f0524203d4f1a8230116c439d3349.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1lhbk1pbmdYaW4=,size_16,color_FFFFFF,t_70)

```shell
# 挂载命令
[root@iZ1608aqb7ntn9Z 20210806]# docker run -it -v /opt/Docker/20210806/:/opt centos /bin/bash

# 进入到容器内部
[root@e749444d0ee1 /]# cd opt/
[root@e749444d0ee1 opt]# ls -l
total 0
-rw-r--r-- 1 root root 0 Aug  6 03:35 ymx

# 查看本机
[root@iZ1608aqb7ntn9Z ~]# cd /opt/Docker/20210806/
[root@iZ1608aqb7ntn9Z 20210806]# ll
总用量 0
-rw-r--r-- 1 root root 0 8月   6 11:35 ymx 

# docker inspect [容器名或ID] 查看挂载
[root@iZ1608aqb7ntn9Z 20210806]# docker inspect e749444d0ee1 
......
     "Mounts": [
            {
                "Type": "bind",
                "Source": "/opt/Docker/20210806",  # 对应主机的源目录
                "Destination": "/opt",    # 容器中的目录
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
......

```

#### 匿名挂载

```shell
docker run -d  -v 容器内目录  镜像名/id  # 匿名挂载
```

匿名挂载后，使用**docker volume ls**命令查看所有挂载的卷：

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-DB58Zok3-1628648977547)(Docker学习笔记(中)].assets/image-20210805151821167.png)](https://img-blog.csdnimg.cn/e5787faba84d4f86b3da82e314128f6f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1lhbk1pbmdYaW4=,size_16,color_FFFFFF,t_70)

每一个VOLUME NAME对应一个挂载的卷，由于挂载时未指定主机目录，因此无法直接找到目录。

#### 具名挂载

```shell
docker run -d  -v 卷名：容器内目录  镜像名/id  # 具名挂载
```

```shell
#如何区分具名,匿名挂载,还是指定路径挂载
-v 容器内路径  #匿名挂载
-v 卷名:容器内路径  #具名挂载
-v /宿主机路径:容器内路径   #指定路径挂载
```

拓展

```shell
#通过 -v 容器内路径:ro   rw改变读写权限
ro  readonly #只读
rw readwrite #可读可写
docker run -d -P --name nginx -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx -v juming-nginx:/etc/nginx:rw nginx
```

## 数据卷容器

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-vANcomBq-1628648977552)(Docker学习笔记(中)].assets/image-20210805151859799.png)](https://img-blog.csdnimg.cn/661dab49bd3a4cffb0f381169aff9fb4.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yX1lhbk1pbmdYaW4=,size_16,color_FFFFFF,t_70)

```shell
docker run -it --name container02 --volumes from container01 镜像名/id  # 将两个容器进行挂载
```

## DockerFile

### 构建步骤

1. 编写 一个dockerfile文件
2. docker builder构建成一个镜像
3. docker run 运行镜像
4. docker push 发布镜像(DockerHub  阿里云镜像仓库)

### DockerFile构建过程

#### 基础知识

1. 每个保留关键字(指令)都必须是大写字母
2. 执行从上到下顺序执行
3. #表示注释
4. 每一个指令都会创建一个新的镜像层,并提交

![image-20221001085508372](D:/图/3150/image-20221001085508372.png)

### DockerFile指令

```shell
FROM			# 基础镜像,一切从这里开始
MAINTAINER		# 镜像是谁写的,姓名+邮箱
RUN				# 镜像构建时需要运行的步骤
ADD				# tomcat镜像,tomcat压缩包 添加内容
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOSE			# 暴露的端口配置
CMD				# 指定容器启动的时候要运行的命令,只有最后一个会生效,可被替代
ENTRYPOINT		# 指定容器启动的时候要运行的命令,可以追加
ONBUILD     	# 当构建一个被继承DockerFile 这个时候就会运行ONBUILD指令.出发指令
COPY			# 类似ADD,将文件拷贝到镜像中
ENV				#构建的时候设置环境变量
```

![image-20221001085649009](D:/图/3150/image-20221001085649009.png)





> #mysql
>
> #拉取并运行容器
> docker run -d --name mysql-5.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7



> 修改root 可以通过任何客户端连接
>
> ```
> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
> ```