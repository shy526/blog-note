# 用于配置API
- `org.apache.hadoop.conf.Configuration`
  - 代表配置属性和取值的集合
  - 属性用String命名,值的类型是多样的
  - 覆盖原则
    1. 后加载覆盖先加载
    2. final的不会被覆盖
  - 变量扩展
    - 可以是其他属性也可以是系统属性的值
      - 系统属性优先级大于其他属性
      - 适用于jvm参数 -Dproperty=value
    - `${key}`
- 使用`hadoop fs -conf 配置文件目录 指令`
  - 来连接hadoop
> HADOOP_USER_NAME 环境变量来显示设定Hadoop用户名

## 辅助类
- `GenericOptionsParser`
  - 解释命令行选项
- `Tool`
  - 一个运行`ToolRunner`的接口
- `ToolRunner`
  - 内部调用`GenericOptionsParser`
