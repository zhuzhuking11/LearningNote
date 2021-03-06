[TOC]



# 理论篇

## 基础篇

#### 为什么需要分布式

1. 增大系统容量。当业务量越来越大时，一台机器容量已经无法满足了，我们就需要多台机器。所以，我们需要垂直或是水平拆分业务系统，让其变成一个分布式的架构.
2. 加强系统可用。当所有业务都部署在一台机器上的时候，一旦机器出故障就会导致整体不可用。所以，需要通过分布式架构来冗余系统以消除单点故障，从而提高系统的可用性.

#### 分布式架构有什么优势？

1. 实现更大数据量的存储。
2. 可以提高系统的高可用性。
3. 提高系统的可重用性。
4. 可更好提高系统的性能。

#### 分布式架构有什么劣势？

采用任何技术方案都是“按下葫芦浮起瓢”，都是有得有失，分布式架构也有劣势，具体如下：

1. 会增加架构设计的难度。
2. 部署和维护的成本也会加大。 分布式系统架构的难点在于系统设计，以及管理和运维。所以，分布式架构解决了“单 点”和“性能容量”的问题，但却新增了一堆问题。

## 进阶篇

#### 分布式架构设计有什么难点？

1. 异构系统的不标准问题？（软件、协议、格式、运维的不标准）
2. 系统架构中的服务依赖问题？（多米诺骨牌效性，一个服务宕机）
3. 故障发生的概率更大？（故障恢复时间、影响面）
4. 多层架构的运维复杂度更大？（基础层-硬件、平台层-中间件、应用层-业务、接入层-网关）

#### 分布式架构设计的目的？

1. 提高整体架构的吞吐量，服务更多的并发和流量。
2. 提高系统的稳定性，让系统的可用性更高。

#### 如何基于架构设计提高其性能？

1. 缓存系统。加入缓存系统，可以有效地提高系统的访问能力。从前端的浏览器，到网络，再到后端的服务，底层的数据库、文件系统、硬盘和CPU，全都有缓存，这是提高快速访问能力最有效的手段。
2. 负载均衡系统。负载均衡是做水平扩展的关键技术。其可以用多台机器来共同分担一部分流量请求。
3. 异步调用。异步系统主要通过消息队列来对请求做排队处理，这样可以把前端的请求的峰值给“削平”了，而后端通过自己能够处理的速度来处理请求。
4. 数据分区和数据镜像。数据分区是把数据按一定的方式分成多个区（比如通过地理位置），不同的数据区来分担不同区的流量。

#### 如何基于架构提高系统的稳定性？

1. 服务拆分，主要有两个目的：一是为了隔离故障，二是为了重用服务模块。
2. 服务冗余，可以去除单点故障，并可以支持服务的弹性伸缩，以及故障迁移。
3. 限流降级，当系统实在扛不住压力时，只能通过限流或者功能降级的方式来停掉一部分服务，或是拒绝一部分用户，以确保整个架构不会挂掉。
4. 高可用架构，从冗余架构的角度来保障可用性。比如，灾备多活，或是数据可以在其中复制保持一致性的集群。
5. 高可用运维，指的是DevOps中的CI（持续集成）/CD（持续部署）。一个良好的运维应做了足够的自动化测试，做了相应的灰度发布，以及对线上系统的自动化控制。这样，可以做到“计划内”或是“非计划内”的宕机事件的时长最短。

#### 分布式架构有哪些关键技术？

1. 服务治理。服务治理的最大意义是需要把服务间的依赖关系、服务调用链，以及关键的服务给梳理出来，并对这些服务进行性能和可用性方面的管理。
2. 架构管理。基于服务所形成的架构需要有架构版本管理、整体架构的生命周期管理，以及对服务的编排、聚合、事务处理等服务调度功能。
3. DevOps。分布式系统可以更为快速地更新服务，但是对于服务的测试和部署都会是挑战。所以，还需要DevOps的全流程，其中包括环境构建、持续集成、持续部署等。
   自动化运维。有了DevOps后，我们就可以对服务进行自动伸缩、故障迁移、配置管理、状态管理等 一系列的自动化运维技术了。
