## 每天一道数据开发面试题

### Flink Slot与并行度关系

- 一个特定算子的子任务的个数被成为并行度，一般情况下，一个Stream的并行度，可以认为就是其所有算子中最大的并行度
- JobGraph中的节点代表操作或算子，A、B并行度为4，C并行度为2....
- Slot为JVM中的线程，Flink允许子任务共享slot，也就是一个Slot中可以有多种操作或算子
- Slot是静态概念，是指TaskManager具有并发执行的能力

![1589108382692](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/10/185951-292513.png)