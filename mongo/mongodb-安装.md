# mongodb

## 安装

1. [下载](https://www.mongodb.com/download-center?jmp=nav#community)
    - 使用`wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.6.4.tgz`指令或者使用`Xshell`之类的

2. 解压
    - `tar -xvf mongodb-linux-x86_64-3.6.4.tgz`

3. 配置profile
    - `vim /etc/profile`

      ```profile
         export MONGODB_HOME=/home/work/mongodb/mongodb-linux-x86_64-3.6.4
         export PATH=$MONGODB_HOME/bin:$PATH
       ```

    - `source /etc/profile`
      - 使配置生效

4. 创建数据库目录和日志目录
    - `MONGODB_HOME`目录执行`mkdir -p  data/db`
    - `MONGODB_HOME`目录执行`mkdir -p  logs`

5. 验证安装是否正确

   - `mongod --dbpath ./data/db`

6. 创建并配置`mongodb.conf`
   - `MONGODB_HOME`目录执行 `vim mongodb.conf` 配置如下

      ```conf
      #数据库文件存放目录
      dbpath=/home/work/mongodb/mongodb-linux-x86_64-3.6.4/data/db
      #日志文件存放地址
      logpath=/home/work/mongodb/mongodb-linux-x86_64-3.6.4/logs/mongodb.log
      #端口
      pot=27017
      #以守护线程方式启动
      fork=true
      #日志开启追加方式
      logappend=true
      # PIDFile
      pidfilepath=/home/work/mongodb/mongodb-linux-x86_64-3.6.4/mongo.pid
      #http接口 默认关闭
      #nohttpinterface=true
      #声明集群分片 默认端口27018
      shardsvr=true
      #认证
      #auth=true
      ```

   - [参数说明(有些版本原因在里面)](http://blog.csdn.net/fdipzone/article/details/7442162)
     - 测试
     - `mongod --config /home/work/mongodb/mongodb-linux-x86_64-3.6.4/bin/mongodb.conf`
     - `mongo`
     - 进入mongoShell

7. 设置服务并且设置开机启动
    - `vim /etc/rc.d/init.d/mongod`写入如下内容

    ```shell
    ulimit -SHn 655350
         #!/bin/sh
         # chkconfig: - 64 36
         # description:mongod
        case $1 in
        start)
        /home/work/mongodb/mongodb-linux-x86_64-3.6.4/bin/mongod  --maxConns 20000  --config /home/work/mongodb/mongodb-linux-x86_64-3.6.4/bin/mongodb.conf
        ;;
 
        stop)
        /home/work/mongodb/mongodb-linux-x86_64-3.6.4/bin/mongo 127.0.0.1:27017/admin --eval "db.shutdownServer()"
        ;;
 
        status)
        /home/work/mongodb/mongodb-linux-x86_64-3.6.4/bin/mongo 127.0.0.1:27017/admin --eval "db.stats()"
        ;;
        esac
 
    ```

   - `chmod +x /etc/rc.d/init.d/mongod`
     - 添加权限
   - `service mongod [stop|start|status]`
     - 测试即可
   - `chkconfig mongod on`
     - 开机自启
