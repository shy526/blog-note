# hadoop

1. 安装jdk

2. 安装hadoop
   - 下载压缩包
     - `tar -xvf`
       - 解压压缩包
     - `./bin hadoop version`
       - 查看是否安装正确

3. 配置 `profile`
    - `vim /etc/profile`

    ```profile
    export HADOOP_HOME=/home/work/hadoop-2.9.0
    export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
    ```

    - `hadoop version`
      - 检查是否安装正确

4. 运行例子
   1. `mkdir input`
      - 创建输入文件夹
   2. `cp etc/hadoop/*.xml input`
      - 拷贝配置文件
   3. `bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]'`
      - 运行demo 指定输入输出
   4. `cat output/*`
       - 查看结果
   - 运行目录为hadoop根目录
