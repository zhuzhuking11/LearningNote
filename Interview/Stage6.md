[TOC]

# Stage6

#### 1. 什么是大数据

大数据（big data），IT行业术语，是指无法在一定时间范围内用常规软件工具进行捕捉、管理和处理的数据集合，是需要新处理模式才能具有更强的决策力、洞察发现力和流程优化能力的海量、高增长率和多样化的信息资产。

#### 2. 大数据特点是什么？

大数据的5V特点（IBM提出）：Volume（大量）、Velocity（高速）、Variety（多样）、Value（低价值密度）、Veracity（真实性）

#### 3. 大数据解决方案的关键步骤是什么

- 提取数据
- 存储数据
- 处理数据

#### 4. 什么是Hadoop?

Hadoop是一个开源软件框架，用于存储大量数据，并发处理/查询在具有多个商用硬件(即低成本硬件)节点的集群上的那些数据。总之，Hadoop包括以下内容：

- HDFS(Hadoop Distributed File System，Hadoop分布式文件系统)：HDFS允许你以一种分布式和冗余的方式存储大量数据。例如，1 GB(即1024 MB)文本文件可以拆分为16 *
  128MB文件，并存储在Hadoop集群中的8个不同节点上。每个分裂可以复制3次，以实现容错，以便如果1个节点故障的话，也有备份。HDFS适用于顺序的“一次写入、多次读取”的类型访问。
- MapReduce：一个计算框架。它以分布式和并行的方式处理大量的数据。当你对所有年龄> 18的用户在上述1 GB文件上执行查询时，将会有“8个映射”函数并行运行，以在其128 MB拆分文件中提取年龄>
  18的用户，然后“reduce”函数将运行以将所有单独的输出组合成单个最终结果。 YARN(Yet Another Resource Nagotiator，又一资源定位器)：用于作业调度和集群资源管理的框架。

#### 5. Hadoop生态系统中有那些框架？

拥有15多种框架和工具，如Sqoop，Flume，Kafka，Pig，Hive，Spark，Impala等  
某些工具(如Pig和Hive)是MapReduce上的抽象层，而Spark和Impala等其他工具则是来自MapReduce的改进架构/设计，用于显著提高的延迟以支持近实时(即NRT)和实时处理。

#### 6. 谈谈 hadoop1 和 hadoop2 的区别

hadoop1的主要结构是由HDFS和mapreduce组成的，HDFS主要是用来存储数据，mapreduce主要是用来计算的，那么HDFS的数据是由namenode来存储元数据信息，datanode来存储数据的。Jobtracker接收用户的操作请求之后去分配资源执行task任务。  
在hadoop2中，首先避免了namenode单点故障的问题，使用两个namenode来组成namenode
feduration的机构，两个namenode使用相同的命名空间，一个是standby状态，一个是active状态。用户访问的时候，访问standby状态，并且，

#### 7. hadoop运行原理

hadoop的主要核心是由两部分组成，HDFS和mapreduce，首先HDFS的原理就是分布式的文件存储系统，将一个大的文件，分割成多个小的文件，进行存储在多台服务器上。  
Mapreduce的原理就是使用JobTracker和TaskTracker来进行作业的执行。Map就是将任务展开，reduce是汇总处理后的结果。

#### 8. mapreduce的原理

mapreduce的原理就是将一个MapReduce框架由一个单独的master JobTracker和每个集群节点一个slave
TaskTracker共同组成。master负责调度构成一个作业的所有任务，这些的slave上，master监控它们的执行，重新执行已经失败的任务。而slave仅负责执行由maste指派的任务。

#### 9. 简要描述如何安装配置一个apache开源版hadoop，描述即可，列出步骤更好

- 解压hadoop包，到指定安装文件夹
- 配置linux基本网络环境、jdk环境、防火墙环境
- 修改主机名，方便后面UI的访问
- 修改hadoop/etc/hadoop/conf下的配置文件，根据部署的模式和需要进行配置
- 格式化namenode，对数据缓存的的路径进行格式化
- 启动hadoop进程

#### 10. 请列出正常工作的hadoop集群中hadoop都需要启动哪些进程，他们的作用分别是什么？

- namenode =>HDFS的守护进程，负责维护整个文件系统，存储着整个文件系统的元数据信息，有image+edit log namenode不会持久化存储这些数据，而是在启动时重建这些数据。
- datanode =>是具体文件系统的工作节点，当我们需要某个数据，namenode告诉我们去哪里找，就直接和那个DataNode对应的服务器的后台进程进行通信，由DataNode进行数据的检索，然后进行具体的读/写操作
- secondarynamenode =>一个冗余的守护进程，相当于一个namenode的元数据的备份机制，定期的更新，和namenode进行通信，将namenode上的image和edits进行合并，可以作为namenode的备份使用
- resourcemanager =>是yarn平台的守护进程，负责所有资源的分配与调度，client的请求由此负责，监控nodemanager
- nodemanager => 是单个节点的资源管理，执行来自resourcemanager的具体任务和命令

