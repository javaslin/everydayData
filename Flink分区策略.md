## 每天一道数据开发面试题

### Flink 分区策略

什么要搞懂什么是分区策略。分区策略是用来决定数据如何发送至下游Operator。目前 Flink 支持了8中分区策略的实现。

**GlobalPartitioner**数据会被分发到下游算子的第一个实例中进行处理。

```scala
dataStream
    .setParallelism(2)
    // 采用GLOBAL分区策略重分区
    .global()
    .print()
    .setParallelism(1);
```

**ShufflePartitioner**数据会被随机分发到下游算子的每一个实例中进行处理。

```scala
dataStream
    .setParallelism(2)
    // 采用SHUFFLE分区策略重分区
    .shuffle()
    .print()
    .setParallelism(4);
```

**RebalancePartitioner**数据会被循环发送到下游的每一个实例中进行处理。

```scala
dataStream
        .setParallelism(2)
        // 采用REBALANCE分区策略重分区
        .rebalance()
        .print()
        .setParallelism(4);
```

**RescalePartitioner**这种分区器会根据上下游算子的并行度，循环的方式输出到下游算子的每个实例。这里有点难以理解，假设上游并行度为2，编号为A和B。下游并行度为4，编号为1，2，3，4。那么A则把数据循环发送给1和2，B则把数据循环发送给3和4。假设上游并行度为4，编号为A，B，C，D。下游并行度为2，编号为1，2。那么A和B则把数据发送给1，C和D则把数据发送给2。

```scala
dataStream
    .setParallelism(2)
    // 采用RESCALE分区策略重分区
    .rescale()
    .print()
    .setParallelism(4);
```

**BroadcastPartitioner**广播分区会将上游数据输出到下游算子的每个实例中。适合于大数据集和小数据集做join的场景。

```scala
dataStream
    .setParallelism(2)
    // 采用BROADCAST分区策略重分区
    .broadcast()
    .print()
    .setParallelism(4);
```

**ForwardPartitioner**ForwardPartitioner 用于将记录输出到下游本地的算子实例。它要求上下游算子并行度一样。简单的说，ForwardPartitioner用来做数据的控制台打印。

```scala
dataStream
    .setParallelism(2)
    // 采用FORWARD分区策略重分区
    .forward()
    .print()
    .setParallelism(2);
```

**KeyGroupStreamPartitioner**Hash分区器。会将数据按 Key 的 Hash 值输出到下游算子实例中。

```scala
dataStream
    .setParallelism(2)
    // 采用HASH分区策略重分区
    .keyBy((KeySelector<Tuple3<String, Integer, String>, String>) value -> value.f0)
    .print()
    .setParallelism(4);
```

**CustomPartitionerWrapper**用户自定义分区器。需要用户自己实现Partitioner接口，来定义自己的分区逻辑。例如：

```java
static class CustomPartitioner implements Partitioner<String> {
      @Override
      public int partition(String key, int numPartitions) {
          switch (key){
              case "1":
                  return 1;
              case "2":
                  return 2;
              case "3":
                  return 3;
              default:
                  return 4;
          }
      }
  }
```

```scala
dataStream
    .setParallelism(2)
    // 采用CUSTOM分区策略重分区
    .partitionCustom(new CustomPartitioner(),0)
    .print()
    .setParallelism(4);
```

