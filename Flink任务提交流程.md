## 每天一道数据开发面试题

### Flink任务提交流程

![1589012342938](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/09/162017-711566.png)

#### Flink TaskManager与JobManager的通信

![1642492-20190418164333932-917191269](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/09/162150-820751.png)

Client提交作业到JobManager，就需要跟JobManager进行通信，它使用**Akka框架**或者库进行通信，另外Client与JobManager进行数据交互，使用的是**Netty框架**。Akka通信基于Actor System，Client可以向JobManager发送指令，比如Submit job或者Cancel /update job。JobManager也可以反馈信息给Client，比如status updates，Statistics和results 

Client提交给JobManager的是一个Job，然后JobManager将Job拆分成task，提交给TaskManager（worker）。JobManager与TaskManager也是基于Akka进行通信，JobManager发送指令，比如Deploy/Stop/Cancel Tasks或者触发Checkpoint，反过来TaskManager也会跟JobManager通信返回Task Status，Heartbeat（心跳），Statistics等。另外TaskManager之间的数据通过网络进行传输，比如Data Stream做一些算子的操作，数据往往需要在TaskManager之间做数据传输。