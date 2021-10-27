## VMware

- VMware 版本： 16+


- NAT网络网段使用 64 网段
    - 编辑 -- 虚拟网络编辑器 -- 选择 vmnet8 -- 左下角修改：`192.168.64.0`


- 虚拟机镜像
    - 课前资料\虚拟机\
        - centos-7-1908.zip
        - centos-8-2105.zip
    - 已经做了几步基础设置：
        - yum源和扩展源，使用阿里镜像
        - 安装了工具： python、pip、ansible
        - 方便设置ip地址的脚本：
            - ip-static 设置固定ip
            - ip-dhcp 自动获取ip

- 加载虚拟机
    1. 解压缩 centos-8-2105.zip
    2. 双击 centos-8-2105.vmx 加载镜像
    3. 启动虚拟机，按提示选择“已复制虚拟机”
    4. 用户名密码都是 `root`

**网卡不可用**

```shell
# centos 7 禁用 NetworkManager 系统服务
systemctl stop NetworkManager
systemctl disable NetworkManager

# centos 8 开启 VMware 托管
nmcli n on
systemctl restart NetworkManager

# 还原 VMware 虚拟网络
# VMware 虚拟网络不稳定，经常出现故障

# 编辑 -- 虚拟网络编辑器 -- 左下角按钮“还原默认设置” -- 设置 VMnet8 的 64 网段

# 会删除所有虚拟网络，重新创建
```

## 离线安装 Docker

1. 上传离线文件到 /root/

    - \DevOps课前资料\docker\docker-install 文件夹

2. 参考笔记，从第三步开始安装

   `https://wanght.blog.csdn.net/article/details/117327543`

## 配置中心

**准备 Git 仓库**
**把 2,3,4 三个项目的配置文件，存放到git仓库**

1. 在 springcloud1 下新建文件夹： config
2. 把 2,3,4 三个项目的配置文件，复制到 config 目录
    - item-service-dev.yml
    - user-service-dev.yml
    - order-service-dev.yml
3. 添加 `override-none=true` 防止配置中心的配置，覆盖本地命令参数
4. （有仓库的同学不用重复创建）
    1. VCS -- create git repository 或 double shift，搜索 create git repository
    2. 选择 springcloud1 工程目录作为仓库目录
    3. commit 提交本地仓库，ctrl+k，右上角对勾按钮，double shift搜索commit
    4. 选择所有文件，填写提交信息，提交
5. （有仓库的同学不用重复创建） 在 gitee 点右上角加号新建仓库
    - 仓库名称： `springcloud1`
    - 设置开源项
6. ctrl+shift+k，右上角向上箭头按钮，double shift搜索push
7. push界面，点击 define remore 连接，填写远程仓库地址，完成推送

**搭建配置中心服务器**

1. 新建模块： sp09-config

2. 添加依赖

    - eureka client
    - config server

3. yml

    - 仓库的地址

    - 存放配置文件的文件夹路径

    - 之后测试如果有问题，果断换仓库

```shell
https://gitee.com/xyxyxyxyx/springcolud01

config
```

4. 启动类注解： `@EnalbleConfigServer`

**确认配置中心服务器是否正确**

1. http://eureka1:2001/  检查是否有 `config-server` 的注册信息
2. 访问配置中心的配置文件
    - http://localhost:6001/item-service/dev
    - http://localhost:6001/user-service/dev
    - http://localhost:6001/order-service/dev

**配置中心的客户端**

1. 把 2,3,4 的配置文件全部注释掉
2. 添加依赖： config client
3. 新建配置文件： `bootstrap.yml`
4. 添加配置：
    - eureka地址
    - 指定配置中心的服务id：`CONFIG-SERVER`
    - 指定下载的配置文件和 profile

**确认**

- 启动控制台中，要看到连接 6001 的日志

# Rabbitmq

消息队列、消息服务、消息中间件、Broker

- Rabbitmq
- Activemq
- Rocketmq 阿里
- Kafka
- Tubemq 腾讯

**搭建Rabbitmq服务器**

1. 克隆 docker-base： rabbitmq

2. 设置ip

```shell
./ip-static
ip: 192.168.64.140

ifconfig
```

3. 下载 rabbitmq 镜像

```shell
docker pull rabbitmq:management
或者从 code 下载 rabbit-image.gz
上传到服务器，然后执行镜像导入
docker load -i rabbit-image.gz
```

