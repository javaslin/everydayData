# 每天一道数据开发面试题

## Spark sql执行计划

spark sql 内部使用 dataFrame 和 Dataset 来表示一个数据集合，然后你可以在这个数据集合上应用各种统计函数和算子，有人可能对 DataFrame 和 Dataset 分不太清，其实 DataFrame 就是一种类型为 Row 的 DataSet，
 
type DataFrame = Dataset[Row]
 
这里说的 Row 类型在 Spark sql 对外暴露的 API 层面来说的， 然而 DataSet 并不要求输入类型为 Row，也可以是一种强类型的数据，DataSet 底层处理的数据类型为 Catalyst 内部 InternalRow 或者 UnsafeRow 类型, 背后有一个 Encoder 进行隐式转换，把你输入的数据转换为内部的 InternalRow，那么这样推论，DataFrame 就对应 RowEncoder。
 
在 Dataset 上进行 transformations 操作就会生成一个元素为 LogicalPlan 类型的树形结构, 我们来举个例子，假如我有一张学生表，一张分数表，需求是统计所有大于 11 岁的学生的总分。

![1587967040097](https://github.com/javaslin/everydayData/blob/master/typora-user-images/1587967040097.png)

![img](https://github.com/javaslin/everydayData/blob/master/typora-user-images/FuuD-SffLPee6Y-iiG-9RLBVLn5y.png)

这个 queryExecution 就是整个执行计划的执行引擎, 里面有执行过程中，各个中间过程变量，整个执行流程如下

![img](https://github.com/javaslin/everydayData/blob/master/typora-user-images/FuCF5eIrAVZM83ZMdHahd1sddI28.png)

那么我们上面例子中的 sql 语句经过 Parser 解析后就会变成一个抽象语法树，对应解析后的逻辑计划 AST 为 
￼![img](https://github.com/javaslin/everydayData/blob/master/typora-user-images/FnJjohYvFSpN0dpO313dKf4ayl61.png)

形象一点用图来表示
 
￼![img](https://github.com/javaslin/everydayData/blob/master/typora-user-images/FlUJLnJJrWyVAEtMuXesWrhRxiRq.png)

我们可以看到过滤条件变为了 Filter 节点，这个节点是 UnaryNode 类型， 也就是只有一个孩子，两个表中的数据变为了 UnresolvedRelation 节点，这个节点是 LeafNode 类型， 顾名思义，叶子节点， JOIN 操作就表位了 Join 节点， 这个是一个 BinaryNode 节点，有两个孩子。
 
上面说的这些节点都是 LogicalPlan 类型的， 可以理解为进行各种操作的 Operator， spark sql 对应各种操作定义了各种 Operator。
 
![img](https://github.com/javaslin/everydayData/blob/master/typora-user-images/FvP1pnNfZdp8L2dwZdsfiz7HXYMF.png)￼
 
这些 operator 组成的抽象语法树就是整个 Catatyst 优化的基础，Catatyst 优化器会在这个树上面进行各种折腾，把树上面的节点挪来挪去来进行优化。

现在经过 Parser 有了抽象语法树，但是并不知道 score，sum 这些东西是啥，所以就需要 analyer 来定位, analyzer 会把 AST 上所有 Unresolved 的东西都转变为 resolved 状态，sparksql 有很多resolve 规则，都很好理解，例如 ResolverRelations 就是解析表（列）的基本类型等信息，ResolveFuncions 就是解析出来函数的基本信息，比如例子中的sum 函数，ResolveReferences 可能不太好理解，我们在 sql 语句中使用的字段比如 Select name 中的 name 对应一个变量， 这个变量在解析表的时候就作为一个变量（Attribute 类型）存在了，那么 Select 对应的 Project 节点中对应的相同的变量就变成了一个引用，他们有相同的 ID，所以经过 ResolveReferences 处理后，就变成了 AttributeReference 类型 ，保证在最后真正加载数据的时候他们被赋予相同的值，就跟我们写代码的时候定义一个变量一样，这些 Rule 就反复作用在节点上，指定树节点趋于稳定，当然优化的次数多了会浪费性能，所以有的 rule 作用 Once， 有的 rule 作用 FixedPoint， 这都是要取舍的。