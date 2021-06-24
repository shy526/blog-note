# hadoop 伪分布式配置

## 配置主机名

- `vim  /etc/sysconfig/network`
  - 修改hostname
- 执行 `hostname xxx`
- `vim /etc/hosts`
  - 增加对应ip

## core-site.xml

- `fs.defaultFS`
  - namenoded的地址
  - `hdfs://localhost/`
- `hadoop.tmp.dir`
  - hadoop运行时产生临时文件的存放目录
  - 默认地址:/tmp

## hdfs-site.xml

- `dfs.replication`
  - hdfs保存数据副本的数量
    - 默认:`3`
- `dfs.permissions`
  - 操作权限
  - `false`表示任何

## mapred-site.xml

- 没有初始文件
  - cp mapred-site.xml.template mapred-site.xml
    - 执行cp命令复制
- `mapreduce.framework.name`
  - 指定`mapreduce`运行在`yarn`上

## yarn-site.xml

- `yarn.resourcemanager.hostname`
  - resoucemanager的主机名
- `yarn.nodemanager.aux-services`
  - NodeManager获取数据的方式
  - 值:`mapreduce_shuffle`

## hadoop-env.sh

- 指定javahome 根目录

## slaves

- 集群中所有datanode的主机名

## 启动

- `hdfs namenode -format`
  - 格式化文件系统
- hadoopx.x.x/sbin目录下
  - `./start-all.sh`
    - 需要密码
  - `jps`
    - 校验集群是否正常启用
      - 7815 NameNode
      - 7913 DataNode
      - 8106 SecondaryNameNode
      - 8350 NodeManager
      - 8254 ResourceManager
      - 8943 Jps

## web管理

- `ip:50070`
  - namenode
- `ip:8088`
  - yarn(资源管理器)
- `ip:19888`
  - 资源服务器

## ssh 免密登陆

- `ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa`
  - 根据空指令生成ssh密钥
- `ssh-keygen -t rsa`
  - 两个执行一个
  - 需要`700`,`644`权限
    - `chmod 700 ~/.ssh`
    - `chmod 644 ~/.ssh/authorized_keys`
- `ssh-copy-id –i ~/.ssh/id_rsa.pub root@免密登录的主机`

- 例

```xml
<property>
   <name>fs.default.name</name>
   <value>localhost:9000</value>
 </property
```