4. 资源调度管理。应用层的自动化运维需要基础层的调度支持，也就是云计算IaaS层的计算、存储、 网络等资源调度、隔离和管理。
5. 整体架构监控。如果没有一个好的监控系统，那么自动化运维和资源调度管理只可能成为一个泡影， 因为监控系统是你的眼睛。没有眼睛，没有数据，就无法进行高效的运维。所以说，监控是非常重要
   的部分。这里的监控需要对三层系统（应用层、中间件层、基础层）进行监控。
6. 流量控制。最后是我们的流量控制，负载均衡、服务路由、熔断、降级、限流等和流量相关的调度都 会在这里，包括灰度发布之类的功能也在这里。

#### 能说说你对CAP的理解吗？

1. 分布式系统设计中的CAP指的是什么：
    - C:一致性(Consistency)，每次访问都能获得最新数据但可能会出现错误的响应。
    - A:可用性(Availability),每次访问都能收到非错响应，但不能保证获取最新数据。
    - P:分区容错性(Partition Tolerance),在任意分区网络故障的情况下系统仍能继续运行。
2. 分布式系统中的CAP设计不可能三者都满足。
   **在一个分布式系统中，首先网络是不可靠的，我们的系统就需要支持分区容错，然后需要在软件可用性和一致性之间做出取舍，也就是鱼和熊掌不能兼得。**
    - CP：一致性和分区容错性，等待分区节点的响应可能会导致延时错误。如果你的业务需求需要原子读写，CP 是一个不错的选择。
    - AP：可用性与分区容错性，响应节点上可用数据的最近版本可能并不是最新的。当分区解析完后，写入（操作）可能需要一些时间来传播。如果业务需求允许最终一致性，或当有外部故障时要求系统继续运行，AP 是一个不错的选择。

# Linux操作系统

## 基础篇

#### 说说你对Linux系统的认识？

Linux最大的特点是源码开放,与Windows相比，它的优点在于：

1. 网络的支持很好，在网络服务中运行更稳定。很多在Windows组网中出现的问题，Linux里完全没有。

2. 算机硬件的要求比Windows要低，能出色的高效率的完成大型程序。

3. 源码开放，用户可以根据自己的需要来修改或改进操作系统，而且这完全是合法的，当然这需要一定的编程能力。

4. 编程操作的有利环境，系统本身就能编程。

5. 序运行的稳定性很好，它的稳定性甚至超过了WindowsXP，而且性能更出色。同样的硬件配置，装有Linux系统的计算机要比装Windows的计算机运行相同程序的效率要高出至少15％

6. 安全性较Windows高，一旦有漏洞可以马上补上，而且没有盗版的分别，虽然源码开放，但是好像没有听说过专门针对Linux而写的病毒。呵呵，看来那些病毒制造者觉得还是挑战别人都不知道的比较刺激。

而缺点在于：

1. 专门为Linux写的程序没有Windows的多，有些工具需要专门使用Linux版的。

2. 程序的安装比较麻烦，需要一定的编程能力(不过不高，看说明就会了)

3. 很多设置和功能也需要用命令来完成，虽然Windows也有命令，但是Linux需要的操作更多。

#### Linux系统启动，重启、退出的常用指令？

```shell
shutdown [option] [time] [message]
shutdown -h +9 "服务器正在关闭，请保存的工作并注销。"
shutdown -c 取消关机
reboot 重启
kill PID
kill -9 PID //强制杀死
kill -9 %工作号
pkill -9 '进程名' //全杀
```

#### 说几个Linux系统下常用的文件操作？

```sh
cd <目录名> 进入某个目录
cd .. 返回上级菜单
cd ../.. 返回上两级目录
cd 进入个人主目录
pwd 显示当前路径
ls 查看文件目录列表
mkdir <目录名> 创建目录
rm -f file1 删除'file1'文件
rmdir dir1 删除'dir1'目录
cat file1 查看文件内容
```

