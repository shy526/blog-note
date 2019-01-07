# 搭建elasticsearch(windos)


## 搭建elasticsearch
- 修改`{elasticsearch_home}/config/elasticsearch.yml`

```yml
#集群名称唯一
cluster.name: my-test
#节点名称 唯一
node.name: node-1
# ip
network.host: 192.168.0.74
# http 端口 
http.port: 9200
# 集群 端口 
transport.tcp.port: 9300
# 集群机器的集合
discovery.zen.ping.unicast.hosts: ["192.168.0.74:9300", "192.168.0.74:9301", "192.168.0.74:9302"]
# 是否允许跨源
http.cors.enabled: true
# 允许请求的来源
http.cors.allow-origin: "*"
```
- 启动
 `{elasticsearchHome}/bin/elasticsearch.bat`

- 测试
    - `http://{network.host}:{http.port}/` 
        - 查看是否配置成功


## 搭建 `elasticsearch-head`
1. `git clone git://github.com/mobz/elasticsearch-head.git`
2. `cd elasticsearch-head`
3. `npm install`
4. 修改文件
    - `{elasticsearch-head_home}/_site/app.js`
        - `this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://localhost:9200";`
            - 修改为`network.host`
    - `{elasticsearch-head_home}/Gruntfile.js`
        - 该文件可以修改端口号等配置信息
```js
connect: {
    server: {
        options: {
            port: 9100,
            base: '.',
            keepalive: true
        }
    }
}
```

5. `npm run start`
6. 打开 `http://localhost:9100/`
> 需要`node`环境


## 搭建 `logstash`

- 创建配置文件
```config
input {
    stdin {
    
    }
    jdbc {
       # mysql 数据库链接,mybatis为数据库名
      jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/weidian_cms"
      # 用户名和密码
      jdbc_user => "root"
      jdbc_password => "root"
      # 驱动 修改为mysql连接包位置
      jdbc_driver_library => "D:\javaEx\es\logstash-6.5.4\my-config\mysql-connector-java-5.1.21.jar"
      # 驱动类名
      jdbc_driver_class => "com.mysql.jdbc.Driver"
      jdbc_paging_enabled => "true"
      jdbc_page_size => "50000"
      # 执行的sql 文件路径+名称 
      statement_filepath => "D:\javaEx\es\logstash-6.5.4\my-config\jdbc.sql"
      # 设置监听间隔  各字段含义（由左至右）分、时、天、月、年，全部为*默认含义为每分钟都更新
      schedule => "* * * * *"
    }
}
output {
    elasticsearch {
        # ES的IP地址及端口
        hosts => ["192.168.0.74:9200"]
        # 索引名称 可自定义
        index => "test"
        # 需要关联的数据库中有有一个id字段，对应类型中的id
        document_id => "%{id}"
        document_type => "test"
    }
    stdout {
        # JSON格式输出
        codec => json_lines
    }
}
```
> 详细查看文档
- 启动
    - `{logstash_home}/bin/logstash -f {logstash.conf_path}`
