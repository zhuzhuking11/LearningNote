[TOC]

# SpringCloud

#### Eureka自我保护机制是什么？

由于网络故障出现网络分区，15 分钟内 85% 服务器出现心跳异常，eureka会进入保护状态，在保护状态下，所有服务的注册信息都不会被删除

#### 什么是hystrix降级？

当一个服务调用后台服务失败，无法获得后台服务的结果时，可以通过hystrix执行当前服务的降级代码来返回降级结果，例如调用后台商品列表失败时，可以执行从缓存服务器获取缓存数据返回给客户端

#### hystrix的限流措施是什么？

hystrix的熔断可以限制后台服务的调用流量，避免故障向其他服务传播引起雪崩效应。当 10 秒内达到 20 次请求，50%出现失败时会触发熔断，之后进入半开状态，会尝试向后台服务发送客户端调用，调用成功可以自动关闭断路器恢复正常

#### ribbon的主要的重试参数是什么？至少说出两个

- MaxAutoRetries - 一台服务器的重试次数
- MaxAutoRetriesNextServer - 更换服务器的次数
- ReadTimeout - 等待后台服务响应的超时时间

#### springcloud config配置中心默认把配置文件存放在哪里？

git仓库

#### bus消息总线的作用是什么？

bus通过mq服务器来发送配置刷新指令，微服务模块收到指令后，在不重启的情况下可以执行配置刷新操作，重新连接配置中心服务器获取新的配置信息。

#### seata的三大组件是什么？

- TC事务协调器，负责协调各个模块事务的执行
- TM事务管理器，负责启动全局事务，并对全局事务状态进行决策
- RM资源管理器，负责与TC通信，上报分支事务状态和执行二阶段的事务操作

#### seata at事务中数据源代理的作用是什么？

数据源代理中提供了全自动事务控制的代码，对执行的数据操作记录日志

#### 什么是TCC事务，和 seata at 事务有什么不同？

tcc事务是 Try、Confirm和Cancel三个操作，  
Try 操作在第一阶段对数据进行预留，或者称为冻结数据  
Confirm 操作在第二阶段确认预留的资源，即执行二阶段的提交操作  
Cancel 操作在第二阶段取消预留的资源，即执行二阶段的回滚操作  
TCC事务对业务有侵入，两个阶段的三种操作的代码都需要自己来编写，  
而 seata at 事务对业务无侵入，只需添加配置就可以实现分布式事务，使用简便  
80%的业务场景下都可以使用 seata at 事务

#### RabbitMQ 的六种工作模式

- 简单模式
- 工作模式
- 发布和订阅模式
- 路由模式
- 主题模式
- RPC模式

#### 简述 RocketMQ 的事务消息执行流程

1. 发送“半消息”
2. 执行本地事务
3. 对事务消息进行提交或回滚：
    - 本地事务执行成功，提交事务消息
    - 本地事务执行失败，回滚事务消息

由于网络终端，无法执行第三步时，RocketMQ 服务器会每隔1分钟回查事务状态