#### Linux系统中的防火墙操作有哪些？

```sh
systemctl start firewalld 启动
systemctl status firewalld 查看状态
systemctl disable firewalld 停止
systemctl stop firewalld 禁用
systemctl start firewalld.service 启动服务
systemctl stop firewalld.service 关闭服务
systemctl restart firewalld.service 重启服务
systemctl status firewalld.service 显示服务状态
systemctl enable firewalld.service 开机启用服务
systemctl disable firewalld.service 开机禁用服务
systemctl is-enabled firewalld.service 查看服务是否开机启动
firewall-cmd --zone=public --list-ports 查看所有打开的端口
firewall-cmd --reload 更新防火墙规则
firewall-cmd --zone=public --add-port=80/tcp --permanent 添加端口
firewall-cmd --zone= public --query-port=80/tcp 查看端口
firewall-cmd --zone= public --remove-port=80/tcp --permanent 删除端口 
```

#### Linux 系统的中的进程操作指令你了解哪些？

```sh
ps -ef 查看所有进程
ps -ef|grep <进程名> 过滤你需要的进程
kill -s name kill指定名称的进程
kill -s pid kill指定pid的进程
top 实时显示进程状态
```

#### Linux 系统中的你是如何查找文件的？

```sh
find / -name file1 从根目录开始搜索文件/目录
find / -user user1 搜索用户user的文件夹/目录
find /dir -name *.bin 在目录/dir中搜索带有*.bin的文件
locate <关键词> 快速定位文件
locate *.mp4 寻找mp4结尾的文件
whereis <关键词> 显示某二进制文件/可执行文件的路径
which <关键词> 查找系统目录下的二进制文件
```

#### Linux 系统中的浏览文件的内容的指令有哪些？

```sh
vi/vim
i 切换到输入模式，以输入字符。
x 删除当前光标所在处的字符。
: 切换到底线命令模式，以在最底一行输入命令。
:q!	若曾修改过档案，又不想储存，使用 ! 为强制离开不储存档案。
:wq	储存后离开，若为 :wq! 则为强制储存后离开 (常用)
```

#### Linux 系统中文件的打包和压缩、解压缩是如何实现的？

```
zip xxx.zip file 压缩至zip包
zip -r xxx.zip file1 file2 dir1 将多个文件,文件夹打成压缩包
unzip xxx.zip 解压zip包
tar -cvfz xxx.tar.gz dir 创建gzip压缩包
tar -zxvf xxx.tar.gz 解压gzip压缩包
```

#### 你是如何理解vi和vim的？

Vim是从 vi 发展出来的一个文本编辑器。代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。

简单的来说， vi 是老式的字处理器，不过功能已经很齐全了，但是还是有可以进步的地方。 vim 则可以说是程序开发者的一项很好用的工具。

