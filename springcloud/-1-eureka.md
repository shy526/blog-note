# eureka

- 基于`NnetFlix Eureka`开发

## eureka server

- 服务注册中心
- 基于http
- 方便各个语言实现
- 开发成本低
- 心跳检测
- 健康检查

### 快速开始-server

- maven依赖

```xml
<dependencies>
    <!--eurekaServer-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
<dependencyManagement>
    <dependencies>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

- 添加注解
  - `@EnableEurekaServer`

- application.yml

```yml
eureka:
  client:
    server-url:
       # 注册地址
      defaultZone: http://localhost:8761/eureka/
    #是否显示注册信息
    register-with-eureka: true
  server:
    #自我保护
    enable-self-preservation: true
spring:
  application:
    name: eureka-01
server:
  port: 8761
```

> 注意springboot 版本和springclound版本对应
> EurekaServer本身就是一个`eureka client`

## 高可用

- 两两注册

- EurekaServer-01

```yml
eureka:
  client:
    server-url:
      defaultZone: http://localhost:8763/eureka/,http://localhost:8762/eureka/
    register-with-eureka: true
  server:
    enable-self-preservation: true
spring:
  application:
    name: eureka-01
server:
  port: 8762
```

- EurekaServer-02

```yml
eureka:
  client:
    server-url:
      defaultZone: http://localhost:8761/eureka/,http://localhost:8763/eureka/
    register-with-eureka: true
  server:
    enable-self-preservation: true
spring:
  application:
    name: eureka-02
server:
  port: 8762
```

- EurekaServer-03

```yml
eureka:
  client:
    server-url:
      defaultZone: http://localhost:8762/eureka/,http://localhost:8761/eureka/
    register-with-eureka: true
  server:
    enable-self-preservation: true
spring:
  application:
    name: eureka-03
server:
  port: 8763
```

> 互相注册的`eurekaServer` 注册信息会互相交换, `eurekaClient` 注册多个`eurekaServer`即可

----

## eureka client

### 快速开始-client

- 添加依赖

```xml
<dependencies>
    <!--eurekaclient-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
        <!--web,个别版本不需要加,如果启动后闪退 就加-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

    <dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

- 添加注解
  - `@EnableDiscoveryClient`
    - 通用的`Client`注解,自动适配
    - 支持其他注册中心
  - `@EnableEurekaClient`
    - 只支持`eurekaServer`

- application.yml

```yml
eureka:
  client:
    service-url:
      #注册地址
      defaultZone: http://127.0.0.1:8761/eureka/
  instance:
    #自定义host
    hostname: localhost
server:
  port: 9091
spring:
  application:
    name: clientName
```
