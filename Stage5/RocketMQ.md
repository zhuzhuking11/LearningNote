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

![](G:\保存截图\Snipaste_2021-08-12_10-08-04.png)

