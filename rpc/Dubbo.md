# Dubbo

- 阿里巴巴于2011年10月正式开源的一个由Java语言编写的分布式服务框架
- 致力于提供高性能和透明化的远程服务调用方案和基于服务框架展开的完整SOA服务治理方案
- 试用场景
  - 高并发、小数据
  - 序列化对于远程调用的响应速度、吞吐量、网络带宽消耗等
    - 采用hessian2序列化
    - 比较老的序列化实现了
    - 跨语言的
    - 单独针对java进行优化的

## 应用架构发展

![ ](https://i.imgur.com/606Oj8M.png)

1. 单机应用架构
    - 当网站流量比较的小的时候，可以采用单机部署架构，可以节省部署节点和硬件成本。
    - cpu资源、内存资源、硬盘资源、网络资源是有限的，此外如果服务器宕机，整个网站就瘫痪，甚至是数据的丢失
    - 抗不住高并发。
2. 垂直应用架构
    - 这个架构就是将各个应用拆分出来，单独部署
    - 提升了整体的响应能力和处理能力
3. 分布式应用架构
    - 网站的访问量增大，单台机器的负载能力有限，解决方法就是形成分布式的架构
    - 提高负载能力
4. 弹性应用架构
    - 可以很方便的动态扩容和减容，很容易实现服务的负载均衡和高可用。
    - 服务是可插拔和透明的
    - 基于SOA的思想（面向服务的思想），达到松耦合的目的

## SOA

- Service-Oriented Architecture
- 面向服务的体系结构
- 根据需求通过网络对应用组件进行分布式部署、组合和使用
- 松耦合

## 特点

1. 面向服务，以服务为中心
2. 服务与服务之间是松耦合的
3. 服务是可复用的
4. 服务可以灵活的组装和编排，满足流程整合和业务变化的需要

> 而Dubbo/Dubbox就是基于这种理念实现的技术框架（服务中间件）

## 相关连接

- [官网](http://dubbo.io/)
  - [dubbo源码](https://github.com/alibaba/dubbo)
- [当当dubbox](https://github.com/dangdangdotcom/dubbox)
  - 开源
- [京东](http://www.oschina.net/p/jd-hydra?fromerr=k30zqfPq)
  - 闭源

## Dubbox

- 基于Dubbo
- REST风格远程调用
- Kryo/FST序列化等
- dubbox为dubbo引入Kryo和FST这两种高效Java序列化实现
  - 逐步取代hessian2
- 升级Spring
  - 将dubbo中Spring由x升级到目前最常用的3.x版本
  - 减少项目中版本冲突带来的麻烦
- 升级ZooKeeper客户端
  - 将dubbo中的zookeeper客户端升级到最新的版本
  - 以修正老版本中包含的bug

## 支持REST风格远程调用（HTTP + JSON/XML)

- dubbo支持多种远程调用方式，但缺乏对当今特别流行的REST风格远程调用的支持。

## 支持基于Kryo和FST的Java高效序列化实现

- dubbo RPC是dubbo体系中最核心的一种高性能、高吞吐量的远程调用方式
  - 长连接
    - 避免了每次调用新建TCP连接，提高了调用的响应速度
  - 多路复用
    - 单个TCP连接可交替传输多个请求和响应的消息
    - 降低了连接的等待闲置时间
    - 减少了同样并发数下的网络连接数，提高了系统吞吐量

> 目前dubbo以整合dubbox

## 控制页面配置

1. 官网下载 Dubbo
   - 解压,在Dubbo-admin目录下打开cmd
     - mvn package -Dmaven.skip.test=true

2. 根据实际实际情况修改tomcat的服务端口

3. 修改doubbo.properties配置文件
    - 配置zk的地址(提前配置Zookeeper)
    - 如下

    ```properties
    dubbo.registry.address=zookeeper://192.168.234.21:2181?
                backup=192.168.234.22:2181,192.168.234.23:2181
    dubbo.admin.root.password=root #控制台root登录的密码
    dubbo.admin.guest.password=guest #控制台guest的登录密码
    ```

## 实例

### protocol

- 定义协议(接口)

1. 创建Maven工程(quick start)
    - 添加接口

### provider

- 定义生产者

1. 创建Maven工程(webapp)
    - 依赖protocol项目
    - 实现协议(接口)
2. 配置spring和Dubbo的相关jar包

    ```xml
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>4.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.8.4</version>
        </dependency>
        <dependency>
            <groupId>org.javassist</groupId>
            <artifactId>javassist</artifactId>
            <version>3.15.0-GA</version>
        </dependency>
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty</artifactId>
            <version>3.7.0.Final</version>
        </dependency>
        <dependency>
            <groupId>org.apache.mina</groupId>
            <artifactId>mina-core</artifactId>
            <version>1.1.7</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.grizzly</groupId>
            <artifactId>grizzly-core</artifactId>
            <version>2.1.4</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.2.1</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.1.39</version>
        </dependency>
        <dependency>
            <groupId>com.thoughtworks.xstream</groupId>
            <artifactId>xstream</artifactId>
            <version>1.4.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.bsf</groupId>
            <artifactId>bsf-api</artifactId>
            <version>3.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.6</version>
        </dependency>
        <dependency>
            <groupId>com.github.sgroschupf</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>2.5.0</version>
        </dependency>
        <dependency>
            <groupId>com.googlecode.xmemcached</groupId>
            <artifactId>xmemcached</artifactId>
            <version>1.3.6</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-frontend-simple</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-transports-http</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.thrift</groupId>
            <artifactId>libthrift</artifactId>
            <version>0.8.0</version>
        </dependency>
        <dependency>
            <groupId>com.caucho</groupId>
            <artifactId>hessian</artifactId>
            <version>4.0.7</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>org.mortbay.jetty</groupId>
            <artifactId>jetty</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.mortbay.jetty</groupId>
                    <artifactId>servlet-api</artifactId>
                </exclusion>
            </exclusions>
            <version>6.1.26</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.16</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.1.0</version>
        </dependency>
        <dependency>
            <groupId>javax.validation</groupId>
            <artifactId>validation-api</artifactId>
            <version>1.0.0.GA</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>4.2.0.Final</version>
        </dependency>
        <dependency>
            <groupId>javax.cache</groupId>
            <artifactId>cache-api</artifactId>
            <version>0.4</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxrs</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-client</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-netty</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jdk-http</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jackson-provider</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxb-provider</artifactId>
            <version>3.0.7.Final</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-core</artifactId>
            <version>8.0.11</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-logging-juli</artifactId>
            <version>8.0.11</version>
        </dependency>
        <dependency>
            <groupId>com.esotericsoftware.kryo</groupId>
            <artifactId>kryo</artifactId>
            <version>2.24.0</version>
        </dependency>
        <dependency>
            <groupId>de.javakaffee</groupId>
            <artifactId>kryo-serializers</artifactId>
            <version>0.26</version>
        </dependency>
        <dependency>
            <groupId>de.ruedigermoeller</groupId>
            <artifactId>fst</artifactId>
            <version>1.55</version>
        </dependency>
        <dependency>
            <groupId>com.demo</groupId>
            <artifactId>dubbo-protocol</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
    ```

3. Dubbo配置文件
    - `resource`下

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
              http://code.alibabatech.com/schema/dubbo
             http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
        <!-- 提供方应用信息，用于计算依赖关系 -->
            <dubbo:application name="addService" />
        <!--  使用multicast广播注册中心暴露服务地址 -->
        <!-- <dubbo:registry address="multicast://224.5.6.7:1234"/>	 -->
            <dubbo:registry address="zookeeper://192.168.119.141:2181?backup=192.168.119.139:2181,192.168.119.140:2181" />

            <!-- 用dubbo协议在20880端口暴露服务 -->
            <dubbo:protocol name="dubbo" port="20880" />

            <!-- 声明需要暴露的服务接口 -->
            <dubbo:service interface="com.demo.dubbo.protocol.AddService" ref="AddService" />

            <!-- 具体的实现bean -->
            <bean id="AddService" class="com.demo.dubbo.provider.AddServiceImpl" />
     </beans>
    ```

4. 添加服务类

    ```java
        public class Start {
            public static void main(String[] args) throws Exception {
                ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(
                    new String[] { "applicationProvider.xml" });
                    context.start();
                    System.out.println("服务启动");
                    //保持服务线程一直开启
                    while(true);
        }
     }
    ```

5. 测试

- 启动服务（执行main方法）
- 打开Dubbo监控页面
  - ->服务治理->服务
  - 查看是否成功启动

### ConSumer

- 定义消费者

1. 创建Maven工程(webabb)
    - 引入pom文件
        - 与生产者相同

2. 引入dubbo配置文件
    - `resource`下

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
               xsi:schemaLocation="http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://code.alibabatech.com/schema/dubbo
                http://code.alibabatech.com/schema/dubbo/dubbo.xsd ">
    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-of-addService" />
    <!-- 注册中心暴露服务地址 -->
    <!--<dubbo:registry address="multicast://224.5.6.7:1234"/> -->
    <dubbo:registry address="zookeeper://192.168.119.141:2181?backup=192.168.119.139:2181,192.168.119.140:2181"/>
    <dubbo:consumer timeout="5000"/>
    <!-- 生成远程服务代理，可以像使用本地bean一样使用demoService -->
    <dubbo:reference id="AddService" interface="com.demo.dubbo.protocol.AddService" />
        </beans>
    ```

3. 定义类

    ```java
    public class ConSumerThr {
        public static void main(String[] args) throws InterruptedException {
          ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] { "applicationConsumer.xml" });
          context.start();
          //获取服务的代理对象
          AddService proxy=(AddService) context.getBean("AddService");
          //通过代理对象调用服务方法
          int result=proxy.add(2, 3);
          for (;;) {
            System.out.println("客户端收到结果:"+proxy.add(1, 3));
            Thread.sleep(500);
          }
       }
    }
    ```

4. 启动provider,consumer

## dubbo 调用过程

![ ](https://i.imgur.com/CDkGQGH.png)

![ ](https://i.imgur.com/5bnTjG7.png)

## Zookeeper 作为Dubbo的服务的注册的中心

- Dubbo注册中心最早基于mysql数据库
- 目前支持
  - ZooKeeper（生产环境）
  - Redis（生产环境）
  - Multicast（缺省配置，只适合测试环境，不能跨网段）、Simple（测试）

- ZooKeeper
  - 一个分布式的，开放源码的分布式应用程序协调服务
  - Google的Chubby一个开源的实现
  - 是Hadoop和Hbase的重要组件

![ ](https://i.imgur.com/US6Gaig.png)