连 vim 的官方网站 ([http://www.vim.org](http://www.vim.org/)) 自己也说 vim 是一个程序开发工具而不是文字处理软件。

#### vim下有几种工作模式？

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode）**，**输入模式（Insert mode）**和**底线命令模式（Last line mode）**

## 进阶篇

#### Linux平台下的网络配置指令常用的有哪些？

```sh
ifconfig 查看网络接口属性
ifconfig etho 查看某网卡的配置
route -n 查看路由列表
netstat -lntp 查看所有监听端口
ifup etho 启用etho网络设备

```

#### Linux平台下的用户和用户组操作？

```sh
useradd <用户名> 添加用户
userdel -r 删除用户
usermod -g group_name user_name 修改用户的组
usermod -aG group_name user_name 将用户添加到组
groupadd group_name 添加组
groupdel group_name 删除组
passwd 修改口令
passwd 修改某用户的口令
```

#### 你了解linux平台下的shell编程吗？

Shell是命令解释器(command interpreter)，是Unix操作系统的用户接口，程序从用户接口得到输入信息，shell将用户程序及其输入翻译成操作系统内核（kernel）能够识别的指令，并且操作系统内核执行完将返回的输出通过shell再呈现给用户，下图所示用户、shell和操作系统的关系：

![img](https://images2018.cnblogs.com/blog/1478220/201809/1478220-20180909162448693-2019827189.png)

 

　　Shell也是一门编程语言，即shell脚本，shell是解释执行的脚本语言，可直接调用linux命令。 .java -> .class

　　一个系统可以存在多个shell，可以通过cat /etc/shells命令查看系统中安装的shell，不同的shell可能支持的命令语法是不相同的。

#### Linux平台下你自己安装过哪些软件？

mysql,docker,redis,jdk,eureka,nacos.....

# docker虚拟引擎

## 基础篇

#### Docker是什么？

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

#### 为什么要选择Docker？

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

#### 如何理解Docker中的镜像和容器？

##### 镜像（Image）

Docker 镜像是一个特殊的文件系统（https://hub.docker.com/），除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。例如JDK镜像、Centos镜像、MySQL镜像等，可以暂时先将其理解为一个安装程序。

##### 容器（Container）

Docker容器可以将其理解为一个运行镜像的载体，镜像（Image）和容器（Container）的关系，就像是光盘和光驱。容器基于镜像创建、启动，然后运行镜像的中的文件。

#### Docker在线安装的基本步骤是怎样的？

1. 下载离线安装包

   ```sh
   https://download.docker.com/linux/static/stable/x86_64/docker-20.10.6.tgz
   ```

2. 下载离线安装工具

   ```shell
   https://github.com/Jrohy/docker-install/
   ```

3. 创建安装目录

   ```shell
   [root@centos7964 docker]# pwd
   /root/setup/docker
   [root@centos7964 docker]# ls -l
   总用量 68132
   -rw-r--r--. 1 root root 69637582 8月   4 13:04 docker-20.10.6.tgz
   -rw-r--r--. 1 root root   114793 8月   4 13:04 docker.bash
   -rwxr-xr-x. 1 root root     7546 8月   4 13:04 install.sh
   [root@centos7964 docker]#
   ```

4. 执行安装操作

   ```shell
   # 进入/root/setup/docker 文件夹
   cd /root/setup/docker
   # 为 install.sh添加执行权限
   chmod +x install.sh
   # 安装
   ./install.sh -f docker-20.10.6.tgz
   ```

5. 检查安装状态

   ```shell
   docker info
   ```

#### Docker的安装方式是怎样的？

1. 安装linux虚拟机

2. 安装yum-util工具

   ```sh
   sudo yum install -y yum-utils 
   ```

3. 设置yum仓库

   ```sh
   sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

4. 更新yum缓存

   ```sh
   sudo yum makecache fast #yum 是包管理器
   ```

5. 安装docker

   ```
   sudo yum install -y docker-ce docker-ce-cli containerd.io
   ```

#### Docker支持的常用基本操作有哪些？

```shell
 systemctl start docker #启动docker
 systemctl status docker #查看docker状态
 systemctl enable docker #设置开机自启
 systemctl disable docker #禁止开机自启
 systemctl stop docker #停止docker服务
 systemctl restart docker #重启docker
 docker info #docker信息
 docker info | grep 'Docker Root Dir:' #查看docker info中具体key的信息
```

修改配置文件 /etc/docker/daemon.json

```shell
cat <<EOF > /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com"
  ],
  "max-concurrent-downloads": 10,
  "log-driver": "json-file",
  "log-level": "warn",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
    },
  "data-root": "/var/lib/docker"
}
EOF
```

## 进阶篇

#### Docker进行数据管理的方式有哪些？

在容器中管理数据主要有两种方式：

- 数据卷（Volumes）
- 挂载主机目录 (Bind mounts)

#### Docker的数据卷你是如何理解的？

数据卷是一个可供一个或多个容器使用的特殊目录，可以在容器之间共享和重用，默认会一直存在，即使容器被删除。

#### Docker镜像如何制作？

例如构建一个 tomcat 10 镜像流程，就像在一台电脑上安装配置 tomcat 环境一样：

1. 选择基础镜像 centos:8（相当于一台新电脑，只有操作系统）
2. 添加 jdk 和 tomcat 文件
3. 设置环境变量
4. 设置开机启动 tomcat

编辑 `Dockerfile` 文件

```shell
cd /root/tomcat

