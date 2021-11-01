# RocketMQ

## RocketMQ可靠消息最终一致性事务

**导入项目**

1. seata-at无事务版本,解压到rockemq-dtx工程目录
2. 导入
   - 工程模块管理中,添加模块
   - double shift,搜索add Maven project
   - 拖拽pom.xml到idea

### **订单使用事务消息,异步调用账户**

#### **订单添加事务消息**

1. 父项目添加spring-Rocketmq依赖
2. yml配置rocketmq连接和生产者组名
3. 添加新的数据表 tx_table,用来存储事务执行状态
4. 添加实体类TxInfo和TxMapper
5. 新建AccountMessage,用来封装发给账户的调用信息: UserID,money,xid
6. 工具类,JsonUtil
7. OrderServiceImpl发送事务消息
8. 实现事务监听器

#### **请求流程**

1. 用户请求OrderController
2. 执行业务方法OrderService.create()
3. create()方法发送事务消息
4. 触发监听器的executeLocalTransaction()执行订单存储

如果网络不稳定,rocketmq服务器可能执行消息回查,

执行checkLocalTransaction()查询

#### **账户接收消息,扣减账户**

1. 依赖
2. yml配置name server
3. AccountMessage
4. JsonUtil
5. 新建消费者类: AccountConsumer,实现消费者接口
6. 通过注解配置接收消息
7. 扣减账户

## Topic基本原理

### 在Rocketmq集群中新建 Topic1

在管理界面中新建**主题**`Topic1`，为了方便观察测试效果，这里把**写队列**和**读队列**的数量都设置成3。

![topic1](https://img-blog.csdnimg.cn/20200711002829639.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

这样，在 broker-a 和 broker-b 上都创建了 Topic1 主题，并各创建了3写3读队列，共6写6读，如下图所示

![topic1](https://img-blog.csdnimg.cn/20200711002512809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

*你也可以修改Topic1分别配置 broker-a 和 borker-b 上的队列数量。*

### perm 参数的含义

`perm` 参数是设置队列的读写权限，下面表格列出了可配置的值及其含义：

| 取值 |     含义     |
| :--: | :----------: |
|  6   | 同时开启读写 |
|  4   |     禁写     |
|  2   |     禁读     |

### Topic 收发消息原理

![topic](https://img-blog.csdnimg.cn/20200711143824268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

生产者将消息发送到 Topic1 的其中一个**写队列**，消费者从对应的一个**读队列**接收消息。

### 生产者的负载均衡

![producer](https://img-blog.csdnimg.cn/20200711153533541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

生产者以**轮询**的方式向所有写队列发送消息，这些队列可能会分布在多个broker实例上。

### 消费者的负载均衡

![topic](https://img-blog.csdnimg.cn/20200713225310740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

一个 group 中的多个消费者，可以以负载均衡的方式来接收消息。

`读取队列`被均匀分配给这些消费者，它们从指定的队列来接收消息。队列的分配可以采用不同的策略，这里简略介绍以下三种策略：

#### AllocateMessageQueueAveragely 平均分配

这是默认策略，它是这样分配队列的：

![topic](https://img-blog.csdnimg.cn/20200713225310740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

#### AllocateMessageQueueAveragelyByCircle 环形分配

如果使用环形分配，在消费者的代码中需要设置分配策略，代码如下：

```java
consumer.setAllocateMessageQueueStrategy(new AllocateMessageQueueAveragelyByCircle());
```

这种分配策略的逻辑很简单，所有0号队列分给0号消费者，所有1号队列分给1号消费者，以此类推。

![topic](https://img-blog.csdnimg.cn/20200713225823999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

#### AllocateMessageQueueConsistentHash 一致性哈希

```java
consumer.setAllocateMessageQueueStrategy(new AllocateMessageQueueConsistentHash());
```

这种算法依靠一致性哈希算法，看当前消费者可以落到哪个虚拟节点，该虚拟节点对应哪个队列。

## NameServer 基本原理

![rocketmq](https://img-blog.csdnimg.cn/20200713233623258.png#pic_center)

NameServer 是 rocketmq 自己开发的一个轻型注册中心，他的作用相当于是 zk、eureka等。

rocketmq 为什么不使用 zk 呢？实际上 rocketmq 的早期版本使用的就是 zookeeper。

而 rocketmq 的架构设计决定了只需要一个轻量级的元数据服务器就足够了。杀鸡焉用牛刀？小区里，搞个货架就行了，建个仓库，又占地方，维护成本又高。

甚至，NameServer 都不需要有一个集群的管理者。以至于，NameServer 看起来都不像一个集群。事实上，NameServer 本质上来看，也不是一个集群。因为它的各个节点是独立的，不相关的。每个 NameServer 都是独立和 Producer、Consumer打交道。

### 基本认识

1. NameServer主要用于存储Topic，Broker关系信息，功能简单，稳定性高。
2. 各个NameServer节点之间不相关，不需要通信，单台宕机不影响其它节点。
3. NameServer集群整体宕机不影响已建立关系的Concumer，Producer，Broker。

### Broker、Producer、Consumer 与NameServer的通信

1. 每个Borker和所有NameServer保持长连接，心跳间隔为30秒。每次心跳时还会携带当前的Topic信息。当某个Broker两分钟之内没有心跳，则认为该Broker下线，并调整内存中与该Broker相关的Topic信息。

2. Consumer 从 NameServer 获得 Topic 的路由信息，与对应的 Broker 建立长连接。间隔30秒发送心跳至Broker。Broker检查若发现某 Consumer 两分钟内无心跳则认为该Consumer下线，并通知该Consumer所有的消费者集群中的其他实例，触发该消费者集群重新负载均衡。

3. Producer 与消费者一样，也是从 NameServer 获得 Topic 的路由信息，与对应的 Broker 建立长连接，30秒发送一次心跳。Broker 也会认为两分钟内没有心跳的 Producer 下线。
   

