# stream

- 帮助构建消息
- 进一步封装MQ,在代码层面无感知
- stream -操作->binder-操作中间件
- 现在只支持`rabbitMq`和`kafka`

## 快速开始

- 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
```

- 配置rabbitMq

```yml
spring:
  rabbitmq:
    addresses: 192.168.239.129
    port: 5672
```

- 定制接口

```java
import org.springframework.cloud.stream.annotation.Input;
import org.springframework.cloud.stream.annotation.Output;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.SubscribableChannel;

public interface StreamClient {
    String key="test_stream";
    @Output(StreamClient.key)
    MessageChannel output();
}

```

- 接收端

```java
@Component
@Slf4j
@EnableBinding({StreamClient.class})
public class StreamReceiver {

    @StreamListener(StreamClient.key)
    public void receiver(Object message){
        log.error("test_stream:"+message);
    }
}


```

```java 
@Controller
public class TestController {
    @Autowired
    @Qualifier(StreamClient.KEY)
    //找到对应的发送者
    private MessageChannel messageChannel;
     return stringObjectHashMap;
    }

    @GetMapping("send/message")
    public void  sendMessage(){
        MessageBuilder<String> stringMessageBuilder = MessageBuilder.withPayload("now:" + new Date());
        messageChannel.send(stringMessageBuilder.build());
    }
}

```
