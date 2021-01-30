# zuul

- Netflix开源的服务网关/API网关,提供动态路由,监控,弹性,安全性,内外网隔离等功能
- 路由+过滤器
- [zuulWiki](https://github.com/Netflix/zuul/wiki)

## 快速开始

- mave配置

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <!--不是eureka的项目可以不添加-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    ```

- application.yml 配置

    ```yml
    spring:
      application:
        name: zuul-01
    server:
      port: 8762
    # 不是eureka的项目可以不添加
    eureka:
    client:
        server-url:
        # 注册地址
        defaultZone: http://localhost:8761/eureka/
        #是否显示注册信息
        register-with-eureka: true
    logging:
      level:
        root: error
    zuul:
      # 路由规则配置
    routes:
        TestService:
          path: /api/**
          # 也可以配置路由转发 path--->url: http://localhost:8080/
          serviceId: eureka-client-01 
    ```

- 启动类添加相关注解

```java
@SpringBootApplication
@EnableEurekaClient
@EnableZuulProxy
public class ZuulServiceApplication {
    public static void main(String[] args) {
       SpringApplication.run(ZuulServiceApplication.class, args);
    }
}

```

## 过滤器

### 过滤器类型

- pre
  - 在请求被路由之前调用
  - 应用场景
    - 限流
    - 鉴权
- routing
  - 请求路由到微服务时调用
- post
  - 路由到微服务时调用
  - 应用场景
    - 统计
    - 日志
- error
  - 在其他阶段发送错误时执行

### zuulFilter 重写方法

|    方法名    |               说明                |
|:------------:|:---------------------------------:|
| filterOrder  |     过滤器执行顺序,越小越优先     |
|  filterType  | 过滤器类型:pre,routing,post,error |
|     run      |             执行逻辑              |
| shouldFilter |            是否要过滤             |

### 简单应用

```java
public abstract class MyAbstractFilter extends ZuulFilter {
    private final RouteLocator routeLocator;
    private final UrlPathHelper urlPathHelper;
    MyAbstractFilter(RouteLocator routeLocator, UrlPathHelper urlPathHelper) {
        this.routeLocator = routeLocator;
        this.urlPathHelper = urlPathHelper;
    }
    /**
     * 获取路由信息
     * @return Route
     */
    protected Route getRoute() {
        RequestContext requestContext = RequestContext.getCurrentContext();
        return routeLocator.getMatchingRoute(urlPathHelper.getPathWithinApplication(requestContext.getRequest()));
    }
    /**
     * 获取ResponseBody 并重新设置
     * @return String
     */
    protected String getResult() {
        RequestContext requestContext = RequestContext.getCurrentContext();
        InputStream stream = requestContext.getResponseDataStream();
        String result = "";
        if (stream != null) {
            result = IOUtils.toString(stream);
            RequestContext.getCurrentContext().setResponseBody(result);
        }
        return result;
    }
}
```

## 路由配置参数

|              配置项               |           说明            |
|:---------------------------------:|:-------------------------:|
|       zuul.ignored-patterns       | 忽略指定的路径,支持通配符 |
|       zuul.ignored-services       |      忽略指定的服务       |
|      zuul.routes.{routename}      |         路由名称          |
|   zuul.routes.{routename}.path    |  要路由的路径,支持通配符  |
| zuul.routes.{routename}.serviceId | 注册在Eureka的ServiceName |
|    zuul.routes.{routename}.url    |       指定Url来路由       |

## 熔断

容错管理工具,通过熔断机制控制服务和第三方库的节点 从而对延迟和故障提供更强大的容错能力

>>路由转发的请求不会采用HystrixCommand来包装 所以这类路由请求没有线程隔离和断路器保护也不会有负载均衡的能力

```java
/**
*熔断降级
*/
@Component
public class TestFallback implements FallbackProvider {
    private final static Logger LOGGER = LoggerFactory.getLogger(TestFallback.class);
    private final String   ERROR_FORMAT= "{'error':%s}";

