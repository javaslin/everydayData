## 每天一道数据开发面试题

### RDD的宽依赖和窄依赖

![](https://raw.githubusercontent.com/javaslin/typoraImg/master/typora202005/03/171623-851020.jpeg)

####  窄依赖

窄依赖是指**1个父RDD分区对应1个子RDD的分区。**换句话说，一个父RDD的分区对应于一个子RDD的分区，或者多个父RDD的分区对应于一个子RDD的分区。所以窄依赖又可以分为两种情况：

- 1个子RDD的分区对应于1个父RDD的分区，比如map，filter，union等算子
- 1个子RDD的分区对应于N个父RDD的分区，比如co-partioned join

####  宽依赖

宽依赖是指**1个父RDD分区对应多个子RDD分区。**宽依赖有分为两种情况

- 1个父RDD对应非全部多个子RDD分区，比如groupByKey，reduceByKey，sortByKey
- 1个父RDD对应所有子RDD分区，比如未经协同划分的join

