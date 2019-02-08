# 统一配置中心

主要解决:

1. 不方便维护
2. 内容安全与权限
3. 更新配置需要重启项目

## 快速开始 configServer

- 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
<!--是configServer也是eurekaClient端-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

```

- 添加注解
  - `@EnableConfigServer`
  - `@EnableDiscoveryClient`

- application.yml

```yml
spring:
  application:
    name: config-01
  cloud:
    config:
      server:
        git:
          #仓库地址
          uri: https://github.com/sunjiaqing/spring-cloud-demo-config.git
          #用户名
          username: sundada214@outlook.com
          #密码
          password:
          # 本地git的存储目录
          basedir: D:\project\spring-cloud-demo\gitbase
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
server:
  port: 9999

```

## 基本

### 主要访问路径

- `http://ip:port{/label}/name-profiles.[properties|yml|json]`
  - `label` 分支
    - 默认master
  - `name` 文件名
  - `profiles` 环境
    - 不存在时访问的是name
> 所有`name-profiles.[properties|yml|json]`的都会与`name.[properties|yml|json]`合并,在返回出来

## 快速开始 configClient

- 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>

```

- 添加注解
  - `@EnableDiscoveryClient`

- bootstrap.yml
  - springboot的引导文件优先于`application.yml`加载
  - 使用`application.yml` 会无法启动,配置还没有加载容器就开始初始化了

```yml
spring:
  application:
    name: product
  cloud:
    config:
      discovery:
        enabled: true
        service-id: config-01
      profile: dev
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

> 注意 `eureka.client.service.defaultZone`配置在配置中心时,如果`eurekaServer`没有使用默认地址`localhost:8761`,就会去加载 `configServer`的默认地址`localhost:8888`
>> 第一种方式是直接将`eureka.client.service.defaultZone`配置到本地,第二种直连configServer,配置`spring.cloud.config.uri`支持多个

### 常用

- `spring.cloud.config.failFast`
  - 无法连接配置服务器时抛出异常,默认flase

- 多文件加载

示例:

```java
spring:
  application:
    name: product
  cloud:
    config:
      name: product,dataSource
      discovery:
        enabled: true
        service-id: config-01
      profile: dev
```

运行结果:  
同时加载:dataSource-dev.yml,product-dev.yml

```log
2019-02-06 16:11:23.479  INFO 14828 --- [           main] b.c.PropertySourceBootstrapConfiguration : Located property source: CompositePropertySource {name='configService', propertySources=[MapPropertySource {name='configClient'}, MapPropertySource {name='https://github.com/sunjiaqing/spring-cloud-demo-config.git/dataSource-dev.yml'}, MapPropertySource {name='https://github.com/sunjiaqing/spring-cloud-demo-config.git/product-dev.yml'}]}
```

> 注意相同属性覆盖,前者覆盖后者

## 内容加密

- 需要`jce`
- [jce8下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)
- 解压至该 `{JAVA_HOME}\lib\security`

### 对称加密

- bootstrap.yml

```yml
encrypt:
  key: 12345
```

- 访问`http://{ip}:{port}/encrypt/status`

返回去如下代表加密已经生效

```yml
{
status: "OK"
}
```

> `encrypt.key`一定要写到`bootstrap.yml`中否则无法生效

- 使用url加密 解密

- 加密url`http://{ip}:{port}/encrypt`

- 加密url`http://{ip}:{port}/decrypt`

- 例子
  - `curl -X POST http://{ip}:{port}/[encrypt|decrypt] -d [密文|明文]`

- 远程git配置文件写法如下

```yml
spring:
datasource:
  driver-class-name: com.mysql.cj.jdbc.Driver
  url: jdbc:mysql://192.168.239.129:3306/spring-clound-demo?characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true
  username: '{cipher}dc00cd5ac6eba39f4976baa43f4cd8dd6f4a08b8fa1b6d8a1a6cc36f61b2dd20'
  password: '{cipher}dc00cd5ac6eba39f4976baa43f4cd8dd6f4a08b8fa1b6d8a1a6cc36f61b2dd20'
jpa:
  show-sql: true
```

> `{cipher}+密文的写法`,`.properties`格式的文件不要引号
>>注意此时的加解密都configServer端完成,配置`spring.cloud.config.server.encrypt.enabled=false`来关闭服务端解密(失去解密能力)
>>> 直接不要配置`encrypt.key`
>>>> 以上方法都需要将 `encrypt.key`配置client端

### 非对称加密

- 生成密钥库
  - `keytool -genkeypair -alias {alias} -keyalg RSA -keypass {secret} -keystore server.jks -storepass {password}`

- 配置

```yml
encrypt:
  keyStore:
    location: classpath:/server.jks
    password: password
    alias: alias
    secret: secret
```

## 嵌入式configServer

- `spring.cloud.config.server.prefix`
  - 更改url的访问方式
  - `spring.cloud.config.server.prefix=/config`
    - 改为访问`localhost:8888/config`
- `spring.cloud.config.server.bootstrap`
  - 表示从git中读取
  - 默认false
  - 作为嵌入式服务时,官方建议开启

## configServer 自动刷新

- 添加bus 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

- application.yml

```yml
spring:
  application:
    name: config-01
  cloud:
    config:
      server:
        git:
          uri: https://github.com/sunjiaqing/spring-cloud-demo-config.git
          username: sundada214@outlook.com
          password: 
          basedir: D:\project\spring-cloud-demo\gitbase
  rabbitmq:
    addresses: 192.168.239.129
    port: 5672
management:
  endpoints:
    web:
      exposure:
        # 开放所所有端口
        include: '*'
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
server:
  port: 9999

```

- 查看消息队列是否已经生成即可

>`client`端也需要配置`spring.rabbitmq`(否则无法完成刷新),也需要依赖`bus`组件

- `client`添加刷新注解
  - `@RefreshScope`
  - 详细代码如下

```java
@Controller
@RefreshScope
public class TestController {
    @Value("${person.name}")
    private String name;
    @Value("${person.age}")

    private Integer age;
    @GetMapping("person")
    @ResponseBody
    public Map<String,Object> getPerson(){
        HashMap<String, Object> stringObjectHashMap = new HashMap<>();
        stringObjectHashMap.put("age",age);
        stringObjectHashMap.put("name",name);
        return stringObjectHashMap;
    }
}
```

- 手动访问`http://{ip}:{port}/actuator/bus-refresh`
  - 进行广播
  - 将url配置到`git`上的`webHook`,每一次`push`都调用次url即可

> 刷新指定`client`端,`http://{ip}:{port}/actuator/bus-refresh?destination=服务名:[port:**(通配符)]` 