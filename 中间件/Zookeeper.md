# zookeeper 概述
- 分布式服务框架，主要是用来解决分布式应用中常见的问题
    - 集群中数据的 一致性、统一命名服务、集群中机器节点的状态同步服务、集群管理、分布式应用配置项的管 理等

- 根据Google的一篇论文《The Chubby lock Service for loosely coupled distributed system》做的开源实现。

## 安装
- 关闭防火墙
    - `service iptables stop`
- 解压
    - `tar -xvf 文件名`
- 配置
    - 进入`Zookeeper/conf`
        - 拷贝个`zoo_sample.cfg`
    - 属性
        - `dataDir`
            - 是存放zookeeper集群环境配置信息
            - 默认:`/tmp/zookeepe`
                - 但是会被清空
                - 修改即可
                - > 指定的目录需要手动创建
        - `clientport`
            - 客户端链接服务端
                - 默认:`2181`

- 配置
    - 2888原子广播端口，3888选举端口
    - 有几个节点，就配置几个server,

```
server.1=192.168.234.10:2888:3888

server.2=192.168.234.11:2888:3888

server.3=192.168.234.12:2888:3888
```

## 配置文件说明
```cnf
tickTime: zookeeper中使用的基本时间单位, 毫秒值.
dataDir: 数据目录. 可以是任意目录.
dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和dataDir相同的设 置.
clientPort: 监听client连接的端口号
initLimit: zookeeper集群中的包含多台server, 其中一台为leader, 集群中其余的server为 follower. initLimit参数配置初始化连接时, follower和leader之间的最长心跳时间. 此时该参 数设置为5, 说明时间限制为5倍tickTime, 即5*2000=10000ms=10s.
 syncLimit: 该参数配置leader和follower之间发送消息, 请求和应答的最大时间长度. 此时该 参数设置为2, 说明时间限制为2倍tickTime, 即4000ms.
```

## 服务指令

1. 启动ZK服务
    - `sh bin/zkServer.sh start`
2. 查看ZK服务状态
    - `sh bin/zkServer.sh status`
3. 停止ZK服务
    - `sh bin/zkServer.sh stop`
4. 重启ZK服务
    - `sh bin/zkServer.sh restart`
5. 连接zk客户端
    - `sh zkCli.sh -server 127.0.0.1:2181`

## 客户端指令

1. 显示根目录下、文件
    - `ls /`
    - 使用 ls 命令来查看当前 ZooKeeper 中所包含的内容
2. 显示根目录下、文件
    - `ls2 /`
    - 查看当前节点数据并能看到更新次数等数据
3. 创建文件，并设置初始内容
    - `create /zk "test"`
    - 创建一个新的 znode节点“ zk ”以及与它关联的字符串
4. 获取文件内容
    - `get /zk`
    - 确认 znode 是否包含我们所创建的字符串
    - 信息详细
    ```
    hello1609   数据
    cZxid = 0x2
    ctime = Mon Mar 14 10:32:08 PDT 2016  创建此节点的时间戳
    mZxid = 0x2
    mtime = Mon Mar 14 10:32:08 PDT 2016  修改此节点数据的时间戳
    pZxid = 0x2
    cversion = 0 dataVersion = 0  数据版本号，每当数据发生变化，版本号递增1
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 9  数据大小
    numChildren = 0  子节点数量
    ```
5. 修改文件内容
    - `set /zk "zkbak" `
    - 对 zk 所关联的字符串进行设置
6. 删除文件
    - `delete /zk`
        - 只能删除没有子节点的节点
    - `rmr /zk`
7. 退出客户端
    - `quit`
8. 帮助命令
    - `help`


## 节点类型

- 普通持久节点
    - `create  /zk01 helllo`
- 普通临时节点
    - 创建此临时节点的客户端失去和zk连接后，此节点消失
    -  `create -e /zk02 12345`
- 顺序持久节点
    - 会根据用户指定的节点路径，自动分配一个递增的顺序号
    - `create -s /zk03  12234`
- 顺序临时节点
    - `create -s -e /zk02 12354`

## API
-  `ZooKeeper(String connectString, int sessionTimeout, Watcher watcher,long sessionId, byte[] sessionPasswd, boolean canBeReadOnly)`
   - connecString *
     -  ip:port格式的字符串,多个使用,号相隔
   - sessionTimeout
     - 超时时间
     - 影响心跳间隔
    - watcher *
      - 监听器
    - sessionId
    - sessionPasswd
    -  canBeReadOnly
      - 是否只读

> *号为必须要的参数

## ACL权限设置
- `addauth digest username:password`
  - 添加一个用户
- 使用 `setACL digest username:password`
   - 需要对密码进行 Base64(SHA1(password))
   >   指令不需要 但是设置权限的时候需要

```java
  /**
    *  添加一个用户
    * @param zooKeeper 客户端
   * @param idPassword 用户名:密码
   * @return zooKeeper客户端
   * @throws NoSuchAlgorithmException
   */
  public  ZooKeeper generateDigest(ZooKeeper zooKeeper,String idPassword) throws NoSuchAlgorithmException {
       String parts[] = idPassword.split(":", 2);
       byte digest[] = MessageDigest.getInstance("SHA1").digest(idPassword.getBytes());
      zooKeeper.addAuthInfo("digest",parts[0].concat(":").concat(base64Encode(digest)).getBytes());
      return zooKeeper;
  }
```
> "auth"方式,必须保证有一个用户被添加 否则抛出异常

