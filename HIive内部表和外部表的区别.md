## 每天一道数据开发面试题

### Hive内部表和外部表的区别

#### 1、建表方式

外表建表是需要添加external的关键字。

```sql
--创建内部表
CREATE TABLE test1(id string);

--创建外部表
CREATE EXTERNAL TABLE test2(id string);
```

建表的时候也可以指定表的location，即位置

#### 2、是否删除数据

​	内表在删除表的时候会将其中的表的元数据和数据一并删除。如果在建表的时候指定了表的location的话，load数据的时候会将数据移动到location中，删除表会将location内的全部删除。

​	而外部表删除表的时候只是删除了表的元数据但是数据是不会被删除的。如果在建表的时候指定了表的location的话，load数据的时候会将数据转到location中（前提是load的数据在hdfs上面），在删除外部表的时候location中的数据会保留的。



**hive之所以引入外部表是因为如果大量数据在hdfs上面可以直接加载到表中，无需移动数据，如果你的数据量在TB级别的话，建议直接建立带分区的外表，这样能够直接将数据入库。**


  