    /**
     * 返回熔断的路由
     * @return String
     */
    @Override
    public String getRoute() {
        return "eureka-client-01";
    }

    /**
     * 熔断的处理
     * @return ClientHttpResponse
     */
    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        LOGGER.error("fallback----------------------------");
        String errMsg="";
        if (cause != null ) {
            errMsg=cause.getMessage();
        }

        return MyClientHttpResponse.getJsonResponse(String.format(ERROR_FORMAT,errMsg));
    }
}
```

## ZUUL端点

- 添加maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 添加配置

```yml
management:
  endpoints:
    web:
      exposure:
        include: routes,filters
```

`{zuulIp}/actuator/filters`

可以获取过滤器列表

`{zuulIp}/actuator/routes`

获取路由信息

## nacos

实现动态服务发现 服务配置 服务元数据及流量管理

以下若有错误望指正

### 安装

[nacos官方文档](https://nacos.io/zh-cn/docs/quick-start.html)

[nacos下载](https://github.com/alibaba/nacos/releases)

直接解压启动 startup.cmd -m standalone 单例启动即可

访问 `http://127.0.0.1:8848/nacos/index.html`

> 默认账户密码nacos nacos

### nacos动态配置的使用

- maven

```xml
<properties>
    <!--2.2.4.RELEASE有个线程池的bug,第一次更新后无法在更新配置-->
    <nacos.version>2.2.3.RELEASE</nacos.version>
</properties>
<dependencies>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        <version>${nacos.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

> 不要用2.2.4 不要用2.2.4

- bootstrap.yml

```yml
spring:
  cloud:
    nacos:
      config:
        server-addr: "127.0.0.1:8848"
        file-extension: yaml
        timeout: 5000
        #config-long-poll-timeout:
        #group: 
  application:
    name: nacos-config
server:
  port: 8766
```

> bootstrap.yml 用来在程序引导时执行 优先于application.yml

- 测试代码

```java
@RestController
@RequestMapping("/config")
@RefreshScope
public class TestController {
    @Value("${useLocalCache:测试}")
    private String testStr;
    @RequestMapping("/get")
    public String get() {
        System.out.println("test = " + testStr);
        return testStr;
    }
}
```

>`@RefreshScope` 一种特殊的scope实现 用来实现配置 实例热加载
>> 一般配合spring cloud bus 实现刷新

### 将nacos作为注册中心

- maven

```java
<dependencies>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.4.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

