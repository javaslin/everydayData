## 每天一道数据开发面试题

### Spark集群运行的几种模式

- local(本地模式)：常用于本地开发测试，本地还分为local单线程和local-cluster多线程;

- standalone(集群模式)：典型的Mater/slave模式，不过也能看出Master是有单点故障的；Spark支持ZooKeeper来实现 HA。

- on yarn(集群模式)： 运行在 yarn 资源管理器框架之上，由 yarn 负责资源管理，Spark 负责任务调度和计算。

- on mesos(集群模式)： 运行在 mesos 资源管理器框架之上，由 mesos 负责资源管理，Spark 负责任务调度和计算。

- on cloud(集群模式)：比如 AWS 的 EC2，使用这个模式能很方便的访问 Amazon的 S3;Spark 支持多种分布式存储系统：HDFS 和 S3。

  

### 资源管理对比图

![1588053801519](typora-user-images\1588053801519.png)