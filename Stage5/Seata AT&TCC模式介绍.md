[TOC]

## Seata介绍
Seata 是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata 将为用户提供了 AT、TCC、SAGA 和 XA 事务模式，为用户打造一站式的分布式解决方案。  
2019 年 1 月，阿里巴巴中间件团队发起了开源项目 Fescar（Fast & EaSy Commit And Rollback），和社区一起共建开源分布式事务解决方案。Fescar 的愿景是让分布式事务的使用像本地事务的使用一样，简单和高效，并逐步解决开发者们遇到的分布式事务方面的所有难题。   
Fescar 开源后，蚂蚁金服加入 Fescar 社区参与共建，并在 Fescar 0.4.0 版本中贡献了 TCC 模式。   
为了打造更中立、更开放、生态更加丰富的分布式事务开源社区，经过社区核心成员的投票，大家决定对 Fescar 进行品牌升级，并更名为 Seata，意为：Simple Extensible Autonomous Transaction Architecture，是一套一站式分布式事务解决方案。   
Seata 融合了阿里巴巴和蚂蚁金服在分布式事务技术上的积累，并沉淀了新零售、云计算和新金融等场景下丰富的实践经验，但要实现适用于所有的分布式事务场景的愿景，仍有很长的路要走。

## Seata AT事务方案  
Seata 的 AT 模式（Automatic Transaction）是一种无侵入的分布式事务解决方案。下面结合具体业务场景来分析其执行的原理。  
### 业务场景
订单系统  

