# RDD

### RDD基本操作

##### RDD基本概念

RDD 是 Spark 提供的最重要的抽象概念，它是一种有容错机制的特殊数据集合，可以分布在集群的结点上，以函数式操作集合的方式进行各种并行操作。

通俗点来讲，可以将 RDD 理解为一个分布式对象集合，本质上是一个只读的分区记录集合。每个 RDD 可以分成多个分区，每个分区就是一个数据集片段。一个 RDD 的不同分区可以保存到集群中的不同结点上，从而可以在集群中的不同结点上进行并行计算。

图 1 展示了 RDD 的分区及分区与工作结点（Worker Node）的分布关系。

![RDD分区及分区与工作节点的分布关系](http://c.biancheng.net/uploads/allimg/190515/5-1Z515091239200.jpg)
图 1  RDD 分区及分区与工作节点的分布关系

RDD 具有容错机制，并且只读不能修改，可以执行确定的转换操作创建新的 RDD。具体来讲，RDD 具有以下几个属性。

- 只读：不能修改，只能通过转换操作生成新的 RDD。
- 分布式：可以分布在多台机器上进行并行处理。
- 弹性：计算过程中内存不够时它会和磁盘进行数据交换。
- 基于内存：可以全部或部分缓存在内存中，在多次计算间重用。

RDD 实质上是一种更为通用的迭代并行计算框架，用户可以显示控制计算的中间结果，然后将其自由运用于之后的计算。

在大数据实际应用开发中存在许多迭代算法，如机器学习、图算法等，和交互式数据挖掘工具。这些应用场景的共同之处是在不同计算阶段之间会重用中间结果，即一个阶段的输出结果会作为下一个阶段的输入。

RDD 正是为了满足这种需求而设计的。虽然 MapReduce 具有自动容错、负载平衡和可拓展性的优点，但是其最大的缺点是采用非循环式的数据流模型，使得在迭代计算时要进行大量的磁盘 I/O 操作。

通过使用 RDD，用户不必担心底层数据的分布式特性，只需要将具体的应用逻辑表达为一系列转换处理，就可以实现管道化，从而避免了中间结果的存储，大大降低了数据复制、磁盘 I/O 和数据序列化的开销。

#####1.构建操作

Spark 里的计算都是通过操作 RDD 完成的，学习 RDD 的第一个问题就是如何构建 RDD，构建 RDD 的方式从数据来源角度分为以下两类。

- 从内存里直接读取数据。
- 从文件系统里读取数据，文件系统的种类很多，常见的就是 HDFS 及本地文件系统。

第一类方式是从内存里构造 RDD，需要使用 makeRDD 方法，代码如下所示。

val rdd01 = sc.makeRDD(List(l,2,3,4,5,6))

这个语句创建了一个由“1,2,3,4,5,6”六个元素组成的 RDD。

第二类方式是通过文件系统构造 RDD，代码如下所示。

val rdd:RDD[String] == sc.textFile(“file:///D:/sparkdata.txt”,1)

这里例子使用的是本地文件系统，所以文件路径协议前缀是 file://。

##### 2.转换操作

RDD 的转换操作是返回新的 RDD 的操作。转换出来的 RDD 是惰性求值的，只有在行动操作中用到这些 RDD 时才会被计算。

许多转换操作都是针对各个元素的，也就是说，这些转换操作每次只会操作 RDD 中的一个元素，不过并不是所有的转换操作都是这样的。表 1 描述了常用的 RDD 转换操作。

​						     **表 1 RDD转换操作（rdd1={1, 2, 3, 3}，rdd2={3,4,5})**

| 函数名         | 作用                                                         | 示例                     | 结果                 |
| -------------- | ------------------------------------------------------------ | ------------------------ | -------------------- |
| map()          | 将函数应用于 RDD 的每个元素，返回值是新的 RDD                | rdd1.map(x=>x+l)         | {2,3,4,4}            |
| flatMap()      | 将函数应用于 RDD 的每个元素，将元素数据进行拆分，变成迭代器，返回值是新的 RDD | rdd1.flatMap(x=>x.to(3)) | {1,2,3,2,3,3,3}      |
| filter()       | 函数会过滤掉不符合条件的元素，返回值是新的 RDD               | rdd1.filter(x=>x!=1)     | {2,3,3}              |
| distinct()     | 将 RDD 里的元素进行去重操作                                  | rdd1.distinct()          | (1,2,3)              |
| union()        | 生成包含两个 RDD 所有元素的新的 RDD                          | rdd1.union(rdd2)         | {1,2,3,3,3,4,5}      |
| intersection() | 求出两个 RDD 的共同元素                                      | rdd1.intersection(rdd2)  | {3}                  |
| subtract()     | 将原 RDD 里和参数 RDD 里相同的元素去掉                       | rdd1.subtract(rdd2)      | {1,2}                |
| cartesian()    | 求两个 RDD 的笛卡儿积                                        | rdd1.cartesian(rdd2)     | {(1,3),(1,4)……(3,5)} |

##### 3.行动操作

行动操作用于执行计算并按指定的方式输出结果。行动操作接受 RDD，但是返回非 RDD，即输出一个值或者结果。在 RDD 执行过程中，真正的计算发生在行动操作。表 2 描述了常用的 RDD 行动操作。

​								**表 2 RDD 行动操作（rdd={1,2,3,3}）**

| 函数名                   | 作用                                                         | 示例                                  | 结果                 |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------- | -------------------- |
| collect()                | 返回 RDD 的所有元素                                          | rdd.collect()                         | {1,2,3,3}            |
| count()                  | RDD 里元素的个数                                             | rdd.count()                           | 4                    |
| countByValue()           | 各元素在 RDD 中的出现次数                                    | rdd.countByValue()                    | {(1,1),(2,1),(3,2})} |
| take(num)                | 从 RDD 中返回 num 个元素                                     | rdd.take(2)                           | {1,2}                |
| top(num)                 | 从 RDD 中，按照默认（降序）或者指定的排序返回最前面的 num 个元素 | rdd.top(2)                            | {3,3}                |
| reduce()                 | 并行整合所有 RDD 数据，如求和操作                            | rdd.reduce((x,y)=>x+y)                | 9                    |
| fold(zero)(func)         | 和 reduce() 功能一样，但需要提供初始值                       | rdd.fold(0)((x,y)=>x+y)               | 9                    |
| foreach(func)            | 对 RDD 的每个元素都使用特定函数                              | rdd1.foreach(x=>printIn(x))           | 打印每一个元素       |
| saveAsTextFile(path)     | 将数据集的元素，以文本的形式保存到文件系统中                 | rdd1.saveAsTextFile(file://home/test) |                      |
| saveAsSequenceFile(path) | 将数据集的元素，以顺序文件格式保存到指 定的目录下            | saveAsSequenceFile(hdfs://home/test)  |                      |