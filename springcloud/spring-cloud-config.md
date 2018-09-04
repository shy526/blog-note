# spring cloud Config

# server
- 使用本地git路径
  - uri: file:d:///Users/admin/Desktop/bower-demo/spring-cloud-config
- 占位符支持
  - {application}
  - {profile}
  - {label}
- 匹配模式
  - 匹配符 `*`
    - `*`置前且在首个参数需要用引号包裹
  - 以这种方式配置`{application}/{profile}`
    - `*{spring.cloud.config.application.name}.`,`{spring.cloud.config.application.name}*/{spring.cloud.config.profile}*`
  - `pattern`缺省时,默认匹配:`{spring.cloud.config.application.name}/*`

```yml
spring:
  cloud:
    config:
      server:
        git:
          uri: file:d:///Users/admin/Desktop/bower-demo/spring-cloud-config
          username: username
          password: password
          repos:
            # 自定义的名字
            local:
              # 需要匹配的application.name
              pattern: "*local"
              uri: d:///Users/admin/Desktop/bower-demo/resources/local
            github:
              pattern: github*/dev*,*github*/dev*
              uri: d:///Users/admin/Desktop/bower-demo/resources/github
            svn:
              uri: d:///Users/admin/Desktop/bower-demo/resources/svn
```
## 仓库属性
- searchPaths
  - 配置子目录
> 使用占位符的方法'{application}'

- `cloneOnStart`
  - 启动时是否克隆仓库
- `force-pull`
  - 强制拉取,默认为false
  - 应对无法拉取的情况
- `order`
  - 仓库优先级
    - 数值越大越高
- `basedir`
  - 可以用来设置拉取文件后存放的地址
> 默认地址 liunx:`/tmp/config-repo-<randomid>`

### 设置
1. `spring.profiles.active = native`
2. `spring.cloud.config.server.native.searchLocations=file:{url}`
> 默认为classpash:url

### 属性覆盖
- `spring.cloud.config.server.overrides`
  - 设置覆盖的属性

### 自定义仓库
- 需要实现EnvironmentRepository 接口
- Ordered 实现order接口
  - 可以控制优先级


### 健康指标
- 禁用健康指标
  - `spring.cloud.config.server.health.enabled=false`

## 加密解密

### 对称加密
- 需要jce 默认情况不存在
[jce](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)
- 解压,复制到该目录下
  - `C:\Program Files\Java\jre1.8.0_161\lib\security`
- [curl](https://curl.haxx.se/download.html)
  - 用`Postman`测试直接跳过
  - 配置环境
    - CURL_HOME=根目录
    - Path追加;%CURL_HOME%\I386
    - Dalston SR3 有`bug`
- 添加 属性或者添加个环境变量`ENCRYPT_KEY`
  ```yml
  encrypt:
    key: 12346454
  ```
- 测试
  - 加密
    - curl -X POST http://localhost:8090/encrypt -d ccxh
  - 解密
    - curl -X POST http://localhost:8090/decrypt -d 8880828a5d16bab74e8a8736d9e8a2be817b5519bff4cef15fc0f04d40f1c338
### 非对称加密
- 使用密钥库
1. 生成密钥库
  - `keytool -genkeypair -alias mytestkey -keyalg RSA -dname "CN=Web Server,OU=Unit,O=Organization,L=City,S=State,C=US" -keypass changeme -keystore server.jks -storepass letmein`
    - 生成文件在执行目录下
2. 添加配置
  ```yml
  encrypt:
  keyStore:
    #文本位置
    location: classpath:/server.jks
    # 解锁库的密码
    password: letmein
    # 识别码
    alias: mytestkey
    # 私钥+解密密码
    secret: changeme
  ```
> Edgware SR3 无法启动
> `spring.cloud.config.server.encrypt.enabled =false` 关闭服务端解密

## 提供文件服务
1. 如何访问文件
  - /{name}/{profile}/{label}/{path}
    - /{应用名}/{标签环境}/{版本号}/{文件名}
2. 使用占位符号
  - 通过占位符为不同环境赋予不同的值
    - `${xxx.xx}`
    - /{name}/{profile}/是指{name}/{profile}.yml,{name}/{profile}.proptees 从那个一个中读取占位符\
    - 在同一个文件中可以使用
    ```yml
    name: x
    #default
    ---  # 截断
    spring:
      # profiles 指定版本号
      profiles: dev
    name: x1

    ```

## 嵌入式服务
- `spring.cloud.config.server.prefix`
  - 更改url的访问方式
  - `spring.cloud.config.server.prefix=/config`
    - 改为访问`localhost:8888/config`
- `spring.cloud.config.server.bootstrap`
  - 表示从git中读取
  - 默认false
  - 作为嵌入式服务时,官方建议开启


## client
- 加载规则
  1. 文件名相同优先加载properties格式的配置文件
  2.  默认只支持applicationName-profile这样的命名风格
  3.  默认加载application
      - 完全匹配的覆盖application的属性
  4. 默认请求 localhost:8888
## 加密
 - `{cipher}密文`
## 多文件加载
-  `application.name`和`spring.cloud.config.profile`
  - 都支持多个参数
  - 匹配规则每个name便利profile
    - 后者覆盖前者
    - 测试版本`Camden.SR7`

## 相关属性
- bootstarap.yml
  - 处于引导上下文阶段
  - `spring.cloud.config.uri`
    - 默认:`http://localhost:8888`
  - `pring.cloud.config.discovery.enabled`
    - 配置有导致无法注册成功
    - 默认:`false`
    - 针对`DiscoveryClient`
  - `spring.cloud.config.failFast`
    - 表示无法连接配置服务器时抛出异常
    - 默认:`false`
  - `spring.cloud.config.retry.*`
    - 配置重试的相关属性
    - 前提`spring.cloud.config.failFast=true`
  - 自定义restTemplate
    1. `spring.cloud.config.enabled=false`
      - 禁用configServer的属性源
    2. `ConfigServicePropertySourceLocator`
      - 使用此类生成一个新的Bean
    3. `resources/META-INF/stringfactories`
      - 配置如下
        -  `org.springframework.cloud.bootstrap.BootstrapConfiguration = 全限定名`
