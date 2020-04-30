### 每天一道数据开发面试题

#### Spark cache() vs persist() vs checkpoint()

- ##### cache()  

  使用cache()场景：会被重复使用但不太大的RDD可以使用cache(),cache()实际会调用persist(StorageLevel.MEMORY_ONLY) 会将RDD存储到内存中。注：而某些在 transformation() 中 Spark 自己生成的 RDD 是不能被用户直接 cache 的，比如 reduceByKey() 中会生成的 ShuffledRDD、MapPartitionsRDD 是不能被用户直接 cache 的。也就是创建RDD后需要接着调用cache()或persist().

  DataFrame调用cache()时，persist()存储级别是MEMORY_AND_DISK。cache()并不会斩断依赖链（Lineage），而persist()会。

- ##### persist()  与checkpoint().

  rdd.persist(StorageLevel.DISK_ONLY) 与 checkpoint 区别的是：前者虽然可以将 RDD 的 partition 持久化到磁盘，但该 partition 由 blockManager 管理。一旦 driver program 执行结束，也就是 executor 所在进程 CoarseGrainedExecutorBackend stop，blockManager 也会 stop，被 cache 到磁盘上的 RDD 也会被清空（整个 blockManager 使用的 local 文件夹被删除）。而 checkpoint 将 RDD 持久化到 HDFS 或本地文件夹，如果不被手动 remove 掉，是一直存在的，也就是说可以被下一个 driver program 使用，而 cached RDD 不能被其他 dirver program 使用。

- ##### 其他细节

  checkpoint()是lazy的，真正在run job时 会遍历创建的RDD，将需要checkpoint的RDD持久化。

  checkpoint()是在job结束时另起一个job来将RDD持久化，因此，在checkpoint之前最好先cache或persist，避免RDD重复计算。

  checkpoint()和persist()都会clean掉依赖链（Lineage）。

  

  