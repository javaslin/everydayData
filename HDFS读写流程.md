## 每天一道数据开发面试题

### HDFS读写流程

#### HDFS读流程

![1589718952285](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/17/203559-944346.png)

1）客户端向namenode请求下载文件，namenode通过查询元数据，找到文件块所在的datanode地址。

2）挑选一台datanode（就近原则，然后随机）服务器，请求读取数据。

3）datanode开始传输数据给客户端（从磁盘里面读取数据放入流，以packet为单位来做校验）。

4）客户端以packet为单位接收，先在本地缓存，然后写入目标文件。

#### HDFS写流程

![1589719061638](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/17/203742-187545.png)

1）客户端向namenode请求上传文件，namenode检查目标文件是否已存在，父目录是否存在。

2）namenode返回是否可以上传。

3）客户端请求第一个 block上传到哪几个datanode服务器上。

4）namenode返回3个datanode节点，分别为dn1、dn2、dn3。

5）客户端请求dn1上传数据，dn1收到请求会继续调用dn2，然后dn2调用dn3，将这个通信管道建立完成

6）dn1、dn2、dn3逐级应答客户端

7）客户端开始往dn1上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位，dn1收到一个packet就会传给dn2，dn2传给dn3；dn1每传一个packet会放入一个应答队列等待应答

8）当一个block传输完成之后，客户端再次请求namenode上传第二个block的服务器。（重复执行3-7步）