![业务](https://img-blog.csdnimg.cn/20200725232213359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

当用户下订单时，执行以下三步流程：

1. **订单系统**保存订单
2. 订单系统调用**库存服务**，减少商品库存
3. 订单系统调用**账户服务**，扣减用户金额

这三步要作为一个整体事务进行管理，要么整体成功，要么整体失败。

## Seata AT基本原理

Seata AT 事务分两个阶段来管理全局事务：
**第一阶段：** 执行各分支事务
**第二阶段：** 控制全局事务最终提交或回滚

### 第一阶段：执行各分支事务

微服务系统中，各服务之间无法相互感知事务是否执行成功，这时就需要一个专门的服务，来协调各个服务的运行状态。这个服务称为 TC（Transaction Coordinator），事务协调器。

![tc](https://img-blog.csdnimg.cn/20200725234150443.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

订单系统开始执行保存订单之前，首先启动 TM（Transaction Manager，事务管理器），由 TM 向 TC 申请开启一个全局事务：

![TM](https://img-blog.csdnimg.cn/20200725234911823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

这时TC会产生一个**全局事务ID**，称为 **XID**，并将 XID 传回 TM：

![xid](https://img-blog.csdnimg.cn/20200726001410404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

这样就开启了**全局事务**！

全局事务开启后，开始执行创建订单的业务。首先执行保存订单，这时会先启动一个 RM（Resource Manager，资源管理器），并将 XID 传递给 RM。

![rm](https://img-blog.csdnimg.cn/20200726001859187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

RM 负责对分支事务（即微服务的本地事务）进行管理，并与 TC 通信，上报分支事务的执行状态、接收全局事务的提交或回滚指令。

RM 首先会使用 XID 向 TC 注册分支事务，将分支事务纳入对应的全局事务管辖。

![rm](https://img-blog.csdnimg.cn/20200726002039831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

现在可以执行保存订单的分支事务了。一旦分支事务执行成功，RM 会上报事务状态：

![tx](https://img-blog.csdnimg.cn/20200726003557248.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

TC 收到后，会将该状态信息传递到 TM：

![o](https://img-blog.csdnimg.cn/20200726104528104.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70)

到此，保存订单过程结束。下面是调用库存服务，减少商品库存，与订单的执行过程相同。

首先调用库存服务，启动 RM，并传递 XID：

![s](https://img-blog.csdnimg.cn/20200726104702337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

库存服务的 RM 使用 XID 向 TC 进行注册，纳入全局事务管辖：

![s](https://img-blog.csdnimg.cn/20200726104836446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

执行本地事务成功后上报状态，TC会将状态发送给TM：

![s](https://img-blog.csdnimg.cn/20200726105128545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

相同的，完成账户分支事务：

![a](https://img-blog.csdnimg.cn/20200726105428261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第二阶段：控制全局事务最终提交

现在，TM（全局事务管理器）收集齐了全部分支事务的成功状态，它会进行决策，确定全局事务成功，向 TC 发送全局事务的提交请求：

![tm](https://img-blog.csdnimg.cn/20200726110936560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

然后，TC 会向所有 RM 发送提交操作指令，RM 会完成最终提交操作：

![tc](https://img-blog.csdnimg.cn/20200726111513383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

到此，全局事务全部提交完成！

### 第二阶段：控制全局事务最终回滚

上面是全局事务执行成功的情况，下面再来看看事务执行失败的情况。

假设订单业务执行过程中，扣减账户金额这一步分支事务执行失败，那么失败状态对TC上报，然后再发送给TM：

![a](https://img-blog.csdnimg.cn/20200726113359673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70)

TM 会进行决策，确定全局事务**失败**，向 TC 发送全局事务的**回滚**请求：

![tm](https://img-blog.csdnimg.cn/20200726115232566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

然后，TC 会向所有 RM 发送回滚操作指令，RM 会完成最终回滚操作：

![tc](https://img-blog.csdnimg.cn/20200726120213144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

## Seata AT具体工作机制

以上了解了 Seata AT 的基本原理、工作流程，那么 Seata 具体是如何实现全局事务的提交和回滚操作呢？下面来分析 Seata 的具体工作机制。

### 第一阶段：执行分支事务

以全面订单业务中的库存服务为例，库存表中存在一条商品的库存信息：

![s](https://img-blog.csdnimg.cn/20200726160219330.png#pic_center)

现在要执行业务操作减少库存，从50件减少到40件：

![s](https://img-blog.csdnimg.cn/2020072616034431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

执行修改库存业务操作前， 会先取出旧的库存信息：

![s](https://img-blog.csdnimg.cn/20200726160439346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

现在可以修改库存了：

![s](https://img-blog.csdnimg.cn/20200726160525175.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

接着，取出更新后的新数据：

![s](https://img-blog.csdnimg.cn/20200726160608179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

接下来，会把旧数据和新数据合并起来，保存到一个事务回滚日志表：undo_log表：

![s](https://img-blog.csdnimg.cn/20200726160655484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

至此，第一阶段，分支事务完成，将状态上报给TC：

![a](https://img-blog.csdnimg.cn/20200726161105593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第二阶段：控制全局事务最终回滚

假如全局事务失败，那么第一阶段已提交的分支事务要执行回滚操作。

首先会收到来自 TC 的全局事务回滚指令：

![s](https://img-blog.csdnimg.cn/20200726161610408.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

接下来，根据事务回滚日志（undo_log）表的记录，将商品恢复成旧的库存数据：

![s](https://img-blog.csdnimg.cn/20200726161735527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

然后删除事务日志，最终完成第二阶段回滚操作：

![s](https://img-blog.csdnimg.cn/20200726162101520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第二阶段：控制全局事务最终提交

上面是全局事务回滚操作。如果全局事务成功，要完成最终提交，AT模式最终提交操作非常简单，只需要删除日志数据即可。

首先接收到 TC 的全局事务提交指令：

![s](https://img-blog.csdnimg.cn/20200726162409754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

接着，直接删除事务日志，就完成了第二阶段提交操作：

![a](https://img-blog.csdnimg.cn/20200817175254193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

## TCC 基本原理

TCC 与 Seata AT 事务一样都是**两阶段事务**，它与 AT 事务的主要区别为：

- **TCC 对业务代码侵入严重**
  每个阶段的数据操作都要自己进行编码来实现，事务框架无法自动处理。
- **TCC 效率更高**
  不必对数据加**全局锁**，允许多个事务同时操作数据。

![a](https://img-blog.csdnimg.cn/2020073022341983.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第一阶段 Try

以账户服务为例，当下订单时要扣减用户账户金额：

![a](https://img-blog.csdnimg.cn/2020073019240411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

假如用户购买 100 元商品，要扣减 100 元。

TCC 事务首先对这100元的扣减金额进行预留，或者说是先冻结这100元：

![a](https://img-blog.csdnimg.cn/20200730192602828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第二阶段 Confirm

如果第一阶段能够顺利完成，那么说明“扣减金额”业务（分支事务）最终肯定是可以成功的。当全局事务提交时， TC会控制当前分支事务进行提交，如果提交失败，TC 会反复尝试，直到提交成功为止。

当全局事务提交时，就可以使用冻结的金额来最终实现业务数据操作：

![a](https://img-blog.csdnimg.cn/20200730214849953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 第二阶段 Cancel

如果全局事务回滚，就把冻结的金额进行解冻，恢复到以前的状态，TC 会控制当前分支事务回滚，如果回滚失败，TC 会反复尝试，直到回滚完成为止。

![a](https://img-blog.csdnimg.cn/2020073021565349.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### 多个事务并发的情况

多个TCC全局事务允许并发，它们执行扣减金额时，只需要冻结各自的金额即可：

![a](https://img-blog.csdnimg.cn/20200730220743117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODMwNTQ0MA==,size_16,color_FFFFFF,t_70#pic_center)

### Seata TCC事务模式

Seata 支持 TCC 事务模式，与 AT 模式相同的，也需要以下组件来支持全局事务的控制：

- TC 事务协调器
- TM 事务管理器
- RM 资源管理器
