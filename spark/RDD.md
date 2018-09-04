# RDD编程

## RDD基础
- RDD就是一个不可变的分布式对象集合,
  - 每个RDD被分为多个区
    - 这些分区运行在集群中的不容节点上
- 包含Pyhon,java,Scala中任意类型的对象
  - 可以时自定义对象

- 创建RDD的两种方式
  1. 读取外部数据集
      - `SparkContext.parallelize()`
        - 需要将整个数据集放在一台机器上
        - 使用较少
      - `SparkContext.textFile()`
        - 读入一个文本文件
        - 常用

  2. 在驱动器程序里分发驱动器程序中对象集合

- RDD支持的两种操作
  1. 转化操作
      - 由一个RDD生成另一个新的RDD
      - spark会使用谱系图记录不同的RDD之间的依赖关系
      - `map()`,`filter()`
  2. 行动操作
      - 把结果返回到驱动程序中
      - 存储到外部存储系统中
      - `count()`,`filter()`
        - `filter()`:会返回一个新的RDD,原RDD不受影响
        - `collect()`:获取整个RDD数据
          - 整个数据集能在单台机器的内存放的下时才能使用
            - 不能用在大规模数据集上
      - 每一次行动操作都会重新计算一次
        - 应该避免这种低效操作
      - 可以将中间结果持久化
  - 两种操作的区别
    - spark计算RDD的方式不同
      - 惰性计算
        - 只有在行动操作中用到才会真正计算
        - 默认情况
          - 如果想重用一个RDD
            - `RDD.persist()`方法把RDD缓冲下来
              - 缓存到内存中
    - 转化操作只会返回RDD类型而行动操作则为其他类型
  - 保存RDD数据失败时
    - spark利用这种特性重算丢掉的分区

## Spark程序工作流程
1. 从外部数据创建输入RDD
2. 使用诸如`filter()`这样的转化操作对RDD进行转化,以定义新的RDD
3. 告诉Spark对需要被重用的中间结果RDD进行`persist()`操作
4. 使用行动操作来触发一次并行计算,spark会对计算进行优化并计算

## spark中传递函数
- 函数需要作为实现了 `org.apache.spark.api.function`包中的任意函数接口的对象传递
  - 根据不同的返回值定义不同的接口

- 基本的java函数接口

|        函数名        |      实现方法       |                              用途                              |
|:--------------------:|:-------------------:|:--------------------------------------------------------------:|
|    Function<T,R>     |      R call(T)      |   接收一个输入值并返回一个输出值,用于`map()``filter()`等操作   |
|    Function2<T,R>    |    R call(T1,T2)    | 接收连个输入值并返回一个输出值,用于`aggregate()``fold()`等操作 |
| FlatMapFunction<T,R> | Iterable<R> call(T) |     接收一个输入值并返回人一个输入值,用于`flatmap()`等操作     |


## RDD基本
- 合集操作
  - `RDD.distinct()`
    - 转换操作
    - 生成一个不可重复集
    - 开销很大
  - `RDD.union()`
    - 合并操作
    - 包含重复集
  - `RDD.intersection()`
    - 返回RDD中都有的元素
    - 不包含重复集
    - 性能差
  - `RDD.sbtract()`
    - 返回只存在ARDD中而不存在BRDD中的元素
    - 需要混洗 开销较大
  - `RDD.cartesian()`
    - 获取一个笛卡尔积
    - 开销巨大

- 基本转换操作

| 函数名                                   | 目的                                                    | 示例                    | 结果            |
| ---------------------------------------- | ------------------------------------------------------- | ----------------------- | --------------- |
| `map()`                                  | 将函数应用于所有元素并将返回值生成一个新的RDD           | rdd.map(x=>x+1)         | {2,3,4,4}       |
| `flatMap()`                              | 将函数应用于所有元素将返回的迭代器的所有内容生成一个RDD | rdd.flatMap(x=>x.to(3)) | {1,2,3,2,3,3,3} |
| `filter()`                               | 将符合条件的元素返回生成一个新的RDD                     | rdd.filter(x=>x!=1)     | {2,3,3}         |
| `distinct()`                             | 去重                                                    | rdd.distinct()          | {1,2,3}         |
| `sample(withReplacement,fraCtion[seed])` | 对RDD采样,一级是否替换                                  | rdd.sample(false,0,5)   | 不确定的        |
> RDD为{1,2,3,3},示例为Scala

