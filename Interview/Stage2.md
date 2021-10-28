[TOC]

# Stage2

#### 数据库里索引的作用

主要作用为了增开数据的查询速度，它的执行也是有一定条件的，不是加了索引就能够加快查询的效率，由于索引的创建是会占据内存空间的，mysql中通过Explain Select来查看索引的扫描次数。情况如下： 以下不适合加索引：

1. 如果每次都需要取到所有表记录，无论如何都必须进行全表扫描了，那么是否加索引也没有意义了。
2. 对非唯一的字段，例如“性别”这种大量重复值的字段，增加索引也没有什么意义。
3. 对于记录比较少的表，增加索引不会带来速度的优化反而浪费了存储空间，因为索引是需要存储空间的，而且有个致命缺点是对于update/insert/delete的每次执行，字段的索引都必须重新计算更新

#### 什么是数据库的事务

事物就是为了保证原有事物的完整性和一致性，通过一定的条件规定该事物的可靠性和安全性（如数据库中的事物）。  
A: 原子性：一个事物是不可再分的工作单位，该事物要么成功要么失败。  
B: 一致性：事物必须是使数据库从另外一个一致性状态到另一个一致性状态，与原子性密切相关。  
C: 持久性：指一个事物一旦提交后对数据库中数据的改变应该是永久不变的。  
D：隔离性：一个事物的执行不能够被其他事物所干扰。

#### 数据库里的字段约束有哪些，各有什么作用

1. 主键约束：实现实体的完整性（PK：主键），值唯一，不能为空，且可以自动递增
2. 外键约束：用于多表之间的查询该表组建作为另外张表的外键建立标语标志就的关系（FK）
3. 非空约束：（NOT NULL）保证该字段的内容不能为空null
4. 唯一约束：列表的唯一性约束（UNIQUE）
5. 检查约束：检查列的值是否满足一个boolean表达式（CHECK）
6. 默认约束：给指定的字段可以设置默认值，不设置值时使用默认值而不是null

#### 数据库优化的方案有哪些

1. 查询时，能不用* 就不用，尽量写全字段名。
2. 索引不是越多越好，每个表控制在6个索引以内。范围where条件的情况下，索引不起作用，比如where value<100
3. 大部分情况连接效率远大于子查询，但是有例外。当你对连接查询的效率都感到不能接受的时候可以试试用子查询，虽然大部分情况下你会更失望，但总有碰到惊喜的时候不是么...
4. 多用explain 和 profile分析查询语句
5. 有时候可以1条大的SQL可以分成几个小SQL顺序执行，分了吧，速度会快很多。
6. 每隔一段时间用alter table table_name engine=innodb;优化表
7. 连接时注意:小表 jion 大表的原则
8. 学会用explain 和 profile判断是什么原因使你的SQL慢
9. 查看慢查询日志，找出执行时间长的SQL进行优化
10. 尽量避免使用order by
11. 因为where子句后面的条件是执行顺序是从右到左，所以尽量把能过滤掉大部分数据的条件放在最后

#### 什么是Spring IOC

Spring的控制反转：把一个类放入到spring容器中，对象的创建、初始化、销毁等工作交给spring容器来做。由spring容器控制对象的生命周期。

#### 描述Spring AOP的机制及原理

- AOP把系统分为两部分，核心关注点和横切关注点。核心主要处理业务逻辑，横切主要是权限验证，日志，事物处理等。
- AOP主要由：方面（Aspect）一个关注点的模块化，这个关注点实现可能另外横切多个对象。事务管理是J2EE应用中一个很好的横切关注点例子。方面用Spring的 Advisor或拦截器实现
- 连接点（Joinpoint）：程序执行过程中明确的点，如方法的调用或特定的异常被抛出。
- 通知（Advice）：Spring中定义了四个advice: BeforeAdvice, AfterAdvice, ThrowAdvice和DynamicIntroductionAdvice
- 切入点（Pointcut）： Spring定义了Pointcut接口，用来组合MethodMatcher和ClassFilter，可以通过名字很清楚的理解，
  MethodMatcher是用来检查目标类的方法是否可以被应用此通知，而ClassFilter是用来检查Pointcut是否应该应用到目标类上
- 引入（Introduction）Spring中要使用Introduction, 可有通过DelegatingIntroductionInterceptor来实现通知，通
- 目标对象（Target Object）: 包含连接点的对象。也被称作被通知或被代理对象。
- AOP代理（AOP Proxy）: AOP框架创建的对象，包含通知。 在Spring中，AOP代理可以是JDK动态代理或者CGLIB代理。
- 织入（Weaving）: 组装方面来创建一个被通知对象。这可以在编译时完成（例如使用AspectJ编译器），也可以在运行时完成。Spring和其他纯Java AOP框架一样，在运行时完成织入。

#### 描述Spring MVC处理流程及应用优势

SpringMVC严格上来说是一种设计思想而不是设计模式，其底层采用反射的机制构建工程，适配器，代理模式构建。

1. 用户向服务器发送请求，请求被Spring 前端控制Servelt DispatcherServlet捕获；
2. DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain对象的形式返回；
3. DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。（附注：如果成功获得HandlerAdapter后，此时将开始执行拦截器的preHandler(...)方法）
4. 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作： HttpMessageConveter：
   将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等 数据格式化：对请求消息进行数据格式化。
   如将字符串转换成格式化数字或格式化日期等 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中
5. Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象；
6. 根据返回的ModelAndView，选择一个适合的ViewResolver（必须是已经注册到Spring容器中的ViewResolver)返回给DispatcherServlet ；
7. ViewResolver 结合Model和View，来渲染视图
8. 将渲染结果返回给客户端。

#### MyBatis应用中#与$有什么区别
#能够有效防止SQL的注入攻击，并且是预编译的SQL  
#还能够将传入的数值当成一个字符串，会对自动传入的数据加上一个双引号。  
如：where name=#{name} 如果传入的值是jack，那么解析成的SQL是：where name="jack"  
$会将传入的值直接显示在sql中，会导致异常 并且不能防止sql的攻击，且相对低效  
#### Mybatis的工作原理
mybatis应用程序通过SqlSessionFactoryBuilder从mybatis-config.xml配置文件（也可以用Java文件配置的方式，需要添加@Configuration）中构建出SqlSessionFactory（SqlSessionFactory是线程安全的）；  
然后，SqlSessionFactory的实例直接开启一个SqlSession，再通过SqlSession实例获得Mapper对象并运行Mapper映射的SQL语句，完成对数据库的CRUD和事务提交，之后关闭SqlSession。  
说明：SqlSession是单线程对象，因为它是非线程安全的，是持久化操作的独享对象，类似jdbc中的Connection，底层就封装了jdbc连接。
#### MyBatis中resultType和resultMap的区别
MyBatis中在查询进行select映射的时候，返回类型可以用resultType，也可以用resultMap，resultType是直接表示返回类型的(对应着我们的model对象中的实体)，而resultMap则是对外部ResultMap的引用(提前定义了db和model之间的隐射key-->value关系)，但是resultType跟resultMap不能同时存在。  
在MyBatis进行查询映射时，其实查询出来的每一个属性都是放在一个对应的Map里面的，其中键是属性名，值则是其对应的值。  
1. 当提供的返回类型属性是resultType时，MyBatis会将Map里面的键值对取出赋给resultType所指定的对象对应的属性。所以其实MyBatis的每一个查询映射的返回类型都是ResultMap，只是当提供的返回类型属性是resultType的时候，MyBatis对自动的给把对应的值赋给resultType所指定对象的属性。
2. 当提供的返回类型是resultMap时，因为Map不能很好表示领域模型，就需要自己再进一步的把它转化为对应的对象，这常常在复杂查询中很有作用。
#### MyBatis中缓存的LRU算法描述及实现分析
LRU也就是“最近最少使用”,LRU缓存就是使用这种原理实现的，就是缓存一定数量的数据，当超过该数据的阈值时就把相关的数据删除掉，例如我们缓存10000条数据，当小于该数据时可以随意的增加，超过时就将过期的数据删除掉。  
好比java中实现LRU就有两种方式：一是LinkedHashMap，一种是自己设计的数据结构，使用链表+HashMap。
#### get和post请求的区别？
1. get请求用来从服务器上获得资源，而post是用来向服务器提交数据；
2. get将表单中数据按照name=value的形式，添加到action 所指向的URL  后面，并且两者使用"?"连接，而各个变量之间使用"&"连接；post是将表单中的数据放在HTTP协议的请求头或消息体中，传递到action所指向URL；
3. get传输的数据要受到URL长度限制（1024字节）；而post可以传输大量的数据， POST数据是没有限制的，上传文件通常要使用post方式；
4. 使用get时参数会显示在地址栏上，如果这些数据不是敏感数据，那么可以使用get；对于敏感数据还是应用使用post；
5. get使用MIME类型application/x-www-form-urlencoded的URL编码（也叫百分号编码）文本的格式传递参数，保证被传送的参数由遵循规范的文本组成，例如一个空格的编码是"%20"。
6. Jsp页面中的FORM标签里的method属性为get时调用doGet()，为post时调用doPost()。
#### 什么是SQL攻击？如何解决
SQL注入：就是通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。具体来说，它是利用现有应用程序，将（恶意）的SQL命令注入到后台数据库引擎执行的能力，它可以通过在Web表单中输入（恶意）SQL语句得到一个存在安全漏洞的网站上的数据库，而不是按照设计者意图去执行SQL语句。  
**防止的方法：**
1. 永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双"-"进行转换等。
2. 永远不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取。
3. 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
4. 不要把机密信息明文存放，请加密或者hash掉密码和敏感的信息。
5. 应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装，把异常信息存放在独立的表中。
#### 什么是盒子模型？
在网页中，一个元素占有空间的大小由几个部分构成，其中包括元素的内容(content)，元素的内边距(padding)，元素的边框(border)，元素的外边距(margin)四个部分。这四个部分占有的空间中，有的部分可以显示相应的内容，而有的部分只用来分隔相邻的区域或区域。4个部分一起构成了css中元素的盒模型。
#### 简述一下src与href的区别
1. href 是指向网络资源所在位置，建立和当前元素(锚点)或当前文档(链接)之间的链接，用于超链接。
2. src是指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置;在请求src资源时会将其指向的资源下载并应用到文档内，例如js脚本，img图片和frame等元素。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。
#### CSS选择符有哪些？
1. id选择器（ # myid）
2. 类选择器（.myclassname）
3. 标签选择器（div, h1, p）
4. 相邻选择器（h1 + p）
5. 子选择器（ul > li）
6. 后代选择器（li a）
7. 通配符选择器（ * ）
8. 属性选择器（a[rel = "external"]）
9. 伪类选择器（a:hover, li:nth-child）