#### 11. 请列出你所知道的hadoop调度器，并简要说明其工作方法？

1. 先进先出调度器（FIFO）
    - Hadoop 中默认的调度器，也是一种批处理调度器。它先按照作业的优先级高低，再按照到达时间的先后选择被执行的作业
2. 容量调度器（Capacity Scheduler)
    - 支持多个队列，每个队列可配置一定的资源量，每个队列采用FIFO调度策略，为了防止同一个用户的作业独占队列中的资源，该调度器会对同一用户提交的作业所占资源量进行限定。调度时，首先按以下策略选择一个合适队列：计算每个队列中正在运行的任务数与其应该分得的计算资源之间的比值，选择一个该比值最小的队列；然后按以下策略选择该队列中一个作业：按照作业优先级和提交时间顺序选择，同时考虑用户资源量限制和内存限制
3. 公平调度器（Fair Scheduler）
    - 公平调度是一种赋予作业（job）资源的方法，它的目的是让所有的作业随着时间的推移，都能平均的获取等同的共享资源。所有的 job
      具有相同的资源,当单独一个作业在运行时，它将使用整个集群。当有其它作业被提交上来时，系统会将任务（task）空闲资源（container）赋给这些新的作业，以使得每一个作业都大概获取到等量的CPU时间。与Hadoop默认调度器维护一个作业队列不同，这个特性让小作业在合理的时间内完成的同时又不"
      饿"到消耗较长时间的大作业。公平调度可以和作业优先权搭配使用——优先权像权重一样用作为决定每个作业所能获取的整体计算时间的比例。同计算能力调度器类似，支持多队列多用户，每个队列中的资源量可以配置，
      同一队列中的作业公平共享队列中所有资源。

####  12. hive有哪些方式保存元数据，各有哪些特点？
1. 内嵌Derby数据库存储
   - 这个是hive默认自带的内嵌数据库，用来储存元数据，但这个在配置了hiveserver2和metastore服务后，不支持多个用户同时登录，不方便对数据库的安全访问
2. multi user mode
   - 在自己本地配一个，mysql的数据库用作，hive的元数据的存储数据库，这个需要要自己本地搭建一个mysql数据库，通过配置文件创建一个，hive自己的元数据库，
3. remote server mode
   - 一种在远端配置数据库服务的方式，这个需要配置metastore服务，通过客户端的metastore服务访问服务器上的元数据库达到访问数据的目的