vim Dockerfile
```

在文件中添加以下内容：

```shell
# 选择基础镜像
FROM centos:8

# jdk 和 tomcat 文件添加到镜像的 /usr/local/ 目录下
# ADD 指令会自动解压文件
ADD jdk-8u291-linux-x64.tar.gz apache-tomcat-10.0.6.tar.gz /usr/local/

# 切换到镜像中指定的文件夹下
WORKDIR /usr/local/apache-tomcat-10.0.6/

# 设置环境变量
ENV JAVA_HOME=/usr/local/jdk1.8.0_291 \
    CATALINA_HOME=/usr/local/apache-tomcat-10.0.6 \
    PATH=/usr/local/jdk1.8.0_291/bin:/usr/local/apache-tomcat-10.0.6/bin:$PATH

# EXPOSE 8080 只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务
# 这个声明有两个好处：
#   1.帮助镜像使用者理解这个镜像服务的端口，以方便配置映射
#   2.在运行时使用随机端口映射时，也就是 docker run -P时，会自动随机映射 EXPOSE 的端口
EXPOSE 8080

# 设置启动命令
CMD ["catalina.sh", "run"]
```

执行构建

```shell
cd /root/tomcat

# 使用当前文件夹中的 Dockerfile 文件进行构建
# 新构建的镜像命名为 tomcat:10
docker build -t tomcat:10 ./
```

#### Docker下如何实现容器互联？

Docker 中容器与容器之间进行通讯的解决方案一般有两种：
第一种：两个容器通过宿主机进行通讯（容器中的端口会映射到宿主机上）
第二种：两个容器之间直接通过虚拟网络进行连接,但是需要我们在docker中创建网络。

# Redis
## 基础篇
#### Redis 是什么？（key/value数据库）
#### Redis 现在最新版本是什么？（6.22）
#### Redis 的版本如何进行查看？（redis-server -v,redis-cli -v）
#### Redis 默认有多少个数据库？（16个）
#### Redis 的核心配置文件是哪个？（redis.conf）
#### Redis 的常用配置参数有哪些？（适当说几个）
#### Redis 支持的数据类型有哪些？(String,Hash，List,Set,Stream等)
#### Redis 支持的数据基本操作有哪些？（Set,Get,Del等）
#### Redis 支持的字符串操作有哪些？
incr/incrby,decr/decrby,append,strlen,mset/mget
#### Redis 中的key的有效时长如何设置（expire,pexpire）
#### Redis 支持的hash操作有哪些？
hset/hget,hincrby,hmset/hmget,hexists,hdel,hlen,hkeys/hvals
#### Redis 持久化数据的方式有哪些？（AOF和RDB）
#### Redis 是如何解决哈希冲突的？（同一个哈希桶中的多个元素用同一个链表来保存）
#### Redis 中的refresh操作用于解决什么问题？（哈希桶中的链表长度越大性能越低）
#### Redis 中的单线程模型如何理解？（Redis网络 IO 和键值对读写由一个线程完成）
#### Redis 在你项目中是通过什么API访问的？
## 进阶篇
#### Redis为什么要设计单线程模型？
1. 单线程减少了cpu资源的争用，避免了上下文的切换。
2. 基于内存读写，单线程读写耗时更少。
#### Redis在单线程模式下为什么还那么快？
1. 纯内存操作
2. 核心是基于非阻塞的IO多路复用机制
3. 单线程反而避免了多线程的频繁上下文切换问题
#### Reids 持久化的意义？
redis持久化的意义，在于故障恢复，数据恢复，也可以归类到高可用的一个环节里面去。比如你redis整个挂了，然后redis就不可用了，你要做的事情是让redis变得可用，尽快变得可用。如果redis重启了，没有持久化的话，redis就会丢失所有的数据，如果通过持久化将数据搞一份儿在磁盘上去，然后定期比如说同步和备份到一些云存储服务上去，那么就可以保证数据不丢失全部，还是可以恢复一部分数据回来的。
#### 如何理解redis中RDB方式的持久化？
RDB 记录的是某一时刻的数据，而且是全量快照。RDB方式会对redis中的数据执行周期性的持久化或手动save操作实现持久化。
#### Redis中的RDB方式持久化中的save和bgsave有什么不同？
Redis 提供了两个命令来生成 RDB 文件，分别是 save 和 bgsave。其中，SAVE保存是阻塞主线程，客户端无法连接redis，等SAVE完成后，主线程才开始工作，客户端可以连接。bgsave会由主线程fork一个save操作的子进程，bgsave 子进程是由主线程  fork 生成的，可以共享主线程的所有内存数据。在执行save过程中，不影响主进程，客户端可以正常链接redis，等子进程fork执行save完成后，通知主进程，子进程关闭。
#### Redis中RDB方式持久化的优点？
1. RDB会生成多个数据文件，每个数据文件都代表了某一个时刻中redis的数据，这种多个数据文件的方式，非常适合做冷备，可以将这种完整的数据文件发送到一些远程的安全存储上去，比如说Amazon的S3云服务上去，在国内可以是阿里云的ODPS分布式存储上，以预定好的备份策略来定期备份redis中的数据
2. RDB对redis对外提供的读写服务，影响非常小，可以让redis保持高性能，因为redis主进程只需要fork一个子进程，让子进程执行磁盘IO操作来进行RDB持久化即可。
3. 相对于AOF持久化机制来说，直接基于RDB数据文件来重启和恢复redis进程，更加快速
#### Redis中RDB方式持久化的缺点？
1. 如果想要在redis故障时，尽可能少的丢失数据，那么RDB没有AOF好。一般来说，RDB数据快照文件，都是每隔5分钟，或者更长时间生成一次，这个时候就得接受一旦redis进程宕机，那么会丢失最近5分钟的数据
2. RDB每次在fork子进程来执行RDB快照数据文件生成的时候，如果数据文件特别大，可能会导致对客户端提供的服务暂停数毫秒，甚至是数秒。
#### 如何理解Redis中的“写后”日志？
AOF 日志正好相反，是写后日志：“写后”的意思是 Redis 是先执行命令，把数据写入内存，然后才记录日志。传统数据库的日志，例如 redo log（重做日志），记录的是修改后的数据，而 AOF 里记录的是 Redis 收到的每一条命令，这些命令是以文本形式保存的。
#### Redis中AOF方式的写后日志有什么优点？
为了避免额外的检查开销，Redis 在向 AOF 里面记录日志的时候，并不会先去对这些命令进行语法检查。所以，如果先记日志再执行命令的话，日志中就有可能记录了错误的命令，Redis 在使用日志恢复数据时，就可能会出错。而写后日志这种方式，就是先让系统执行命令，只有命令能执行成功，才会被记录到日志中，否则，系统就会直接向客户端报错。同时也不会阻塞当前的写操作。
#### Redis中AOF方式的写后日志有什么风险？
如果刚执行完一个命令，还没有来得及记日志就宕机了，那么这个命令和相应的数据就有丢失的风险。如果此时 Redis 是用作缓存，还可以从后端数据库重新读入数据进行恢复，但是，如果 Redis 是直接用作数据库的话，此时，因为命令没有记入日志，所以就无法用日志进行恢复了。
AOF 虽然避免了对当前命令的阻塞，但可能会给下一个操作带来阻塞风险。这是因为，AOF 日志也是在主线程中执行的，如果在把日志文件写入磁盘时，磁盘写压力大，就会导致写盘很慢，进而导致后续的操作也无法执行了。
#### Redis中AOF方式的写后日志有什么策略？
1. Always：同步写，可靠性高数据基本不丢，但每个写命令都要落盘，性能较差
2. Everysec:每秒写，性能适中，宕机时可能回丢失1秒的数据
3. No: 操作系统控制的的写，性能好，当即时丢失数据较多。
#### Redis中AOF方式持久化的优点？
1. AOF可以更好的保护数据不丢失，一般AOF会每隔1秒，通过一个后台线程执行一次fsync操作，最多丢失1秒钟的数据
2. AOF日志文件以append-only模式写入，所以没有任何磁盘寻址的开销，写入性能非常高，而且文件不容易破损，即使文件尾部破损，也很容易修复
3. AOF日志文件即使过大的时候，出现后台重写操作，也不会影响客户端的读写。因为在rewrite log的时候，会对其中的指导进行压缩，创建出一份需要恢复数据的最小日志出来。再创建新日志文件的时候，老的日志文件还是照常写入。当新的merge后的日志文件ready的时候，再交换新老日志文件即可。
4. AOF日志文件的命令通过非常可读的方式进行记录，这个特性非常适合做灾难性的误删除的紧急恢复。比如某人不小心用flushall命令清空了所有数据，只要这个时候后台rewrite还没有发生，那么就可以立即拷贝AOF文件，将最后一条flushall命令给删了，然后再将该AOF文件放回去，就可以通过恢复机制，自动恢复所有数据
#### Redis中AOF方式持久化的缺点？
1. 对于同一份数据来说，AOF日志文件通常比RDB数据快照文件更大
2. AOF开启后，支持的写QPS会比RDB支持的写QPS低，因为AOF一般会配置成每秒fsync一次日志文件，当然，每秒一次fsync，性能也还是很高的
3. 以前AOF发生过bug，就是通过AOF记录的日志，进行数据恢复的时候，没有恢复一模一样的数据出来。所以说，类似AOF这种较为复杂的基于命令日志/merge/回放的方式，比基于RDB每次持久化一份完整的数据快照文件的方式，更加脆弱一些，容易有bug。不过AOF就是为了避免rewrite过程导致的bug，因此每次rewrite并不是基于旧的指令日志进行merge的，而是基于当时内存中的数据进行指令的重新构建，这样健壮性会好很多。
#### Redis 中的持久化机制该如何选择？
1. 数据不能丢失时，内存快照和 AOF 的混合使用是一个很好的选择；
2. 如果允许分钟级别的数据丢失，可以只使用 RDB；
3. 如果只用 AOF，优先使用 everysec 的配置选项，因为它在可靠性和性能之间取了一个平衡。
#### Redis 底层设计上都支持哪些数据结构？
Redis 为了平衡空间和时间效率，针对 value 的具体类型在底层会采用不同的数据结构来实现，有简单动态数组SDS、链表、字典、跳跃链表、整数集合、压缩列表、对象等，其中哈希表和压缩列表是复用比较多的数据结构，如图所示：
#### Redis中分片的应用场景和原理？
Redis中的分片思想就是把鸡蛋放到不同的篮子中进行存储。因为一个redis服务的存储能力是有限。分片就是实现redis扩容的一种有效方案。
#### Redis中的多路复用机制是如何实现的？
Linux 中的 IO 多路复用机制是指一个线程处理多个 IO 流，就是我们经常听到的  select/epoll 机制
#### Redis中的哨兵机制你是如何理解的？
哨兵其实就是一个运行在特殊模式下的 Redis 进程，主从库实例运行的同时，它也在运行。哨兵主要负责的就是三个任务：监控、选主（选择主库）和通知。
#### Redis的高性能如何保证？
线程模型，数据结构，持久化，网络模型
#### Redis的高可靠性如何保证？（持久化，主从复制，哨兵机制）
Redis 的高可靠性有两层含义：第一，数据尽量少丢失，通过 AOF 和 RDB 进行保证。第二，服务尽量少中断，通过增加副本冗余量保证，将一份数据同时保存在多个实例上。即使有一个实例出现了故障，需要过一段时间才能恢复，其他实例也可以对外提供服务，不会影响业务使用。
#### Redis的高可扩展性如何保证？
数据分片+负载均衡
#### Redis 要支持10万的QPS该如何玩？
单机的redis几乎不太可能说QPS超过10万+，除非一些特殊情况，比如你的机器性能特别好，配置特别高，物理机，维护做的特别好，而且你的整体的操作不是太复杂，一般的单机也就在几万。
真正实现redis的高并发，需要读写分离，对缓存，一般都是用来支撑读高并发的，写的请求是比较少的，可能写请求也就一秒钟几千，一两千。大量的请求都是读，一秒钟二十万次读。所以redis的高并发可以这样玩：主从架构 -> 读写分离 -> 支撑10万+读QPS的架构
# Nginx反向代理
## 基础篇

#### 如何理解正向和反向代理？
正向代理服务器一般都是客户端代理，代理客户端执行业务，反向代理服务器一般都是服务器端代理.用户无需关心真实的服务器是谁.
#### Nginx是什么？
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强。
#### Nginx主要用于解决什么问题？
反向代理、请求的转发、负载均衡等。
#### 你现在用的Nginx是什么版本？
#### Nginx 的基础架构是怎样的？
nginx 是由一个 master 管理进程，多个 worker 进程（处理工作）。基础架构设计如图所示：


其中，master 负责管理 worker 进程，worker 进程负责处理网络事件。整个框架被设计为一种依赖事件驱动、异步、非阻塞的模式。
#### Nginx 的进程管理模型有什么优点？
1. 可以充分利用多核机器，增强并发处理能力。
2. 多 worker 间可以实现负载均衡。
3. Master 监控并统一管理 worker 行为。在 worker 异常后，可以主动拉起  worker 进程，从而提升了系统的可靠性。并且由 Master 进程控制服务运行中的程序升级、配置项修改等操作，从而增强了整体的动态可扩展与热更的能力。
#### Nginx的核心配置文件是哪个文件？
Nginx.conf
#### Docker平台下Nginx配置的结构是怎样的？
嵌套结构，在nginx.conf文件内部嵌套了default.conf文件，我们所需要做的大部分修改，直接在default.conf文件中进行即可。

#### 说几个Nginx配置文件中关键元素？

server、upstream、。。。

## 进阶篇

#### Nginx的负载均衡策略有哪些？

轮询、轮询+权重、iphash

#### Nginx代理的服务高可用性是如何实现的？

当服务器宕机时,如果访问的失败达到最大失败次数,则标识为down.自动完成.在一定的周期之内,如果服务器恢复正常,则还会尝试访问故障机.例如设置max_fails=1 最大的失败次数 ，fail_timeout=60s; 设定周期为60秒。宕机后60秒还可尝试恢复访问。

#### Nginx为什么不采用多线程模型管理连接？

1. 采用独立的进程，可以让互相之间不会影响。一个进程异常崩溃，其他进程的服务不会中断，提升了架构的可靠性。  
2. 进程之间不共享资源，不需要加锁，所以省掉了锁带来的开销。

#### Nginx为什么不采用多线程处理逻辑业务？

1. 进程数已经等于核心数，再新建线程处理任务，只会抢占现有进程，增加切换代价。
2. 作为接入层，基本上都是数据转发业务，网络 IO 任务的等待耗时部分，已经被处理为非阻塞/全异步/事件驱动模式，在没有更多 CPU 的情况下，再利用多线程处理，意义不大。并且如果进程中有阻塞的处理逻辑，应该由各个业务进行解决，比如 openResty 中利用了 Lua 协程，对阻塞业务进行了优化。

