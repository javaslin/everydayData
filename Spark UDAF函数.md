## 每天一道数据开发面试题

### Spark UDAF函数

#### 什么是UDAF？

- UDAF（user-defined aggregate function, 用户定义的聚合函数）
- 同时处理多行，并且返回一个结果，通常结合使用 GROUP BY 语句（例如 COUNT 或 SUM）

#### 实例

使用Scala实现一个UDAF函数需要自定义一个Object并继承自UserDefinedAggregateFunction且需要实现其抽象方法

```
initialize()方法,初始方法，即没数据时的值
update() 方法把每一行的数据进行计算，放到缓冲对象中
merge() 把每个分区，缓冲对象进行合并
evaluate()计算结果表达式，把缓冲对象中的数据进行最终计算
```

##### 实现平均值计算

```scala
	//聚合函数的输入参数数据类型
    def inputSchema: StructType = {
      StructType(StructField("inputColumn",LongType) :: Nil)
    }

    //中间缓存的数据类型
    def bufferSchema: StructType = {
      StructType(StructField("sum",LongType) :: StructField("count",LongType) :: Nil)
    }

    //最终输出结果的数据类型
    def dataType: DataType = DoubleType

    def deterministic: Boolean = true

    //初始值，要是DataSet没有数据，就返回该值
    def initialize(buffer: MutableAggregationBuffer): Unit = {
      buffer(0) = 0L
      buffer(1) = 0L
    }

    /**
      * @param buffer  相当于把当前分区的，每行数据都需要进行计算，计算的结果保存到buffer中
      * @param input
      */
    def update(buffer: MutableAggregationBuffer, input: Row): Unit ={
      if(!input.isNullAt(0)){
        buffer(0) = buffer.getLong(0) + input.getLong(0)   // salary
        buffer(1) = buffer.getLong(1) + 1  // count
      }
    }

    /**
      * 相当于把每个分区的数据进行汇总
      * @param buffer1  分区一的数据
      * @param buffer2  分区二的数据
      */
    def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit={
      buffer1(0) = buffer1.getLong(0) +buffer2.getLong(0)  //   salary
      buffer1(1) = buffer1.getLong(1) +buffer2.getLong(1)  // count
    }
    //计算最终的结果
    def evaluate(buffer: Row): Double = buffer.getLong(0).toDouble / buffer.getLong(1)
	//使用
	def main(args: Array[String]): Unit = {

    	val spark = sparkSession(true)

    	spark.udf.register("MyAverage",MyAverage)

    	val df = spark.read("xxx")
    	df.createOrReplaceTempView("employees")
    	val sqlDF = spark.sql("select MyAverage(salary)  as average_salary from employees  ")
    	sqlDF.show()
    	spark.stop()
  }
```