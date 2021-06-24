# Redis

- k-v存储系统,或者说是k-v数据库
- C语言开发,提供不同编程语言的客户端
- 提供丰富的数据类型
- 存储介质是内存,提供高效的读取
- 对持久化的支持

## Redis 安装

- Redis没有其它外部依赖安装简单

1. `wget http://download.redis.io/releases/redis-4.0.9.tar.gz`
    - 下载解压
2. 进入redis解压目录,进行编译
    - 执行`make`
3. 执行`make install`
4. 启动服务
    - `redis-server`
    - `redis-server 配置文件路径`
        - 指定配置文件启动

   ![ ](https://i.imgur.com/2yhnFlA.png)

5. 执行`redis-cli`
    - 打开redis客户端
    - `exit`
        - 退出客户端
6. 测试是否正常
    - redis客户端下执行`ping`
        - 回应`Pong`成功
7. 关闭redis服务
    - 强行终止Redis可能导致数据丢失
    - `redis-cli shutdown`
    - Redis可以妥善处理SIGTERM信号
        - `kill Redis进程的PID`
          - 效果同shutdown

## 配置

- redis.conf
  - redis的配置文件

- `daemonize`
  - Redis服务是否以后台线程运行
  - 默认`no`

- `port`
  - 50行
  - 端口号
  - 默认`6379`
- `maxmemory`
  - 453行
  - Redis最大可使用内存容量
  - 如果超过这个值,就会动用淘汰策略
  - Redis作为**db**使用时,不要设置此项
    - 数据库不能容忍跌势数据
  - Redis作为**cache**,则可以使用
  - xxmb
- `requirepass`
  - 为redis添加访问密码
  - `auth 密码`
    - 进入cli进行授权

## 开机自启动

- `cp {redis_home}/utils/redis_init_script redis
  - 复制一份
- 修改`redis`文件
  - CONF 指向配置文件路径
  - 执行过`make install`,不用配置别的东西
- 添加`chkconfig`到文件中,如下

```shell
# chkconfig: 2345 10 90  
```

- `mv redis /etc/rc.d/init.d/redis`
  - 移动文件
- `chkconfig --add redis`
  - 添加 服务
- `chkconfig redis on`
  - 开机启动
- 测试
  - `service redis start`
  - `service redis stop`

## 基础指令

- `help 指令`
  - 查看指令信息

- `set k v`
  - 向redis中插入数据

- `get k`
  - 获取k对应的value

- `strlen k`
  - 查看对应value的长度
- `exists k`
  - 判断k值是否存在
  - 1:存在
  - 0:不存
- `del k [k1 [k2] ...]]`
  - 删除k-v对

- `keys 通配符`
  - ?:匹配一个
  - *:任意字符
  - []:匹配括号间的任一字符
  - \x 匹配转义字符

- `mset k v [k1 v1[k1 v1 ...]]`
  - 设置多个k-v对

- `mget k [k1 [k2] ...]]`
  - 取多个k的v

- `append k xxx`
  - 对v值进行追加
    - xxx:任意字符

- `type k`
  - 查看value的类型

- `flushdb`
  - 清空当前数据库

- `flushall`
  - 清空所有数据库

- `select xxx`
  - 切换数据库
  - xxx:数据库编号
  - 默认为16(0-15)个数据库

- `incr k`
  - 使value 自增

- `decr k`
  - 使value 自减

- `incrby k 步长`
  - 设置递增步长

- `decrby k 步长`
  - 设置递减步长

- `incrbyfloat k 浮点步长`
  - 设置递增步长

- `expire k 秒数`
  - 设置缓存失效时间
        - 单位为秒

- `ttl k`
- 查看剩余存活时间

- `pexpire`
  - 设置缓存失效时间
        - 单位为毫秒

## 数据类型

- Redis支持多种数据类型
  - 字符串类型:String
  - 散列类型:Hash
  - 列表类型:list
  - 集合类型:set
  - 有序集合

### 散列

- 用来存储对象信息

#### 散列-相关指令

- 缩写介绍:
  - k key
  - v value
  - f field
- `hset k f v`
  - 设置散列类型的字段
- `hget k f`
  - 获取莫个散列类型的字段值

- `hmset  k f v [f v [f v ...]]`
  - 设置多个散列类型的字段

- `hmget k f [f [f ...]]`
  - 获取多个个散列类型的字段值

- `hgetall k`
  - 获取所有字段及字段值

- `hdel k f`
  - 删除指定字段

- `hkeys k`
  - 获取所有字段

- `hvals k`
  - 获取所有字段值

- `hlen`
  - 查看指定key字段的个数

### 列表

- 采用双端列表
  - 两端数据查找速度快,中间数据查找速度慢
- 存储一个有序的字符串列表
- 常用操作是向列表添加元素,获取莫一个片段

#### 列表-相关指令

- `lpush k v [v] ...`
  - 向列表添加元素(左侧)

- `rpush k v [v] ...`
  - 向列表添加元素(右侧)

- `lpop k`
  - 移除首元素(左侧),并将值返回

- `rpop k`
  - 移除首元素(左侧),并将值返回

- `llen k`
  - 获取列表中元素个数

- `lrange k s e`
  - 获取索引s-e的数据
  - 负数从右开始
  - 正数从左开始
  - `lrange k 0 -1`
    - 获取所有

- `lrem k s v`
  - s>0
    - 从左侧开始删除 s 个 为v的值的元素
  - s<0
    - 从右侧
  - s=0
    - 删除所有为v的元素

- `lindex k s`
  - 查看指定索引的值

- `lset k s v`
  - 替换索引为s的值为v

- `linsert k [before/after] v v1`
  - before
        - 从左向右，在第一个为v的元素前插入v1
    - after
    - 从右向左,，在第一个为v的元素
后插入v1

### 集合

- 集合中每个元素都是不同的
- 没有顺序
- 同java的set类型
- 可以进行 交集,并集,差集运算

#### 集合类型指令

- `sadd k v [v1 [v2...]]`
  - 向集合中插入元素
    - 不存在则创建

- `srem k v`
  - 删除指定元素

- `smembers k`
  - 获取集合中所有元素

- `sismember k v`
  - 判断v是否在集合k中

- `sdiff k k1`
  - 差集运算
  - 返回k中不属于k1的v

- `sinter k k1 [k2 ...]`
  - 取多个集合的交集

- `sunion k k1 [k2 ...]`
  - 取多个合集的并集

- `scard k`
  - 获取集合中元素的个数

### 有序集合

- 底层是用skipLiset来实现
- 按照分数,分许相同时按照`"0"＜"9"＜"A"＜"Z"＜"a"＜"z"`进行排列
  - 中文取决于编码方式
- 有序集合与列表
  - 相似
    - 都是有序的
    - 两者都可以获取莫以范围的元素
  - 不容
    - 列表通过链表实现,有序集合通过散列表和跳跃表实现(时间复杂度(log(n))
    - 列表中不能调整莫个元素的位置,有序列表可以
    - 有序集合要比列表更耗费内存

### 有序集合类型指令

- `zadd k s e [s e...]`
  - 向有序集合中插入 元素(e)及对应分数(s)

- `zscore k e`
  - 获取元素的分数

- `zrange k s e [withscores]`
  - 获取s-e的元素
  - withscores
    - 获取元素以及分数

- `zrangebyscore [(]ss [[(]se [+inf/-inf]][WITHSCORES] [LIMIT offset count]`
  - 获取ss-se分数的数据
  - `(`:表示不带`=`号
  - +inf:>=
  - -inf:<=
  - limit: 截取结果

- `zrevrangebyscore k ss se [LIMIT offset count]`
  - 获取指定区间的数据

- `zincrby k s e`
  - 给e元素+s分

- `zcard k`
  - 获取有序集合中的元素的数量

- `zcount ss se`
  - 获取分数范围内的元素个数

- `zrem k e`
  - 删除指定元素

- `zremrangebyrank s e`
  - 按照从小到大顺序，删除指定索引范围内的数据

- `zremrangebyscore ss se`
  - 按照从小到大顺序，删除指定分数范围内的数据

- `zrank k e`
  - 查看排名

## 单机多实例

1. 拷贝redis.conf
    - cp redis.conf redis-端口号.fonf

2. 修改redis.conf中port 的值

3. `redis-server xxx.conf`
    - 以指定的配置文件启动服务
    - 默认以`/usr/local/redis.conf`启动

4. `redis-server -p 端口号`
    - 进入指定端口号的Redis

## jedis API

- Redis java客户端
- 容易使用
  - 方法名和指令名及指令作用都是一
样的
- 兼容redis 2.8x和3.x.x

- [github 源码](https://github.com/xetorthio/jedis "git")

- 简单示例

```java
public class JedisTest {
  @Test
  public void jTest() {
     Jedis j=new Jedis("192.168.119.139", 6379);
     j.set("test", "you,sb");
     String str=j.get("h");
     System.out.println(str + "<==str");
     j.close();
  }
}
```

## Shard数据分片

- Jedis 利用池化思想,管理多个redis服务实例
      - 对k-v数据进行存储和管理
- 只需要通过API进行key-value做CRUD即可
- 一致性哈希算法
  - 满足单调性和平衡性
  - 避免数据倾斜,数据均分布

! [](https://i.imgur.com/to2zDWQ.png)

- Jedis 实现 示例

```java
//连接池设置
JedisPoolConfig config=new JedisPoolConfig();
//redis服务器列表
List<JedisShardInfo> serverList=new ArrayList<>();
serverList.add(new JedisShardInfo("192.168.234.206",6379));
serverList.add(new JedisShardInfo("192.168.234.206",6380));

ShardedJedisPool pool=new ShardedJedisPool(config, serverList);
ShardedJedis jedis=pool.getResource();

for(int i=0;i<1000;i++){
    jedis.set(i+"",i+"");
}
//用完后将jedis 连接还到池子
pool.returnResource(jedis);
```

## 主从复制

1. 建立redis服务器作为 Master节点
2. 修改redis.conf
    - `daemonize yes`
3. 建立redis服务器作为Slave节点
4. 修改redis.conf
    - `daemonize yes`
    - `slaveof MasterIp Master端口`
5. 启动Master和Salve节点的服务器
6. Master/Salve执行:`redis-cli`
   - `info replication`
     - 查看即可

> slave不能执行写操作

## Redis 哨兵

- Redis提供了sentinel（哨兵）机制
- 通过sentinel模式启动redis后，自动监控master/slave的运行状态
- 基本原理是：心跳机制+投票裁决
- Master/Salve会在Master挂掉后,发生转换
- 当引入哨兵机制后，当主从关系发生变化时，sentienl.conf里的配置文件监听的Master ip 地址也会发生变化

## 哨兵配置

- 确保完成主从复制

1. 编辑Sentinel.cnf
2. `sentinel monitor mymaster MasterIP MasterPort 最少票数`
    - Master/Slave都需要修改
3. 启动Master/Slave,redis服务
4. 启动Master/Slave哨兵
    - redis安装目录执行
        - `./src/redis-sentinel sentinel.conf`

5. `./redis-cli -p shutdown 哨兵端口`
    - 停止哨兵

### 配置参数补充

- `port <sentinel-port>`
  - 哨兵实例运行所在的端口（默认26379）

- `sentinel failover-timeout <master-name> <milliseconds（默认值3分钟）>`
- 表示如果15秒后,mymaster仍没活过来，则启动failover，从剩下的slave中选一个升级为master

## Redis 集群

- Redis 集群没有并使用传统的一致性哈希来分配数据，而是采用另外一种叫做哈希槽 (hash
slot)的方式来分配
- redis cluster 默认分配了 16384(2^14) 个slot
- 集群的最大节点数量也是 16384 个（推荐的最大节点数量为 1000 个)
  - 每个主节点可以负责处理1到16384个槽位

>> 必须要3个或以上的主节点，否则在创建集群时会失败，并且当存活的主节点
数小于总节点数的一半时，整个集群就无法提供服务了。

## 集群配置

1. 配置 Ruby
    - 执行`yun install ruby`
    - `gem install redis`
        - 安装Redis相关接口

2. `ruby -v`
    - 确认安装
![ ](https://i.imgur.com/Np4JtYc.png)

3. 配置 如下`reids.conf`

      ```conf
          port 7000 //端口7000
          bind 本机ip //默认ip为127.0.0.1 需要改为其他节点机器可访问的ip 否则创建集群时无法
          访问对应的端口，无法创建集群
          daemonize yes //redis后台运行
          pidfile /home/software/redis-3.0.7/cluster/7000/redis_7000.pid //pidfile文件对应7000
          cluster-enabled yes //开启集群 把注释#去掉
          cluster-config-file nodes-7000.conf //集群的配置 配置文件首次启动自动生成
          appendonly yes //aof日志开启 有需要就开启，它会每次写操作都记录一条日志
      ```

    > 需要将主从配置项注释掉 `slave of`

4. `cp redis-trib.rb /usr/local/bin/`

5. `./redis-trib.rb create --replicas 1 ip:端口号...`
     - 之后输入`yes`

6. `redis-cli -h ip -c -p 端口`
    - 进入集群节点
    - `info replication`
        - 查看

## 相关链接

[Redis 官网](http://redis.io/ )
[Redis 指令](https://redis.io/commands)