> 注意版本对应
>> [对应版本wiki](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

- application.yml

```yml
server:
  port: 8767
spring:
  application:
    name: nacos-client
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

- 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(NacosClientApplication.class, args);
    }
}

```

 zuul切换注册中心

- maven修改 application.yml 启动类 三个修改参照 上方

> application.yml中 serviceId 参数 修改为对应服务名 即可正常路由和熔断

查看nacos的管理界面服务列表就会出现该服务
>> 如果没有任何一个路径不会显示在服务列表里

## zuul使用nacos实现动态路由配置一种方式

- maven添加依赖

```xml
<dependency>
  <groupId>com.alibaba.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
  <version>2.2.3.RELEASE</version>
</dependency>
```

- 添加一个配置类

```java
@Configurable
public class ZuulConfig {
    @Bean
    @RefreshScope
    @ConfigurationProperties("zuul")
    @Primary
    public ZuulProperties zuulProperties() {
        return new ZuulProperties();
    }
}
```

> @rimary 有相同类型时优先注入 @RefreshScope 保证数据刷新

- bootstrap.yml

```yml
spring:
  application:
    name: zuul-01
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yaml
        timeout: 5000
      discovery:
        server-addr: 127.0.0.1:8848
```

- application.yml

```yml
server:
  port: 8762
logging:
  level:
    root: error
management:
  endpoints:
    web:
      exposure:
        include: routes,filters
```

- nacos配置上 zuul-01.yaml

```yml
zuul:
  routes:
    uuu:
      path: /api2/**
      serviceId: nacos-client
```

> 在nacos修改配置 在使用端点查看路由信息`{zuulIp}/actuator/routes` 验证

## zuul扩展

### 令牌桶限流

- maven

```xml
<dependency>
  <groupId>com.google.guava</groupId>
  <artifactId>guava</artifactId>
  <version>30.1-jre</version>
</dependency>
```

- 过滤器

```java
@SuppressWarnings("UnstableApiUsage")
@Component
public class RateLimitFilter extends MyAbstractFilter {
    private static final RateLimiter RATE_LIMITER = RateLimiter.create(10.0D);
    RateLimitFilter(RouteLocator routeLocator, UrlPathHelper urlPathHelper) {
        super(routeLocator, urlPathHelper);
    }
    @Override
    public String filterType() {
        return FilterConstants.PRE_TYPE;
    }
    @Override
    public int filterOrder() {
        return 0;
    }
    @Override
    public boolean shouldFilter() {
        RequestContext context = RequestContext.getCurrentContext();
         String id ="threadId="+Thread.currentThread().getId()+"-->"+System.currentTimeMillis()+"-->";
        if (!RATE_LIMITER.tryAcquire()) {
            context.setSendZuulResponse(false);
            context.setResponseStatusCode(200);
            HttpServletResponse response = context.getResponse();
            response.addHeader("Content-Type","text/plain;charset=UTF-8");
            context.setResponseBody("稍后");
            System.out.println(id+"pre限流限制");
            return false;
        }
        System.out.println(id+"pre限流通过");
        return true;
    }
    @Override
    public Object run() {
        return null;
    }
}
```

- 测试方法

```java
    @Test
    void rateLimitFilterTest() {
        int total = 200;
        RestTemplate restTemplate = restTemplateBuilder.build();
        CyclicBarrier cyclicBarrier = new CyclicBarrier(total);
        CountDownLatch countDownLatch = new CountDownLatch(total);
        AtomicInteger s = new AtomicInteger(0);
        AtomicInteger f = new AtomicInteger(0);
        //每(1/?*1000)毫秒秒方放一个令牌
        for (int i = 1; i <= total; i++) {
            new Thread(() -> {
                try {
                    cyclicBarrier.await();
                    ResponseEntity<String> forEntity = restTemplate.getForEntity("http://localhost:8762/api2/test/get?test=xxxxx", String.class);
                    String body = forEntity.getBody();
                    if ("稍后".equals(body)) {
                        f.getAndIncrement();
                    } else {
                        s.getAndIncrement();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    countDownLatch.countDown();
                }
            }).start();
        }
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("成功" + s.get());
        System.out.println("失败" + f.get());
    }
}
```

- 上述的测试程序结果

| permitsPerSecond | 线程数  | 成功 | 失败 |
|:----------------:|:------:|:----:|:----:|:----:|
|       100        |  200     | 117  |  83  |
|       100        |  200     | 124  |  76  |
|       100        |  500     | 130  |  370  |
|       100        |  500     | 134  |  366  |
|       100        |  1000     | 156  |  844  |
|       100        |  1000     | 158  |  842  |

在比较集中的并发情况下有令牌超出的情况出现(可能出现跨秒的情况)

## 参考

[测试demo](https://github.com/sunjiaqing/spring-cloud-zuul-demo)

[nasco官网](https://nacos.io/zh-cn/index.html)

[zuul配置项](https://cloud.spring.io/spring-cloud-static/Finchley.SR4/single/spring-cloud.html#_router_and_filter_zuul)

[zuul](https://github.com/Netflix/zuul)

[过滤器源码分析](https://zhuanlan.zhihu.com/p/28376627)

[动态路由源码分析](https://zhuanlan.zhihu.com/p/58207504)

[zuul生命周期](http://reader.epubee.com/books/mobile/9a/9adaecfdfecd6b8da7997c772fa46198/text00146.html)

[令牌桶原理](https://www.cnblogs.com/jianhaoscnu/p/13385180.html)

[RateLimiter 相关文档](http://ifeve.com/guava-ratelimiter/)