- 两个RDD的转换

| 函数名           | 目的                      | 示例                    | 结果                 |
| ---------------- | ------------------------- | ----------------------- | -------------------- |
| `union()`        | 生成两个RDD中的所有有元素 | rdd.union(other)        | {1,2,3,3,4,5}        |
| `intersection()` | 生成两个RDD的共存元素     | rdd.intersection(other) | {3}                  |
| `subtract()`     | 移除一个RDD的元素         | rdd.subtract(other)     | {1,2}                |
| `cartesian()`    | 生成笛卡尔积              | rdd.cartesian(other)    | {(1,3),(45)....(35)} |
> rdd为{1,2,3},other为{3,4,5} 示例scala

- 行动操作

|                  函数名                  |                目的                |                                  示例                                  |        结果         |
|:----------------------------------------:|:----------------------------------:|:----------------------------------------------------------------------:|:-------------------:|
|               `collect()`                |        返回RDD中的所有元素         |                             rdd.collect()                              |      {1,2,3,3}      |
|                `count()`                 |          RDD中的元素个数           |                              rdd.count()                               |          4          |
|             `countByValue()`             |         各个元素出现的次数         |                           rdd.countByValue()                           | {(1,1),(2,1),(3,2)} |
|               `take(num)`                |        从RDD中返回num个元素        |                              rdd.take(2)                               |        {1,2}        |
|                `top(num)`                |    从RDD中返回最前面的num个元素    |                               rdd.top(2)                               |        {33}         |
|       `takeOrdere(num)(ordering)`        | 从RDD中按顺序返回最前面的num个元素 |                      rdd.takeOrdere(2)(myOrdring)                      |        {33}         |
| `takeSample(withReplacement,num,[seed])` |      从RDD中返回任意num个元素      |                           rdd.takeSample(2)                            |       不确定        |
|              `reduce(func)`              |      并行整合RDD中的所有元素       |                         rdd.reduce((x,y)=>x+y)                         |          9          |
|            `fold(zero)(func)`            |     和reduce相同,但需要初始值      |                        rdd.fold(0)((x,y)=>x+y)                         |          9          |
|          `aggregate(zeroValue)`          |     和reduce相似,返回类型不同      | rdd.aggregate(0,0)((x,y)=>(x.1_1+y,x._2+1),(x,y)=>x._1+y._1,x._2+y._2) |          9          |
|             `foreache(func)`             |      对rdd中的每一个元素使用       |                           rdd.foreach(func)                            |                     |
> rdd为{1,2,3,3} 示例scala

- RDD类型的转换
  - `JavaDoubleRDD`,`JavaPairDD`
    - 这两个类专门处理特殊类型的RDD
  - 专门类型的函数接口如下表

|            函数名            |              等价函数               |                用途                |
|:----------------------------:|:-----------------------------------:|:----------------------------------:|
|  `DoubleFlatMapFunction<T>`  |   `Function<T,Interable<Double>>`   | 用于flatMapToDouble,生成DoubleRDD  |
|     `DoubleFunction<T>`      |        `Function<T,Double>`         |   用于MapToDouble,生成DoubleRDD    |
| `PairFlatMapFunction<T,K,V>` | `Function(T,Iterable<Tuple2<k,V>>)` | 用于flatMaoToPair,生成PairRDD<K,V> |
|    `PairFunction<T,K,V>`     |      `Function<T,Tuple2<K,V>>`      |   用于mapToPair,生成PairRDD<k,V>   |

- 持久化(缓存)
  - Spark RDD 是惰性求值的
  - 持久话别

|        级别         | 使用的空间 | cpu  | 内存 | 磁盘 |
|:-------------------:|:----------:|:----:|:----:|:----:|
|     MEMORY_ONLY     |     高     |  低  |  是  |  否  |
|   MEMORY_ONLY_SER   |     低     |  高  |  是  |  否  |
|   MEMORY_AND_DISK   |     高     | 中等 | 部分 | 部分 |
| MEMORY_AND_DISK_SER |     低     |  高  | 部分 | 部分 |
|      DISK_ONLY      |     低     |  高  |  否  |  是  |
> 如果数据在内存中放不下会溢出到磁盘上,在内存中存放序列化后的数据
> 在级别后加上`_2`来把持久数据存为两份

