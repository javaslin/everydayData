## 每天一道数据开发面试题

### Yarn执行流程

![img](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/03/171550-991703.jpeg)

- 1、用户向YARN提交应用程序，其中包括ApplicationMaster程序、启动ApplicationMaster的命令、用户程序等；
- 2、ResourceManager为该应用程序分配第一个Container，并与对应的NodeManager通信，要求它在整个Container中启动应用程序的ApplicationMaster；
- 3、ApplicationMaster首先向ResourceManager注册，这样用户可以直接通过ResourceManager查看应用程序的运行状态，然后它将为各个任务申请资源，并监控它的运行状态，直到运行结束，即重复步骤4~7；
- 4、ApplicationMaster采用轮询的方式通过RPC协议向ResourceManager申请和领取资源；
- 5、一旦ApplicationMaster申请到资源后，则与对应的NodeManager通信，要求其启动任务；
- 6、NodeManager为任务设置好运行环境（包括环境变量、jar包、二进制程序等）后，将任务启动命令写到一个脚本中，并通过运行该脚本启动任务；
- 7、各个任务通过某RPC协议向ApplicationMaster汇报自己的状态和进度，以让ApplicationMaster随时掌握各个任务的运行状态，从而可以在任务失败时重新启动任务。
- 在应用程序运行过程中，用户可以随时通过RPC向ApplicationMaster查询应用程序的当前运行状态；
- 8、应用程序运行完成后，ApplicationMaster向ResourceManager注销并关闭自己。

