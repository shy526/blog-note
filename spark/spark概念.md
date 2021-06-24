# spark

## 特点

- 快速而通用的集群计算平台
- 扩展了Mapreduce,支持更多交互模型
- 基于内存,计算速度块
- 接口丰富(java,Scala)
  - 运行在hadoop集群上
  - Cassandra在内的任意Hadoop数据源

## Spark 组件

- Spark Core
  - 实现了Spark基本功能
    - 任务调度,内存管理,错误恢复,与存储系统交互等
    - 弹性分布式数据集(RDD)
      - 分布在多个计算节点上可以并行操作的元素合集
      - 主要的编程抽象
- Spark SQL
  - 操作结构化数据
    - hive的sql,sql查询数据
  - 支持多种数据源
    - hive表
    - Parque
    - json
  - 使用sql对RDD进行数据分析
  - Spark 1.0 引入

- Spark Streaming
  - 实时数据进行流式计算的组件
    - 服务器日志,消息队列
  - 提供操作数据流的API
- MLlib
  - 机器学习功能的程序库
    - 提供机器学习算法
      - 分类,回归,聚类,协同过滤
      - 模型评估,数据导入等
    - 机器学习原语
      - 梯度下降优化算法
- Graphx
  - 操作图的程序库
    - 并行的图计算

## spark-shell demo

- 输出行数
  1. `spark-shell`
      - 进入Scala客户端
  2. `val lines=sc.textFile("README.md")`
      - 创建一个RDD
  3. `lines.count()`

  4. `lines.first`

>[sparkui地址](http://ip:4040)
>>sparkshell(驱动器) -启动时自动创建-> SparkContext(sc变量) -访问-> spark -创建-> RDD -->count()(不同节点统计不同部分的行数)
>>> 通过 `sc` 查看类型
>>>>? 显示结果`org.apache.spark.SparkContext = org.apache.spark.SparkContext@2d02a066`

- 单词筛选
  1. `val lines=sc.textFile("README.md")`
  2. `val pythonlines=lines.filter(line=>line.contains("Python"))`
      - 筛选Python
  3. `pythonlines.first()`

> fiter方法会在集群上并行执行

#### java 中使用spark

- 导入jar
