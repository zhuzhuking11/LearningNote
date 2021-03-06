[TOC]



# Nacos 注册与配置中心

## 基础篇
#### 我们的服务实例是如何注册到Nacos服务的？(基于HTTP请求)
#### 在Nacos中服务提供者是如何向注册中心(Registry)续约的？(5秒心跳)
#### 对于Nacos服务来讲它是如何判定服务实例的状态？(检测心跳包，15,30)
#### 服务启动时如何找到服务启动注册配置类?(NacosNamingService)
#### 服务消费方是如何调用服务提供方的服务的？(RestTemplate+LoadBalancerClient)
#### 当nacos注册中心关闭了，服务消费方还能调用到服务吗？(可以，本地缓存)
#### Nacos注册中心基于什么协议进行服务提供者的实例信息推送？(UDP协议)
#### Nacos 是如何实现其高可用的?(重试，心跳，缓存、集群)
#### 什么是配置中心？（存储项目配置信息的一个服务）
#### 为什么要使用配置中心？(集中管理配置信息，动态发布配置信息)
#### 市场上有哪些主流的配置中心？(Apollo,nacos,…..)
#### 配置中心一般都会配置什么内容？(可能会经常变化的配置信息，例如连接池，日志、线程池、限流熔断规则)
#### 什么信息一般不会写到配置中心?(服务端口，服务名，服务的注册地址，配置中心)
#### 项目中为什么要定义bootstrap.yml文件？(定义优先访问的配置信息)
#### Nacos配置中的管理模型是怎样的？（namespace,group,service/data-id）
#### Nacos配置中心关闭，微服务还能都到配置信息吗?(可以，都本地缓存)
#### Nacos的客户端是采用什么机制从配置中心获取数据的？(拉取+namespce+group+dataId)
#### Nacos 的服务端为什么不采取配置推送(push)方式，而是客户端pull方式去获取配置信息？(服务端的压力可能会比较大，除非服务端的性能要求非常高)
#### Nacos客户端(微服务)是否可以读取共享配置?(可以)
## 进阶篇
#### 服务消费方是如何拿到nacos注册中心的服务提供者实例的？(pull)
#### 微服务应用中我们的客户端如何获取配置中心的信息?(可以基于客户端轮询的方式)
#### 微服务应用中客户端如何感知配置中心数据变化？(@RefreshScope)
# Ribbon 负载均衡
## 基础篇
#### Ribbon 是什么？(Netflix公司提供的一个负载均衡客户端)
#### Ribbon 可以解决什么问题? (基于负载均衡策略进行服务调用)
#### @LoadBalanced的作用是什么？(告诉Spring框架，再使用RestTempalte进行服务调用时，要启动负载均衡策略。)
## 进阶篇 
#### Ribbon 内置的负载策略都有哪些?(8种)
#### 我们可以自己定义负载均衡策略吗？(可以，基于IRule接口进行策略定义)
# Feign 远程调用
## 基础篇
#### 为什么使用feign?
基于Feign可以更加友好的实现服务调用，简化服务消费方对服务提供方方法的调用。
#### @FeignClient注解的作用是什么？

主要用于客户端服务发现，实际就是调用其他的服务。

#### Feign方式调用底层负载均衡是如何实现的？

feign集成了ribbon，主要是通过ribbon来实现负载均衡

## 进阶篇
#### Feign方式的底层调用过程是怎样的？

1. 通过 @EnableFeignCleints 注解告诉springcloud,启动 Feign Starter 组件。

2) Feign Starter 会在项目启动过程中注册全局配置，扫描包下所由@FeignClient注解描述的接口，然后由系统底层创建接口实现类(JDK代理类)，并构建类的对象，然后交给spring管理(注册 IOC 容器)。
3) Feign接口被调用时会被动态代理类逻辑拦截，然后将 @FeignClient 请求信息通过编码器创建 Request对象，基于此对象进行远程过程调用。
4) Feign客户端请求对象会经Ribbon进行负载均衡，挑选出一个健康的 Server 实例（instance）。
5) Feign客户端会携带 Request 调用远端服务并返回一个响应。
6) Feign客户端对象对Response信息进行解析然后返回客户端。

#### Feign方式的调用过程中出现异常如何解决？



