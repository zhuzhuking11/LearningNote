1. hadoop:是一个分布式存储和分布式计算的框架,

2. hadoop的核心组件:

   hdfs:分布式存储组件

   mapreduce:分布式计算组件

   yarn:资源调度器

3. hadoop的来源:

   主要来自于google的两篇论文:GFS,Mapreduce

4. hadoop的架构:

   主要包括hdfs和yarn架构:主从架构

   jdfs:

   namenode:主节点,主要用于元数据的管理,接收客户端的文件请求,管理从节点

   datanode:从节点,主要用于存储数据

   secondarynamenode: 辅助名称节点,辅助namenode管理元数据,定期合并元数据

   yarn:

   resourcemanager:主节点,主要用于接收用户提交的job请求,分配资源

   nodemanager:从节点,用于执行job任务.

5. hadoop的安装方式

   1. 单机模式:直接解压,一般不去用,买有hdfs功能
   2. 伪分布模式:一般是开发测试使用
   3. 完全分布式:一般是在生产中使用

   core-site.xml

   hdfs-site.xml

   mapred-site.xml

   yarn-site.xml

6. 初体验

   1. 提交数据到hdfs

      ```
      hadoop fs -put xxx /
      ```

   2. 提交作业到yarn

      ```
      hadoop jar xxx.jar 
      ```

7. HDFS

   1. hdfs:分布式文件存储系统,来自于GFS的论文思想

   2. hdfs:主从结构

   3. block:块,是一个逻辑的概念,上传的数据按照block来进行文件的划分和存储.默认是128m

   4. replication:副本机制,数据冗余存储.默认按照3个副本存储

   5. 名字空间,在hdfs上数据存储的路径.hdfs://namenode:8020/a/b

   6. 心跳机制:datanode定时向namenode发送心跳,表明自己还活着

   7. 一次写入,多次读取:强调是hdfs不支持修改,所以一般是读取数据,完成数据的分析操作

   8. 元数据:描述数据的数据,比如:文件大小,名称等

      Fsimage:镜像文件,是全部的元数据的聚合

      edits:临时文件,默认每个一小时,生成的元数据文件

      secondarynamenode:职责就是将fsimage和edits的元数据进行合并

   9. hdfs的写入流程:

      hdfs的数据写入路程:串行写入.

      hdfs的数据读取流程:并行读取

   10. hdfs的shell api

       ```shell
       hadoop fs -xxx
       hadoop fs -put xxx /xxx #上传文件
       hadoop fs -gegt hadoop/xxx/xxx file:///xxx #下载文件
       hadoop fs -mkdir -p xxx
       hadoop fs -lsr /
       hadoop fs -rmr /
       ```

   11. hdfs java api

       ```java
       FileSystem fs = FileSystem.get(conf)
       ```

8. MapReduce

   mapreduce:分布式计算组件

   mapreduce的核心思想:分而治之

   主要分两个阶段:

   map阶段:将数据进行转换.默认情况下就是一个block对应一个map任务

   reduce阶段:将数据进行聚合

   mapreduce的api实现:

   主要完成Mapper和Reducer的实现

   ```java
   map: k1,v1 -> k2,v2
   reduce:k2,v2 -> k3,v3
   ```

9. yarn

   yarn:资源调度管理器

   主要调度的资源: CPU, memory

   架构:resourcemanager和nodemanager

   主要分为3种资源调度:

   1. FIFO:先进先出,任务排队
   2. capacity:资源的优化调整,分为多个FIFO,进行任务资源调度
   3. fair:共享资源,平局分配资源