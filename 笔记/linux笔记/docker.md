**安装docker**

​	环境准备

1、linux

2、CentOS 7

3、Xshell连接远程服务器



环境查看

​	uname -r

[root@localhost script]# cat /etc/os-release 

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

1、卸载旧的版本

​	yum  remove docker \

​						docker-client \

​						docker-client-latest \

​						docker-common \

​						docker-latest  \

​						docker-latest-logrotate \

​						docker-logrotate \

​						docker-engine

2、需要的安装包

​	yum install -y yum-utils

3、设置镜像的仓库

### 	阿里云

​	yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

更新yum软件包  yum makecache fast

4、安装docker相关的源  docker-ce 社区版 ee 企业版

​	yum install docker-ce docker-ce-cli containerd.io

5、启动docker

​	systemctl start docker

6、使用docker version查看是否安装成功

7、docker run hello-world

8、查看一下hello-world镜像  docker images

9、卸载docker

​		yum remove docker-ce docker-ce-cli containerd.io

​		rm -rf /var/lib/docker

10.docker 开机启动docker服务 容器不会启动

​	systemctl enable docker 

阿里云镜像加速

sudo mkdir -p /etc/docker



sudo tee /etc/docker/daemon.json <<-'EOF'

{  "registry-mirrors": ["https://ss7dsimb.mirror.aliyuncs.com"]

}

EOF



sudo systemctl daemon-reload

sudo systemctl restart docker





hello-world流程