# Sentinel 熔断限流
## 基础篇
#### Sentinel是什么？(阿里推出一个流量控制平台，防卫兵)
#### 类似Sentinel的产品你知道有什么？(hystrix)
#### 你了解哪些限流算法？(计数器、令牌桶、漏斗算法，滑动窗口算法，…)
#### Sentinel 默认的限流算法是什么？(滑动窗口算法)
#### 你了解sentinel中的阈值应用类型吗?（两种-QPS,线程数）
#### Sentinel 限流规则中默认有哪些限流模式?(直连，关联，链路)
#### Sentinel的限流效果有哪些？(快速失败，预热，排队)
#### Sentinel 为什么可以对我们的业务进行限流，原理是什么？
我们在访问web应用时，在web引用内部会有一个拦截器，这个拦截器会对请求的url进行拦截，拦截到请求以后，读取sentinel 控制台推送到web应用的流控规则，基于流控规则对流量进行限流操作。
#### Sentinel如何针对热点参数进行限流？
#### 何为降级熔断？（让外部应用停止对服务的访问）
#### 为什么要进行熔断呢？(一般服务不稳定，例如平均响应速度越来越慢或经常出现异常，这样可能会导致调用链堆积，最终系统崩溃)
#### Sentinel中限流，降级的异常父类是谁？(BlockException)
#### Sentinel 出现降级熔断时，系统底层抛出的异常是谁？(DegradeException)
#### Sentinel中异常处理接口是谁？（BlockExceptionHandler）
#### Sentinel中异常处理接口下默认的实现类为? (DefaultBlockExceptionHandler)
#### 假如Sentinel中默认的异常处理规则不满足我们的需求怎么办?(自己定义)
#### 我们如何自己定义Sentinel中异常处理呢？
#### Sentinel 降级熔断策略有哪些？（慢调用，异常比例，异常数）
#### Sentinel 熔断处理逻辑中的有哪些状态？(Open，HalfOpen,Closed)
#### Sentinel 对服务调用进行熔断以后处于什么状态？(熔断打开状态-Open)
#### Sentinel 设置的熔断时长到期以后，Sentinel的熔断会处于什么状态？
探测-HalfOpen,假如再次访问时依旧响应时间比较长或依旧有异常，则继续熔断
#### Sentinel 中的熔断逻辑恢复正常调用以后，会出现什么状态?(熔断关闭-closed)
#### 如何理解热点数据？(访问频度比较高的数据)
#### 热点数据的限流规则是怎样的?(主要是针对参数进行限流设计)
#### 热点数据中的特殊参数如何理解？(热点限流中的某个参数值的阈值设计)
#### 对于热点数据的访问出现限流以后底层异常是什么？(ParamFlowException)
#### Sentinel 限流熔断、系统保护等规则默认是存储在哪里的？（内存）
#### Sentinel 限流熔断等规则是否支持持久化?(支持)
#### Sentinel 限流熔断规则为什么要持久化？(防止系统在重启以后规则数据调用)
## 进阶篇
#### Sentinel模式支持的持久化方式有哪些？(file,nacos,zk,redis, Apollo, consul)
#### Sentinel持久化的流控信息，在微服务客户端是如何获取的？(服务端推，客户端拉)
#### Sentinel持久化的流控信息，在生产环境一般推荐采用什么模式？
推模式，实时性比较好，缺陷是服务端压力会比较大，还有可能会产生消息堆积
#### Sentinel 基于nacos的持久化的实现过程是怎样的？(依赖，数据源配置，nacos新建配置)
#### Nacos中存储sentinel流控信息的格式是什么？(JSON)
# Gateway网关设计
## 基础篇
#### 什么是网关？服务访问的一个入口，类似生活中的“海关“
#### 为什么使用网关？(服务安全，统一服务入口管理，负载均衡，限流，鉴权)
#### Spring Cloud Gateway 应用的初始构建过程(添加依赖，配置)
#### Gateway 服务的启动底层是通过谁去实现的？(Netty)
#### Gateway 服务做请求转发时一定要在注册中心进行注册吗？
#### 网关层面是如何实现负载均衡的？(通过服务名去查找具体的服务实例)
#### 网关层面是如何通过服务名查找服务实例的？(Ribbon)
#### 你了解Ribbon中的哪些负载均衡算法?(轮询，权重，hash,…..)
#### 网关进行请求转发的流程是怎样，有哪些关键对象？
客户端向Spring Cloud Gateway发出请求。 如果Gateway Handler Mapping 通过断言predicates(predicates)的集合确定请求与路由(Routers)匹配，则将其发送到Gateway Web Handler。 Gateway Web Handler 通过确定的路由中所配置的过滤器集合链式调用过滤器（也就是所谓的责任链模式）。 Filter由虚线分隔的原因是， Filter可以在发送代理请求之前和之后运行逻辑。处理的逻辑是 在处理请求时 排在前面的过滤器先执行，而处理返回相应的时候，排在后面的过滤器先执行。

#### 网关层面服务的映射方式怎样的？(断言-path,服务名/服务实例)
#### 网关层如何记录服务的映射？(通过map，并要考虑锁的应用)
#### 何为谓词?(网关中封装了判断逻辑的一个对象)
#### 谓词逻辑的设计是怎样的？(谓词判断逻辑返回值为true则进行请求转发)
#### 你了解哪些谓词逻辑？(path,请求参数，ip，请求方式，cookie,请求头,….)
## 进阶篇 
#### 我们可以自己定义谓词工厂对象吗？(可以的)
#### 网关层面结合sentinel实现限流，其限流的类型有几种？(两种-route id,api)
#### 网关层面可以自定义限流后的异常处理结果吗？(可以)
#### 你知道Sentinel底层限流的算法有哪些？(滑动窗口，令牌桶，漏斗，。。。)
# Zipkin 链路追踪
## 基础篇 
#### 为什么要进行链路监控?(及时发现问题、找到问题并解决问题)
#### 你了解市场那些链路监控产品?(zipkin/skywalking)
#### Zipkin 进行链路监控时有什么缺陷吗？(性能问题，监控数据的设计需要更详细一些)
## 进阶篇
#### Zipkin进行链路监控的的底层原理是怎样的？

一个应用的代码发起HTTP get请求，经过Trace框架拦截，然后

1. 把当前调用链的Trace信息添加到HTTP Header里面
2. 记录当前调用的时间戳
3. 发送HTTP请求，把trace相关的header信息携带上
4. 调用结束之后，记录当前调用话费的时间
5. 然后把上面流程产生的 信息汇集成一个span，把这个span信息上传到zipkin的Collector模块