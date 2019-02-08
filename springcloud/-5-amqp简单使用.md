# amqp

## 快速开始 amqp

- 添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

- application.yml rabbitmq相关配置

```yml
spring:
  rabbitmq:
    addresses: 192.168.239.129
    port: 5672
    username: guest
    password: guest
```

- 代码-接收方

```java
@Component
@Slf4j
public class ReceiverMQ  {
   /**
    *@RabbitListener(queues = {"test"})
    *需要事先建立好mq
    */

    //自动创建mq
    @RabbitListener(queuesToDeclare = {@Queue("test")})
    public void  receiver(String message){
        log.error("test队列接收消息:{}",message);
    }

     /**
     * 通道绑定
     * 都会自动创建
     */
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("test-01"),
            exchange = @Exchange("exchange-01"),
            key="01"
    ))
    public void receiver1(String message){
        log.error("test-01队列接收消息:{}",message);
    }


    /**
     * 通道绑定
     * 都会自动创建
     */
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue("test-02"),
            exchange = @Exchange("exchange-01"),
            key="02"
    ))
    public void receiver2(String message){
        log.error("test-02队列接收消息:{}",message);
    }
}
```

- 代码-发送发

```java
public class SendMQ extends OrderServiceApplicationTests {
    @Autowired
    private AmqpTemplate amqpTemplate;

    @Test
    public void sen(){
        amqpTemplate.convertAndSend("test","now"+new Date());
        amqpTemplate.convertAndSend("exchange-01","01","01接收的");
        amqpTemplate.convertAndSend("exchange-01","02","02接收的");
    }
}
```