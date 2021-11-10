# Java基础

## 面向对象

- 什么是面向对象:封装,继承,多态

- 封装

  - 类
  - 对象
  - 引用
  - 构造方法
  - this
  - 方法重写
  - 隐藏

- 继承

  - 代码复用/重用
  - 父类中定义的通用代码,子类不用重复编写
  - 创建子类实例
    1. 先创建父类实例,先执行父类构造方法
    2. 在创建子类实例,执行子类构造方法
    3. 两个实例绑定,整体作为一个实例
  - 调用成员
    - 先找子类,再找父类
  - 方法重写
    - 从父类级继承的方法,在子类重新定义,重新编写
  - super
    - super() 调用父类构造方法,默认调用无参,也可以手动调用有参
    - super.f() 一般在方法重写时,调用父类中的同一个方法

- 多态

  - 类型转换:向上转型,向下转型
  - 作用: 所有子类型都可以被当作一致的父类型处理
  - 在某种类型需要特殊处理时,需要向下转回成它本身的类型再处理
  - instanceof
    - 运行期类型识别
    - 对真实类型及其父类型判断都返回true

- 抽象类 - 半成品类,未完成的方法要由子类实现

- final 

  - 常量 - 不可变

    ```java
    final int a=5
    a=6;//错
    
    final Student s=new Student(5,"张三");//内存地址不可变
    s.setName("李四");//对
    s=new Student();//错
    s=null;//错
    ```

  - 方法 - 不能重写

  - 类- 不能被继承

    - System
    - String

- static

  - 静态是属于类的,不属于实例

  - 静态不是面向对象的语法

  - 静态初始化块

    ```java
    class A{
    	static {
    		类加载时,只执行一次
    	}
        public void a(){
            
        }
    }
    ```

- 访问控制符

  - private,[default],protected,public
  - 便于代码维护修改

- 接口

  - 作用: 程序结构设计工具,用来解耦

- 内部类

  - 非静态内部类
  - 静态内部类
  - **匿名内部类**
  - 局部内部类-方法内或局部代码块内

## 集合

- ArrayList
  - 数组存储数据
  - 默认初始长度10
  - 1.5倍增长
  - new ArrayList(10000)
- LinkedList
  - 双向链表
  - 两端效率高
  - 如果只在两端操作数据,使用LinkedList
  - 栈(Stack)操作 -LIFO
    - push()
    - pop()
  - 队列(Queue)操作- FIFO
    - offer()
    - pick()
    - pull()
- HashMap
  - 用Entry[]数组存放数据
  - 数组默认长度16
  - 运算过程
    - 获得键的哈希值key.hashCode()
    - 用哈希值**计算下标**
    - 新建Entry实例封装键值对,放入下标i位置
      - 如果是空位置直接放入
      - 如果有数据,依次**用equals()比较键是否相等**
        - 找到相等的键,覆盖值
        - **没有相等的键,用链表连在一起**
    - **加载因子.负载率0.75**
      - 新建翻倍容量的新数组
      - 所有数据重新hash,放入新数组
    - jdk1.8
      - 链表长度到8,转成**红黑树**
      - 树上的数据减少到6,转回成链表

- ConcurrentHashMap
  - 分段锁
- LinkedHashMap
  - 放入HashMap的数据,再用双向链表连在一起
  - 有序的HashMap

## 线程

- 线程的创建

  - 继承Thread
  - 实现Runnable

- 线程的方法

  - run()
  - start()
  - Thread.sleep()
  - Thread.yield()
  - interrupt()
  - join()

- 线程同步 synchronized

  - synchronized(.class){

    }

    获取指定对象的锁

  - synchronized void f(){

    }

    获取当前实例的锁

  - static synchronized void f(){

    }

    获取""类对象""的锁

- 生产者消费者模型

  - 线程间的通信模型
  - 生产者线程产生数据放入集合
  - 消费者线程从集合获取数据

- 等待和通知

  - 没有数据的情况下,消费者要暂停等待数据

  - 生产者产生数据后,通知消费者取数据

  - Object的方法

    - wait()

    - notify()
    - notifyAll()

  - 线程在集合上等待数据: list.wait()

  - 线程在集合上发出通知: list.notifyAll()

  - 等待和通知方法必须在synchrinized(list)内才能调用

  - 等待和通知的对象必须是加锁的对象

- 线程池

  - ExcutorService- 线程池

  - Executors-辅助创建线程池的工具类

  - 新建线程池:

    - Excutors.newFixedThreadPool(5) -最多只能创建5个线程
    - Excutors.newCachedThreadPool()创建足够多的线程,使任务不必等待
    - Excutors.newSingleThreadPoolExcutor() -只创建1个线程

  - 把Runnable任务丢进线程池

    - pool.executor(Runnable)

  - 把Callable任务丢进线程池

    - Callable -1.有返回值 2.可以抛异常

    - pool.submit()

    - 可以以异步方式,获取一个线程的执行结果

      ```java
      //Future 是取餐条
      Future future=pool.submit(Callable)
      //继续执行其他运算
      //直到需要计算结果时,用取餐条去取餐
      Object result = future.get();
      ```

- ThreadLocal

  - 线程数据绑定
  - 把线程当作一条流水线,上游在线程放入数据,下游可以访问数据
  - 方法
    - set()
    - get()
    - remove()

- volatile

  - 可见性
    - 多线程共享数据,对频繁修改的数据要加volatile,防止数据不可见
  - 禁用指令重排
    - cpu会对程序指令顺序进行优化,提高运算效率
    - 如果这种优化会让程序产生错误的运行结果,可以禁用重排

## MySql数据库

### 查询优化

`select * from a where title ='abc' and desc='def' `

### 创建索引

`create index title_index on a(title)`

`create index title_describe_index on a(title,describe)`

### 对查询进行分析

`explain select * from a where title='abc'`

- 是否使用索引
- 扫描的数据量是多少

### 分库分表(分片) 工具MyCat

数据量过大时,必须分库分表,把数据分散存放

## 前端

## Mybatis

## Springboot

## Spring

## Spring MVC

## Spring Cloud Alibaba

## Spring Cloud Nteflix