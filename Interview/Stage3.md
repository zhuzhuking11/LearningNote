[TOC]

# stage3

#### 简述MVVM思想

- M: Model 模型层
- V: View视图层
- VM: ViewModel 视图模型控制  
  vue是实现了双向数据绑定的mvvm框架，当视图改变更新模型层，当模型层改变更新视图层。 在vue中，使用了双向绑定技术，就是View的变化能实时让Model发生变化，而Model的变化也能实时更新到View。

#### vue-router 是什么?它有哪些组件

vue-router是用来定义路由规则的  
组件: 1.router-link 2.router-view

#### created和mounted的区别?

- created:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
- mounted:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些

#### 请详细说下你对vue生命周期的理解?

总共分为8个阶段 1.创建前/后，2.载入前/后，3.更新前/后，4.销毁前/后。

1. 创建前/后： 在beforeCreated阶段，vue实例的挂载元素$el和**数据对象**data都为undefined，还未初始化。
2. 在created阶段，vue实例的数据对象data有了，$el还没有。 载入前/后：
3. 在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前为虚拟的dom节点，data.message还未替换。
4. 在mounted阶段，vue实例挂载完成，data.message成功渲染。
5. 更新前/后：当data变化时，会触发beforeUpdate和updated方法。
6. 销毁前/后：在执行destroy方法后，对data的改变不会再触发周期函数，说明此时vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。

#### 什么是 Spring Boot Stater ？

启动器是一套方便的依赖描述符，它可以放在自己的程序中。你可以一站式的获取你所需要的 Spring 和相关技术，而不需要依赖描述符的通过示例代码搜索和复制黏贴的负载。  
真正做到开箱即用

#### 常见的 Starter Project Options (常见启动项有哪些)

**Spring Boot 也提供了其它的启动器项目包括，包括用于开发特定类型应用程序的典型依赖项。**

- spring-boot-starter-web-services - SOAP Web Services
- spring-boot-starter-web - Web 和 RESTful 应用程序
- spring-boot-starter-test - 单元测试和集成测试
- spring-boot-starter-jdbc - 传统的 JDBC
- spring-boot-starter-hateoas - 为服务添加 HATEOAS 功能
- spring-boot-starter-security - 使用 SpringSecurity 进行身份验证和授权
- spring-boot-starter-data-jpa - 带有 Hibeernate 的 Spring Data JPA
- spring-boot-starter-data-rest - 使用 Spring Data REST 公布简单的 REST 服务

#### 为什么我们需要 spring-boot-maven-plugin?

- spring-boot-maven-plugin 提供了一些像 jar 一样打包或者运行应用程序的命令。
- spring-boot:run 运行你的 SpringBooty 应用程序。
- spring-boot：repackage 重新打包你的 jar 包或者是 war 包使其可执行
- spring-boot：start 和 spring-boot：stop 管理 Spring Boot 应用程序的生命周期（也可以说是为了集成测试）。
- spring-boot:build-info 生成执行器可以使用的构造信息。

#### 如何查看Linux系统都有哪些进程？

ps -aux 或者ps -elf

#### 如何修改Linux IP 需要编辑哪个配置文件，修改完配置文件后，如何重启服务，使配置生效？

路径: 使用vi或者vim编辑器编辑网卡配置文件/etc/sysconfig/network-scripts/ifcft-eth0(网卡文件名称可变)  
重启服务: service network restart

#### Linux 中NGINX常用命令

1. 启动 ./nginx
2. 停止 nginx -s stop 或 nginx -s quit
3. 重启 ./nginx -s reload(平滑重启) 或 service nginx reload 。
4. 查看nginx版本 nginx -v 。
5. 检查配置文件是否正确 nginx -t 。

#### 什么是正向代理

正向代理，意思是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标原始服务器，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端才能使用正向代理。

**特点:**

1. 客户端可以使用正向代理(正向代理是客户端代理)
2. 正向代理 用户非常清楚自己访问服务器的地址, 服务器不知道真实的请求者是谁
3. 应用场景:   美团外卖,滴滴打车,路由器机制

#### 什么是反向代理

反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为Web加速，即使用反向代理作为Web服务器的前置机来降低网络和服务器的负载，提高访问效率。

**特点:**

1. 服务器端使用反向代理( 服务器端代理)
2. 反向代理 用户访问服务器时,用户不清楚真实的服务器到底是谁, 服务器清楚是谁访问的.
3. 使用场景:  一般服务器端都是反向代理

#### Nginx负载均衡机制

1. 轮询机制 根据配置文件的顺序,依次访问服务器. 默认的负载均衡策略
2. 权重策略 如果需要设定访问服务器的频次(某个访问的多,某个访问的少),可以通过权重的策略实现.  
   例如: server localhost:8091 weight=9;
3. IPHASH 如果需要用户与服务器进行绑定时,则设定IPHash

#### Nginx 有哪些优点？

1. 跨平台、配置简单。
2. 非阻塞、高并发连接 2-3 万并发连接数，官方监测能支持 5 万并发。
3. 内存消耗小 1个服务不超过2M
4. 成本低廉，且开源。
5. 稳定性高，宕机的概率非常小。

#### 简述什么是跨域

1. 如果浏览器的网址与Ajax请求网址必须满足同源策略.则浏览器才能解析Ajax,执行正确的请求.
2. 同源策略规定 规定要求:请求协议://域名:端口号
    1. 如果3者都满足,则符合同源策略.浏览器可以正确解析.
    2. 反之(有一个不满足)不满足同源策略.称之为跨域请求 则浏览器不能正常解析Ajax请求.

3. 跨域解决方案:
    1. JSONP(以前使用)
    2. CORS(跨域资源共享)

#### 简述什么是CORS
CORS是一个W3C标准，全称跨域资源共享（Cross-origin resource sharing）。
它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。  
CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。  
如果实现跨域资源共享,则需要单独配置服务端即可. 只需要将允许访问的页面的网址,通过响应头的形式进行设定.则可以实现跨域请求