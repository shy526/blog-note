# spring clound netflix
- 是对netflix做了一系列集成
  - 服务发现Eureka
  - 断路器Hystrix
  - 智能路由Zuul
  - 客户端负载平衡Ribbon

## Eureka 服务器
- 服务拥有一个UI界面
  - `http://{ip}`
- `@EnableEurekaServer`
  - 标识一个Eureka服务器
- `eureka.instance.preferIpAddress`
  - 使用ip注册,不使用

## 独立模式
```yml
server:
  port: 8761
eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```
## 多个Eureka
- 提高可用性
- 多个Eureka环形连接即可
```
spring:
  profiles: peer1
eureka:
  instance:
    hostname: peer1
  client:
    serviceUrl:
      defaultZone: http://peer2/eureka/
---
spring:
  profiles: peer2
eureka:
  instance:
    hostname: peer2
  client:
    serviceUrl:
      defaultZone: http://peer1/eureka/
```
