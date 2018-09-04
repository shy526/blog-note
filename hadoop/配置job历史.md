
## yarn-site.xml

```xml
<!-- 日志聚合功能 日志存储在hdfs上(默认地址${fs.defaultFS}/tmp/logs/${user}/logs)-->
<property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
</property>
<!-- 日志本地化目录(当开启日志聚合功能时,不会生成次日志,只能在web端查看) -->
<property>
    <name>yarn.nodemanager.log-dirs</name>
    <value>/userlogs</value><!-- 默认:{HADOOP_HOME}/logs/userlogs -->
</property>

<!-- 日志聚合目录 -->
<property>
        <name>yarn.nodemanager.remote-app-log-dir</name>
        <value>/tmp/logs</value>
</property>
```

##  mapred-site.xml

```xml
<!-- 设置jobhistoryserver 没有配置的话 history入口不可用 -->
<property>
    <name>mapreduce.jobhistory.address</name>
    <value>localhost:10020</value>
</property>

<!-- 配置web端口 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>localhost:19888</value>
</property>

<!-- 配置正在运行中的日志在hdfs上的存放路径 -->
<property>
    <name>mapreduce.jobhistory.intermediate-done-dir</name>
    <value>/history/done_intermediate</value>
</property>

<!-- 配置运行过的日志存放在hdfs上的存放路径 -->
<property>
    <name>mapreduce.jobhistory.done-dir</name>
    <value>/history/done</value>
</property>
```

## jobhistory的启动与停止
- 在`{hadoopHome/sbin/}`目录下执行
-  `mr-jobhistory-daemon.sh start historyserver`
-  `mr-jobhistory-daemon.sh stop historyserver`