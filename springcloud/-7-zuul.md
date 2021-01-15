# zuul

- Netflix开源的服务网关/API网关,提供动态路由,监控,弹性,安全性,内外网隔离等功能
- 路由+过滤器
- [zuulWiki](https://github.com/Netflix/zuul/wiki)

## 快速开始

- mave你配置

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
|      zuul.routes.{routename}      |         路由名称          |
|   zuul.routes.{routename}.path    |  要路由的路径,支持通配符  |
| zuul.routes.{routename}.serviceId | 注册在Eureka的ServiceName |
|    zuul.routes.{routename}.url    |       指定Url来路由       |
|       zuul.ignored-services       |      忽略指定的服务       |
|       zuul.ignored-patterns       | 忽略指定的路径,支持通配符 |

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

## 参考

[测试项目](https://github.com/sunjiaqing/spring-cloud-zuul-demo)

[zuul配置项](https://cloud.spring.io/spring-cloud-static/Finchley.SR4/single/spring-cloud.html#_router_and_filter_zuul)

[zuul](https://github.com/Netflix/zuul)

[过滤器源码分析](https://zhuanlan.zhihu.com/p/28376627)

[动态路由源码分析](https://zhuanlan.zhihu.com/p/58207504)

[zuul生命周期](http://reader.epubee.com/books/mobile/9a/9adaecfdfecd6b8da7997c772fa46198/text00146.html)