####  13. 请简述hadoop怎么样实现二级排序？
在MapReduce中本身就会对我们key进行排序，所以我们要对value进行排序，主要思想为将key和部分value拼接成一个组合key（实现WritableComparable接口或者调用 setSortComparatorClass函数），这样reduce获取的结果便是先按key排序，后按value排序的结果，在这个方法中，用户需 要自己实现Paritioner，继承Partitioner<>,以便只按照key进行数据划分。Hadoop显式的支持二次排序，在Configuration类中有个 setGroupingComparatorClass()方法，可用于设置排序group的key值。
####  14. HDFS存储机制
HDFS主要是一个分布式的文件存储系统，由namenode来接收用户的操作请求，然后根据文件大小，以及定义的block块的大小，将大的文件切分成多个block块来进行保存
####  15. HDFS数据写入实现机制
- 写入HDFS过程：
1. 根namenode通信请求上传文件，namenode检查目标文件是否已存在，父目录是否存在
2. namenode返回是否可以上传
3. client会先对文件进行切分，比如一个blok块128m，文件有300m就会被切分成3个块，一个128M、一个128M、一个44M请求第一个 block该传输到哪些datanode服务器上
4. namenode返回datanode的服务器
5. client请求一台datanode上传数据（本质上是一个RPC调用，建立pipeline），第一个datanode收到请求会继续调用第二个datanode，然后第二个调用第三个datanode，将整个pipeline建立完成，逐级返回客户端
6. client开始往A上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位（一个packet为64kb），当然在写入的时候datanode会进行数据校验，它并不是通过一个packet进行一次校验而是以chunk为单位进行校验（512byte），第一台datanode收到一个packet就会传给第二台，第二台传给第三台；第一台每传一个packet会放入一个应答队列等待应答
7. 当一个block传输完成之后，client再次请求namenode上传第二个block的服务器。
- 读取文件过程：
1. 使用HDFS提供的客户端开发库Client，向远程的Namenode发起RPC请求；Namenode会视情况返回文件的部分或全部block列表，对于每个block，Namenode都会返回有该block拷贝的DataNode地址；客户端开发库Client会选取离客户端最接近的DataNode来读取block；如果客户端本身就是DataNode,那么将从本地直接获取数据.读取完当前block的数据后，关闭与当前的DataNode连接，并为读取下一个block寻找最佳的DataNode；当读完列表的block后，且文件读取还没有结束，客户端开发库会继续向Namenode获取下一批的block列表。读取完一个block都会进行 checksum 验证，如果读取 datanode 时出现错误，客户端会通知 Namenode，然后再从下一个拥有该 block 拷贝的 datanode 继续读。
####  16. 文件大小默认为 128M，改为 64M 有啥影响
更改文件的block块大小，需要根据我们的实际生产中来更改block的大小，如果block定义的太小，大的文件都会被切分成太多的小文件，减慢用户上传效率，如果block定义的太大，那么太多的小文件可能都会存到一个block块中，虽然不浪费硬盘资源，可是还是会增加namenode的管理内存压力。
####  17. mapreduce的大致流程
**主要分为八个步骤**
1. 对文件进行切片规划
2. 启动相应数量的maptask进程
3. 调用FileInputFormat中的RecordReader，读一行数据并封装为k1v1
4. 调用自定义的map函数，并将k1v1传给map
5. 收集map的输出，进行分区和排序
6. reduce task任务启动，并从map端拉取数据
7. reduce task调用自定义的reduce函数进行处理
8. 调用outputformat的recordwriter将结果数据输出
####  18. datanode在什么情况下不会备份数据
在客户端上传文件时指定文件副本数量为1
####  19. 搭建hadoop集群 ， master和slaves都运行哪些服务
master主要是运行我们的主节点，slaves主要是运行我们的从节点
####  20. hadoop节点的动态上线下线的大概操作
- 节点上线
1. 关闭新增节点的防火墙
2. 在 NameNode节点的hosts文件中加入新增数据节点的hostname
3. 在每个新增数据节点的hosts文件中加入NameNode的hostname
4. 在NameNode节点上增加新增节点的SSH免密码登录的操作
5. 在NameNode节点上的dfs.hosts中追加上新增节点的hostname,
6. 在其他节点上执行刷新操作：hdfs dfsadmin -refreshNodes
7. 在 NameNode 节点上，更改slaves文件，将要上线的数据节点hostname追加 到slaves文件中
8. 启动DataNode节点
9. 查看NameNode的监控页面看是否有新增加的节点      
- 节点下线
1. 修改/conf/hdfs-site.xml文件
2. 确定需要下线的机器，dfs.osts.exclude文件中配置好需要下架的机器，这个是阻止下架的机器去连接NameNode
3. 配置完成之后进行配置的刷新操作./bin/hadoop dfsadmin -refreshNodes,这个操作的作用是在后台进行block块的移动
4. 当执行三的命令完成之后，需要下架的机器就可以关闭了，
5. 机器下线完毕，将他们从 excludes 文件中移除。
####  21. 什么是Hive
Hive是由Facebook开源用于解决海量结构化日志的数据统计；Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射 成一张表，并提供类SQL查询功能，底层计算引擎默认为Hadoop的MapReduce（本质是将sql转化成mapreduce程序），可以将引擎更换为Spark/Tez；
####  22. Hive优点
- 操作接口采用类SQL语法，提供快速开发的能力(简单、容易上手)；
- 避免了去写MapReduce，减少开发人员的学习成本；
- 统一的元数据管理；
- 易扩展(HDFS+MapReduce：可以扩展集群规模；支持自定义函数)；
####  23. Hive使用场景
- 数据的离线处理；比如：日志分析，海量结构化数据离线分析…
- Hive的执行延迟比较高，因此hive常用于数据分析的，对实时性要求不高的场合；
- Hive优势在于处理大数据，对于处理小数据没有优势，因为Hive的执行延迟比较高。
####  24. Hive有哪些方式存储元数据，各有哪些优缺点
三种：内存数据库 derby—小，不常用。
本地mysql
####  25. SQL转化成MapReduce过程
**Hive是如何将SQL转化为MapReduce任务的，整个编译过程分为六个阶段：**
1. Antlr定义SQL的语法规则，完成SQL词法，语法解析，将SQL转化为抽象语法树AST Tree；
2. 遍历AST Tree，抽象出查询的基本组成单元QueryBlock；
3. 遍历QueryBlock，翻译为执行操作树OperatorTree；
4. 逻辑层优化器进行OperatorTree变换，合并不必要的ReduceSinkOperator，减少shuffle数据量；
5. 遍历OperatorTree，翻译为MapReduce任务；
6. 物理层优化器进行MapReduce任务的变换，生成最终的执行计划
####  26. hive内部表和外部表的区别
- 内部表(MANAGED_TABLE)：内部表其实就是管理表，当我们删除一个管理表时，Hive 也会删除这个表中数据。因此管理表不适合和 其他工具共享数据。
- 外部表(EXTERNAL_TABLE)：删除该表并不会删除掉原始数据，删除的是表的元数据。
####  
####  