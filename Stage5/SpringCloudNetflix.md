# Spring Cloud Netflix

- 注册中心
    - Nacos
    - Eureka
- 配置中心
    - Nacos
    - Spring Cloud Config
- 远程调用、负载均衡
    - Feign、Ribbon
- 系统容错、限流
    - Sentinel
    - Hystrix
- API网关
    - Spring Cloud Gateway
    - Zuul
- 数据监控
    - Sentinel
    - Hystrix Dashboard+Turbine
    - 链路跟踪监控：Sleuth+Zipkin

## eureka

服务的注册和发现  
**搭建eureka注册中心服务器**

1. 新建 module： sp05-eureka
2. 调整pom.xml
    - 父项目
    - 添加 eureka server 依赖
3. yml
    - 关闭自我保护模式
    - 主机名
    - 针对单台服务器，不向自己注册，不从自己拉取
4. 启动类注解：`@EnableEurekaServer` 触发 eureka 服务器的自动配置

**eureka四条运行机制**

1. 客户端启动时，会反复连接注册中心尝试注册，直到注册成功为止
2. 客户端每30秒发送一次心跳数据，服务器连续3次收不到一个服务的心跳，会删除它的注册信息
3. 客户端每30秒拉取一次注册表，刷新本地注册表缓存
4. 自我保护模式
    - 由于网络中断，15分钟内，85%服务器出现心跳异常，自动进入保护模式， 自我保护模式下所有的注册信息都不删除
    - 网络恢复后，自动退出保护模式
    - 开发调试期间，可以关闭保护模式，避免影响调试

**客户端连接 eureka 注册中心**
修改 2,3,4 项目

1. 添加 eureka client 依赖

2. yml

   eureka连接地址： http://eureka1:2001/eureka

## 高可用

**02商品服务**

1. 右键点击02的启动配置 或者点上面的启动配置下拉菜单 选择 “Edit Configuration”
2. 对02创建两个启动配置，设置启动参数`--server.port=xxxx`

**05eureka注册中心**

## 04调用02、03

1. 在 04 的 pom.xml 添加依赖： openfeign
2. 启动类添加注解：`@EnableFeignClients` 触发Feign的自动配置
3. 定义远程调用接口：
    - ItemClient
    - UserClient
4. OrderServiceImpl，实现远程调用

**Feign 集成 Ribbon 负载均衡和重试**

- Feign集成Ribbon，默认实现了负载均衡和重试

**Ribbon的重试**

远程调用失败，可以自动发起重试调用

- 异常
- 服务器宕机
- 后台服务阻塞超时

重试参数：

- MaxAutoRetries - 单台服务器的重试次数，模式0
- MaxAutoRetriesNextServer - 更换服务器的次数，默认1
- ReadTimeout - 等待响应的超时时间，默认1000
- OkToRetryOnAllOperations - 是否对所有类型请求都重试，默认只对GET请求重试
- ConnectTimeout - 与后台服务器建立连接的等待超时时间，默认1000

## Zuul API 网关

1. 统一的访问入口
2. 统一的权限校验
3. 集成 Ribbon 负载均衡和重试
4. 集成 Hystrix 容错和限流

**统一的访问入口**

1. 新建模块： sp06-zuul

2. 添加依赖：

    - eureka client
    - zuul
    - sp01

3. yml 配置路由转发规则

```yml
#  **包含深层路径
#  * 只包含一层路径

# 服务id设置成访问子路径，是默认规则，
# zuul根据注册表的注册信息完成自动配置
# 最好手动配置，防止注册表不全
zuul:
  routes:
    item-service: /item-service/**
    user-service: /user-service/**
    order-service: /order-service/**
```

4. 启动类添加注解 `@EnableZuulProxy`

**zuul统一权限校验**
http://localhost:3001/item-service/t45t4    没有登录不能访问
http://localhost:3001/item-service/t45t4?token=65345rt    已经登录过，可以访问

1. 新建 ZuulFilter 子类
2. 按 zuul 的规则实现
3. 添加 `@Component` 注解

zuul 的自动配置类可以在 spring 容器中自动发现过滤器实例，完成自动配置

**zuul集成Ribbon**

- 默认启用了负载均衡
- 默认没有启用重试
    - 在入口位置进行重试，会造成后台大面积服务压力翻倍，可能造成故障传播、雪崩

zuul启用重试（不推荐）

1. 添加 spring-retry 依赖
2. yml配置 `zuul.retryable=true`
3. 配置重试参数（可选）

**zuul集成 Hystrix**
Hystrix是容错和限流工具

- 容错 -- 降级

  调用后台服务出错（异常，阻塞，服务崩溃），可以执行当前服务的一段代码，直接向客户端返回降级结果

    - 错误提示
    - 缓存的结果
    - 根据业务逻辑，返回任意的结果

- 添加 Hystrix 降级

1. Zuul默认已经启用 Hystrix
2. 实现 FallbackProvider 接口，按zuul的规则实现接口的方法
3. 添加 `@Component

- Hystrix 限流 - 熔断
- 当流量过大，造成后台服务故障，可以断开链路，限制后台服务的访问流量，等待后台服务恢复
- 断路器打开的条件
    - 10秒20次请求（必须首先满足）
    - 50%出错，执行降级代码
- 半开状态
    - 断路器打开一段时间后，会进入半开状态
    - 会尝试发送一次客户端调用，
        - 成功，关闭断路器，恢复正常
        - 失败，继续保持打开状态

## Hystrix dashboard

Hystrix数据监控仪表盘

Hystrix日志，是通过 Actuator 工具来暴露出来

**Actuator**

springboot 提供的一个项目指标工具，可以通过Actuator获取项目的各种日志数据

- 健康状态
- spring容器中所有的对象
- spring mvc映射的所有路径
- jvm堆内存镜像
- .....

- 添加 Actuator

1. 添加 actuator 依赖

2. 暴露监控日志

```yml
m.e.w.e.i= "*"  暴露所有日志
m.e.w.e.i= health  暴露健康状态日志
m.e.w.e.i= health,beans,mappings,hystrix.stream  暴露多种日志
```

3. 查看日志  
   ``http://localhost:3001/actuator``

**搭建 Hystrix dashboard**

1. 新建模块： sp07-hystrix-dashboard

2. 添加依赖： Hystrix dashboard

3. yml配置

```yml
允许抓取的服务器列表: localhost
```

4. 启动类添加注解： `@EnableHystrixDashboard`

5. 访问 `http://localhost:4001/hystrix`

**Turbine**

聚合多台服务器的日志数据，提供给仪表盘显示

1. 新建模块： sp08-turbine

2. 添加依赖

    - eureka client
    - turbine

3. yml  
```yml
聚合的服务： zuul
为聚合的日志数据命名：new String("default")
```
4. 启动类注解：`@EnableTurbine`  
   
合并的日志地址： `http://localhost:5001/turbine.stream`