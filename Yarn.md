# 	每天一道数据开发面试题

## Yarn

### 1、为什么要使用YARN？

为了提升集群的利用率、资源统一管理， 使用YARN为上层应用提供统一的资源管理和调度的平台。

### 2、YARN的优势？

​    **资源的统一管理和调度：**

   集群中所有节点的资源(内存、CPU、磁盘、网络等)抽象为Container。计算框架需要资源进行运算任务时需要向YARN申请Container， YARN按照特定的策略对资源进行调度进行Container的分配。

​    **资源隔离：**

   YARN使用了轻量级资源隔离机制Cgroups进行资源隔离以避免相互干扰，一旦Container使用的资源量超过事先定义的上限值，就将其杀死。

### 3、YARN是如何工作的？

介绍YARN调度过程之前，解释几个专用名词：

Resource Manager：全局资源管理器，一个集群只有一个RM。负责和AM(Application Master)交互，资源调度、资源分配等工作。

Application Master：应用程序的管理器，类似项目经理，一个应用程序只有一个AM。负责任务开始时找RM要资源,任务完成时向RM注销自己，释放资源；与NM通信以启动/停止任务；接收NM同步的任务进度信息。

Node Manager：一台机器上的管理者，类似于部门经理。管理着本机上若干小弟Containers的生命周期、监视资源和跟踪节点健康并定时上报给RM；接收并处理来自AM的Container启动/停止等各种请求。

Container：一台机器上具体提供运算资源，将设备上的内存、CPU、磁盘、网络等资源封装在一起的抽象概念——“资源容器”，Container是一个动态资源分配单位，为了限定每个任务使用的资源量。

![img](typora-user-images\clip_image002.png)



NM和Container是一台设备上的不同进程

Attempt：提交到Yarn中的应用程序被称为Application，它可能会尝试运行多次，每次的尝试运行称为“Application Attempt”，如果一次尝试运行失败，则由RMApp创建另一个继续运行，直至达到失败次数的上限![1587907949511](C:\Users\10033\AppData\Roaming\Typora\typora-user-images\1587907949511.png)

以下通俗地解释一下向YARN提交一个应用程序时的执行过程：

1、用户向YARN提交程序，以Map Reduce程序为例，Resource Manager(资源管理器)接收到客户端程序的运行请求

2、Resource Manager分配一个Container(资源)用来启动Application Master(程序管理员)，并告知Node Manager(节点管理员)，要求它在这个Container下启动Application Master

3、Application Master启动后，向Resource Manager发起注册请求

4、Application Master向Resource Manager申请资源

5、取得资源后，根据资源，向相关的Node Manager通信，要求其启动程序

6、Node Manager（多个）启动MR（每个MR任务都是一个job，可以在job日志中查看程序运行日志）

7、Node Manager不断汇报MR状态和进展给Application Master

8、当MR全部完成时，Application Master向Resource Manager汇报任务完成，并注销自己