# 应用间通讯

## restTempale

### 快速开始-restTemplae

- restTemplate 配置

```java
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;
import org.springframework.web.client.RestTemplate;

@Component
@Configuration
public class RestTemplateConfig {
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
       return new RestTemplate();
    }
}
```

- 服务a 如下所示

```java
@RequestMapping("/product")
public interface ProductController {

    @GetMapping("list")
    Result<List<ProductVo>> list();

    @PostMapping("fork/order")
    Result<List<OutPutProductVo>> forkOrder(@RequestBody List<String> productIds);
}
```

- 调用方带俩

```java
@Service
@Slf4j
public class OrderServiceImpl implements OrderService {
    @Autowired
    private RestTemplate restTemplate;
    /**
     * 格式为http://服务名/+路径
     */
    private String forkOrder = "http://product/product/fork/order";

    @Override
    public Result<List<OutPutProductVo>>  test() {
        HttpEntity<List<String>> httpEntity = new HttpEntity<List<String>>(Arrays.asList("1","2"), null);
        ResponseEntity<Result<List<OutPutProductVo>>> response = restTemplate.exchange(forkOrder, HttpMethod.POST, httpEntity,
                new ParameterizedTypeReference<Result<List<OutPutProductVo>>>() {
                });
     (HttpStatus.OK), "请求失败");
        Result<List<OutPutProductVo>> body = response.getBody();
    }
}

```

> map参数时 需要使用 `LinkedMultiValueMap` 否则接收方无法接收

### 负载均衡器 Ribbon

- Ribbon核心
  - 服务发现 serverList
  - 服务监听过滤 serverListFiler
  - 选择规则 IRule
    - 默认轮询

- 修改选择规则

```yml
服务名:
  ribbon:
   # 随机
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```

## feign

- 伪rpc客户端
- 简化调用流程

### 快速开始 feign

- 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

```

- 添加注解
  - `@EnableFeignClients(basePackages = {"top.ccxh.product.client.controller"})`
    - 默认扫描启动类所在以及下的包
    - 一般feign 由服务提供方指定

- FeignClient 客户端写法
  - 与`controller` 相同 换注解为 `@FeignClient(name = "服务名")`
  - 一般作为自己的`controller`实现这套接口即可,减少代码量
    - 作为`controller`作为路由时会报错

```java
@FeignClient(name = "product")
@RequestMapping("/product")
public interface ProductController {

    @GetMapping("list")
    Result<List<ProductVo>> list();

    @PostMapping("fork/order")
    Result<List<OutPutProductVo>> forkOrder(@RequestBody List<String> productIds);

    @PostMapping("stock")
    Result<List<OutPutProductVo>> decreaseStockProcess(@RequestBody List<InOrderStockeVo> inOrderStockeVos);
}

```

### 负载均衡器

- Ribbon
- 与restTemplae 相同