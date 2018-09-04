# spark安装
- 安装配置java jdk(1.8)
- 安装配置hadoop(2.9.0)
  - 配置伪分布式
1. 下载 解压
2.  配置profile
  - 例子
  ```
  export SPARK_HOME=/home/work/spark-2.2.1-bin-hadoop2.7
  export PATH=$PATH:$SPARK_HOME/bin
  ```
  - `source /etc/profile`
    - 生效
3. 验证
  - `spark-shell`

4. 运行例子
 - `./bin/run-example SparkPi 2>&1 | grep "Pi is roughly"`
   - `grep` 过滤不必要输出信息
   - 输出结果
     - Pi is roughly 3.1390156950784753