4. 启动rabbitmq容器

```shell
关闭防火墙
systemctl stop firewalld
systemctl disable firewalld
 
重启 docker 系统服务
systemctl restart docker

mkdir /etc/rabbitmq
vim /etc/rabbitmq/rabbitmq.conf

# 添加两行配置：
default_user = admin
default_pass = admin

docker run -d --name rabbit \
-p 5672:5672 \
-p 15672:15672 \
-v /etc/rabbitmq/rabbitmq.conf:/etc/rabbitmq/rabbitmq.conf \
-e RABBITMQ_CONFIG_FILE=/etc/rabbitmq/rabbitmq.conf \
rabbitmq:management

访问管理控制台 http://192.168.64.140:15672
用户名密码是 admin
```

# Bus 配置刷新

1. 修改 2,3,4,9 添加依赖

    - Bus
    - Rabbitmq
    - binder-rabbit

2. 09添加依赖： actuator

3. 09的yml配置：

    - 暴露 bus-refresh：

      `m.e.w.e.i=bus-refresh`

    - rabbitmq连接

4. 修改 2,3,4 的yml 配置，修改 config 目录的三个文件并提交

    - rabbitmq连接

**启动项目测试**

1. 启动5，耐心等待完全启动完成
2. 启动9，耐心等待完全启动完成
3. --------- http://eureka1:2001 注册表中存在 config-server
   http://localhost:6001/item-service/dev
   http://localhost:6001/user-service/dev
   http://localhost:6001/order-service/dev
4. 启动 2,3
5. 启动 4
6. --------- 查看 2,3,4 的控制台，要看到连接 6001
7. 启动6
8. --------- http://localhost:6001/actuator  这里面要看到 bus-refresh
9. 使用 postman 向 `http://localhost:6001/actuator/bus-refresh` 提交 post 请求
10. 观察 2,3,4 的控制台，要看到重新连接 6001 刷新配置的日志

**把刷新到的配置信息，重新注入到对象中**

`@RefreshScope`

在需要重新注入配置数据的对象上添加这个注解，刷新的配置才能重新注入到对象中；

如果加这个注解，即使刷新到新配置，也不会重新向对象注入

# 链路跟踪

- sleuth

  产生链路跟踪日志

- zipkin

  日志的可视化  
  **sleuth**

A --> B --> C --> D

A, U5HG4HG456UYU, U5HG4HG456UYU, true

B, U5HG4HG456UYU, O7IUYH45TY34Y45, true

C, U5HG4HG456UYU, U56YU4Y344U456, true

D, U5HG4HG456UYU, 34T345Y456U56U5, true

**修改 2,3,4,6 添加 sleuth 产生链路跟踪日志**

- 只需要添加 sleuth 依赖
- sleuth 是自动配置，不需要添加任何其他配置

**2,3,4,6 向rabbitmq 发送日志数据**

1. 添加 zipkin 客户端依赖
2. 在 06 添加 rabbitmq 依赖
3. yml 配置发送方式： `rabbit`
4. 修改 06的yml，添加 rabbitmq 连接配置

**起动zipkin服务器**

```shell
# 使用 140 服务器，没有 virtual host
java -jar zipkin-server-2.23.4-exec.jar --
zipkin.collector.rabbitmq.uri=amqp://admin:admin@192.168.64.140:5672

# 使用 140 服务器，有 virtual host
java -jar zipkin-server-2.23.4-exec.jar --zipkin.collector.rabbitmq.uri=amqp://admin:admin@192.168.64.140:5672/vh0

# 使用我的服务器，有 virtual host
java -jar zipkin-server-2.23.4-exec.jar --zipkin.collector.rabbitmq.uri=amqp://admin:admin@wht6.cn:5672/vh0
```

# 向注册中心注册时，选择正确网卡，注册ip地址

**选择正确网卡**

bootstrap.yml

```yml
spring:
  cloud:
    inetutils:
      ignored-interfaces: # 忽略的网卡
        - VM.*
      preferred-networks: # 要是用的网卡的网段
        - 192\.168\.0\..+
```

**注册ip地址，而不注册主机名**

application.yml

```yml
eureka:
  instance:
    prefer-ip-address: true # 使用ip进行注册
    instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port} # ip:服务id:端口
```