![img](https://i0.hdslb.com/bfs/note/9c5ebe9f5e0cab1bbdbda449609e9714600a1af9.png)

Docker  vs VM

1、docker 有着比虚拟机更少的抽象层

2、docker 利用的是宿主机的内核，VM需要Guest OS

所以，新建一个容器的时候，docker不需要重新加载一个操作系统内核，避免引导。虚拟机是分钟级别的，docker是秒级的



![img](https://i0.hdslb.com/bfs/note/7912cfa61a5d6d1ca4fa6691f6d43e45c0b4a6c2.png)

**Docker的常用命令**

**帮助命令**

​	docker  version  显示docker版本信息

​	docker info			显示docker的熊信息，包括镜像和容器数量

​	

​	docker   命令  --help  帮助命令

\----------------------------------------------------------------

**镜像命令**

**docker  images**  查看所有镜像

​			REPOSITORY 镜像的仓库源

​			TAG 				镜像的标签

​			IMAGE ID		镜像的id

​			CREATED		镜像的创建时间

​			SIZE				镜像的大小

可选项

​			-a   列出所有镜像

​			-q	  只显示镜像的id   docker镜像的id 不是运行后的容器id

**docker  search**   搜索命令

可选项

​			--filter=STARS=3000	搜索出来的镜像就是STARS大于3000的

**docker pull**	 镜像名[:tag]	下载镜像

不写tag，默认是latest   

**docker  rmi**  删除镜像

​	docker rmi  -f  $(docker images -aq)  删除全部镜像

​	docker rmi -f  镜像id 镜像id  。。。。删除多个镜像

\--------------------------------------------------------------------

**容器命令**

说明：有了镜像才可以创建容器，linux，下载一个镜像来测试学习 docker pull centos



**新建容器并启动**

**docker run [可选参数] image**

--name =“容器名字” 

-d        后台运行

-it		使用交互方式运行，进入容器查看内容

-p			指定容器的端口 -p 8080：:8080

​		-p  主机端口:容器端口

​		-p		容器端口

​		-p		主机ip：主机端口:容器端口

-P		随机指定端口

**启动并进入容器**

**docker run -it  centos  /bin/bash**



![img](https://i0.hdslb.com/bfs/note/c9a897fc06b21f7a03c3e21ab09671a138644706.png)

exit 退出容器

docker ps  运行的容器

docker ps -a  列出当前正在运行的容器，带出历史运行的容器

-n=？ 显示最近创建的容器

-q  只显示容器的编号

退出容器

exit  直接退出容器并停止

ctrl +p + q 容器不停止退出

删除容器

docker rm  容器id   删除指定容器 不能删除正在运行的容器

docker rm -f $(docker ps -aq)   删除所有容器 强制删除

docker ps -a -q | xargs  docker rm  删除所有的容器



启动和停止容器的操作

docker start 容器id   启动容器

docker restart 容器id	重启容器

docker  stop  容器id	停止运行的容器

docker  kill  容器id		强制停止当前的容器



**常用其他命令**

  docker run -d  镜像名       后台启动容器  ，启动后停止了，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止

docker没有提供服务就会停止

docker   logs   -tf  --tail =10  容器id     查看日志

docker   top  容器id        查看容器中的进程信息

docker   inspect  容器id   查看镜像的元数据

docker exec  -it 容器id  /bin/bash进入当前正在运行的容器 开启一个新的终端

docker attach  容器id  [/bin/bash]  正在执行当前的代码

docker  cp 容器id：容器内路径  目的主机目录   从容器拷贝文件到主机





![img](https://i0.hdslb.com/bfs/note/6dade2e2deeda50d1fbf5dce7c6e3808eeb27923.png)

**部署Nginx**  ：搜索镜像 docker search nginx

1、拉取镜像docker pull nginx

2、启动镜像  -d后台运行   --name  给容器命名   -p宿主端口：容器内端口

​	docker run -d  --name nginx01  -p  3344:80 nginx

3、测试 curl  localhost：3344

4、进入容器 docker  exec -it  nginx01  /bin/bash

**安装tomcat**

docker run -it --rm tomcat:9.0  用来测试用完即删

下载启动  docker pull tomcat:9.0

docker run -d  --name tomcat01  -p  3355:8080 tomcat

进入容器 docker  exec -it  nginx01  /bin/bash



发现问题 1、linux命令少  2、没有webapps，默认最小的镜像，所有不必要的都移除掉

可以在容器外部提供一个映射路径，webapps，在外部放置项目，就自动同步到内部

**部署es+kibana**



docker run --name elasticsearch  -p 9200:9200 -p 9300:9300  -e "discovery.type=single-node"  elasticsearch:7.6.2





docker  stats  查看服务器cpu的状态

增加内存限制

docker run --name elasticsearch  -p 9200:9200 -p 9300:9300  -e "discovery.type=single-node"  -e  ES_JAVA_OPTS="-Xms64 -Xmx512m" elasticsearch:7.6.2



**可视化**

portainer（先用这个）

​	Docker图形化界面管理工具！提供又一个后台面板供我们使用

docker run -d -p 8088:9000 \

--restart=always  -v /var/run/docker.sock:/var/run/docker/sock  --privileged=true  portainer/portainer

测试 ip：8088

设置密码

选择本地的



如何提交一个自己的镜像

**commit 镜像**

docker commit  提交容器成为一个新的副本

 docker commit -m="提交的描述信息"  -a=“作者”  容器id  目标镜像名 :[tag]



 **容器数据卷**

​	容器之家可以有一个数据共享的技术！docker 容器中产生的数据，同步到本地，这就试卷技术

​	目录的挂载，将我吗容器内的目录，挂载到linux上面

​		容器持久化和同步操作，容器间也可以数据共享

**使用数据卷**

方式一：直接使用命令-v

​	docker  run -it -v  主机目录:容器内目录 容器名

测试：docker run -it  -v /home/ceshi:/home centos  /bin/bash

启动后可以使用 docker inspect 容器id  查看



文件的同步是双向的



**安装mysql**

1、获取镜像 docker pull  mysql:5.7

2、运行 docker run -d -p  3306:3306 -v /home/mysql/conf:/etc/mysql/   -e MYSQL_ROOT_PASSWORD=123456  --name mysql mysql:5.7



具名挂载和匿名挂载

匿名挂载：-v  容器内路径

docker volume ls  查看所有卷的情况

使用--filter dangling=true 过滤 不在使用的数据卷

docker volume ls  --filter dangling=true



具名挂载：docker volume inspect juanming-nginx   查看具名卷

-v 卷名：容器内路径

docker run -d -p  --name  nginx02 -v juming:/etc/nginx:ro nginx

一旦这个设置了容器权限，容器对我们挂载出来的内容就有限定

ro 说明这个路径之内通过宿主机来操作，容器无法操作！

**初识Dockerfile**

Dockerfile 就是用来构建docker镜像的构建文件。命令脚本

通过脚本可以生成一个个镜像

docker build  -f  dockerfile路径  -t name：tag

创建dockerfile文件  格式 ：指令  参数

FROM  centos

VOLUME ["volume01","volume02"]

CMD echo  "-----------end----------"

CMD  /bin/bash

每个命令都是镜像的一层



**数据卷容器**

容器间数据同步

docker run -it --name docker02 --volumes-from docker01 kuangshen/centos:1.0



**Dockerfile**

dockerdile是用来构建docker镜像的文件！命令参数脚本

构建步骤：

1、编写一个dockerfile文件

2、docker  build 构建成为一个镜像

3、docker  run 运行镜像

4、docker push 发布镜像（DockerHub、阿里云镜像仓库）

Dockerfile构建过程

基础知识：

1、每个保留关键字都是必须是大写字母

2、执行从上到下顺序执行

3、#表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交！



![img](https://i0.hdslb.com/bfs/note/cc5e94b299d2396d3dd907f40bff8258ab2d9216.png)

dockerfile是面向开发的，以后要发布项目，做镜像，就需要编写dockerfile文件

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品

Docker容器：容器就是镜像运行起来提供服务的





![img](https://i0.hdslb.com/bfs/note/83a43be9fb565377f3f2f681c50820017f8b88cc.png)

DockerFile的指令

FROM   #基础镜像，一切从这里开始构建

MAINTAINER #镜像是谁写的

RUN　　　　＃镜像构建的时候需要运行的命令

ADD					＃步骤，tomcat镜像，这tomcat压缩包！添加内容

WORKDIR　　＃镜像的工作目录　

VOLUME			＃挂载的目录

EXPOSE			　＃暴露端口

CMD						＃指定这个容器启动的时候要运行的命令，只有最后一个回升小，可被替代

ENTRYPOINT	＃指定这个容器启动的时候需要运行的命令，可以追加命令

ONBUILD		＃当构建一个被继承DockerFile这个时候就会运行ONBUILD的指令

COPY			＃类似ADD，将我们文件拷贝到镜像中

ＥＮＶ			＃　构建的售后设置环境变量



**实战测试**

Docker Hub中９９％镜像都是从FROM scratch　　过来的，然后配置需要的软件和配置来进行构建的

**创建一个centos**

 **vim mydockerfile**

**F**ROM centos

MAINTAINER	liu

ENV MYPATH  /usr/local

WORKDIR	$MYPATH



RUN  yum -y install vim

RUN  yum -y install net-tools



EXPOSE  80



CMD echo $MYPATH

CMD echo "----------end-------------"

CMD  /bin/bash

2、构建镜像

docker  build -f mydockerfile -t  mycentos:0.1  .



docker history  列出镜像的变更历史



CMD和ENTRYPOINT

CMD测试

FORM centos

CMD ["ls","-a"]



CMD  命令会被替换

ENTRYPOINT会追加命令



**DockerFile制作Tomcat镜像**

1、准备镜像文件tomcat压缩包，jdk压缩包

touch readmin

2、编写dockerfile文件

vim dockerfile

FROM centos

MAINTAINER  liu



COPY readme.txt /usr/local/readme.txt

ADD   jdk   /usr/local/

ADD  tomcat /usr/local/



RUN yum -y install vim

ENV MYPATH /usr/local/

WORKDIR $MTPATH



ENV JAVA_HOME  /usr/local/jdk

ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar



ENV CATALINA_HOME /usr/local/tomcat

ENV	 CATALINA_BASH /usr/local/tomcat

ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin



EXPOSE 8080



CMD	/usr/local/tomcat/bin/startup.sh && tail -F /usr/local/apache-tomcat/bin/logs/catalina.out

_____________________________________

3、构建镜像

docker build -t diytomcat .

4、启动 

docker run -d  -p 9090:8080 --name  diytomcat1 -v  /home/linux/build/tomcat/test:/url/local/tomcat/webapps/test  -v /home/linux/tomcat/tomcatlogs/:/url/local/tomcat/logs diytomcat

5.访问测试

6.发布项目 



**发布镜像到Docker HUP**

1.地址，注册账号

2、确定账号可以登陆 

3、在服务器上提交自己的镜像

docker login -u  name  -p  密码

4、docker push  liu/镜像名:tag



docker tag  镜像id tomcat



**发布阿里云镜像**

1、登录阿里云

docker login --username =      registry.cn-beijing.aliyuncs.com

2、找到容器镜像服务

3、创建命名空间

4、创建镜像仓库

docker tag [Imageid]

docker push  imagename



**Docker网络**

**1、理解网络**

![img](https://i0.hdslb.com/bfs/note/d0903389dc5d9bd23c6af7b987ddf92f0bd560dd.png)

查看容器的内部网络地址  ip  addr 

容器启动后会得到一个 eth0@if262

linux 可以 ping通docker 容器内部



1、没启动一个docker容器就会给docker容器分配一个ip，安装docker后就会得到一个网卡docker0 桥接模式使用evth-pair技术

2、evth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连

经常 将evth-pair桥梁，链接各种虚拟网络设备

3、容器和容器之间也能ping通

![img](https://i0.hdslb.com/bfs/note/731dff700b0499af0ba21e8587d2307db7d9b24c.png)

docker使用linux的桥接

docker中的所有网络接口都是虚拟的

![img](https://i0.hdslb.com/bfs/note/0d8a0f16fe607e5b18ee59d1e09ea0cc07da1e8a.png)

**--link**

使用容器名ping

docker run -d -P --name  tomcat03 --link tomcat02 tomcat



docker exec  -it tomcat03 ping tomcat02

tomcat03在hosts配置中增加了tomcat02 的映射

docker0不支持容器名连接访问



**自定义网络**

查看所有的docker网络  docker network ls

网络模式

bridge：桥接docker默认，自己创建也使用

none：不配置网络

host：和宿主机共享网络

container：容器网络连通



docker network create  创建一个网络

直接启动的命令

docker run -d -p --name tomcat01  tomcat

docker run -d -p  --net bridge



创建一个网络

docker network create --driver  bridge  --subnet 192.168.0.0/16 --gateway  192.168.0.1 mynet

查看网络详情：docker network inspect  网络id



docker exec -it tomcat01 ping tomcat02

**网络连通**

docker network connect  网络名  容器名 容器连接到一个网络上

连通之后就会将容器方放到网络下，一个容器两个网络



1、redis 安装 redis版本过高 需升级gcc 建议低版本

2、yum install gcc-c++

3、进入解压后目录 make 安装

4、make install 确认

5、进入usr/local/bin路径（本地程序默认安装路径）复制 配置文件到此目录 方便启动

6、修改配置文件 daemonize yes（后台启动）注释 bind 127.0.0.1和 protected-mode 改为no 不受保护 可以远程无密码访问 否则需要设置密码 防火墙和安全组开放端口

7、启动服务redis-server + 配置文件 （以配置文件启动）



