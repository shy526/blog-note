# RabbitMQ

- 开源消息队列
- erlang语言开发

## 系统架构

![img](https://i.imgur.com/0QJxywt.png)

## 特点

- 异步处理
  - 减少请求响应时间和解耦
- 使用场景
  - 较耗时而且不需要即时（同步）返回结果的操作作为消息放入消息队列
- 同时由于使用了消息队列，只要保证消息格式不变，消息的发送方和接收方并不需要彼此联系，也不需要受对方的影响，即解耦合

## 安装

- erlang
- 安装RabbitMQ
  - 安装目录`/usr/share/doc/rabbitmq-server-3.6.1`
  - rabbitmq.config.example文件复制并重命名为 rabbitmq.config
    - `cp /usr/share/doc/rabbitmq-server-3.6.1/rabbitmq.config.example /etc/rabbitmq/rabbitmq.config`
- 编辑`/etc/rabbitmq/rabbitmq.config`
  - 打开远程访问权限
    - 64行
    - `{loopback_users,[]}`
      - 去掉注释符号%%即可

- 开放端口/关闭防火墙
  - 15672
    - 访问端口
  - 5672
    - 连接端口

- `rabbitmq-plugins enable rabbitmq_management`
  - 打开RabbitMQ管理平台

- `service rabbitmq-server start（stop,restart)`
  - 启动服务

## 添加用户

- 访问浏览器 Ip:15672
  - 登录名/密码
    - guest/guest

- 点击Admin选项卡-->点击右侧的Virtual Hosts

![img](https://i.imgur.com/PlWk9H3.png)

- 注意以/ 开头

![img](https://i.imgur.com/lzaIxZ7.png)

- 添加用户/分配权限

![img](https://i.imgur.com/DTvLNuB.png)

![img](https://i.imgur.com/Q43xbkh.png)

![img](https://i.imgur.com/3jAJRDH.png)

- 所需虚拟主机
- 配置权限
- 可读权限
- 可写权限

![img](https://i.imgur.com/XGUJsDL.png)

- 配置完成以此帐号登录即可

## RabbitMQ各种模式介绍

### 简单模式（Simple）

![img](https://i.imgur.com/Q39CjGh.png)

- P： producer 消息生产者
- 红色：队列
- C：consumer 消息消费者
- 产生者产生消息，将消息写入队列
- 消费者监听队列
  - 队列有消息就消费
  - 获取消息后消息从队列中删除
  - 如果没有消息继续监听
- 产生者和消费者都只依赖消息中间件
  - 消息的发送方和接收方并不需要彼此联系
  - 也不需要受对方的影响，达到松耦合目的

- Demo

```java
    public class SimpleDemo {
        private Connection connection;

        @Before
        public void initConnection() throws IOException {
            ConnectionFactory factory = new ConnectionFactory();
            factory.setHost("192.168.119.137");
            factory.setPort(5672);
            factory.setUsername("admin");
            factory.setPassword("123456");
            factory.setVirtualHost("/test");
            connection = factory.newConnection();

        }

        @Test
        public void simple_producer() throws IOException {

            //获取Channel通道，作用是通过Channel声明队列，交换机，发布信息
            Channel channel = connection.createChannel();

            String QUEUE_NAME = "Q1";
            //①参：队列名
            //②参：durable 消息是否需要持久化，false表示不需要。
            //③参：如果此参数是true,表示创建此队列的客户端断开连接后，对应的队列被移除，并且此队列不能被共用
            //④参：true表示：如果此队列没有消费者，队列被自动移除
            //⑤参：额外的扩展参数，一般为null即可
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);

            String message = "hello";
            //basicPublish()是用于向队列里生成消息的
            //①参：交换机的名字
            //②参：队列名，补充：如果用到交换机，此参数要写路由key
            //③参：额外的扩展参数，一般为null即可
            //④参：消息体的字节数组
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
/*        channel.close();
        connection.close();*/
            while (true) ;
        }

        @Test
        public void simple_consumer() throws IOException {
            Channel channel = connection.createChannel();

            String QUEUE_NAME = "Q1";

            channel.queueDeclare(QUEUE_NAME, false, false, false, null);

            Consumer consumer = new DefaultConsumer(channel) {
                //消费者消费消息时，会进入到此方法
                //数据会传到 byte[] body 这个对象里
                @Override
                public void handleDelivery(String consumerTag, Envelope envelope, BasicProperties properties, byte[] body)
                        throws IOException {
                    String message = new String(body);
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("获取消息:" + message);
                }
            };
            //①参：队列名
            //②参：true表示 当消费者收到消费后，自动发送ack确认信息。false表示手动发送ack
            //③参：consumer对象
            channel.basicConsume(QUEUE_NAME, true, consumer);
/*        channel.close();
        connection.close();*/
            while (true) ;
        }
    }
```

## 工作队列模式（Work Queue）

![img](https://i.imgur.com/SoWNqwg.png)

- 避免立即执行资源密集型任务并等待它完成
- 行许多消费者时，任务将在他们之间共享
  - 一个消息不会被多个消费者重复消费

- Demo

```java

    public class WorkDemo {
        private Connection connection;

        @Before
        public void initConnection() throws IOException {
            //与简单模式相同
        }

        @Test
        public void work_producer() throws IOException {
            Channel channel = connection.createChannel();
            String QUEUE_NAME = "Q2";
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            for (int i = 0; i < 100; i++) {
                String message = i + "";
                channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            }
            while (true) ;
        }

        @Test
        public void work_consumer() throws IOException, InterruptedException {
            Channel channel = connection.createChannel();
            String QUEUE_NAME = "Q2";
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            channel.basicQos(1);
            QueueingConsumer consumer = new QueueingConsumer(channel);
            channel.basicConsume(QUEUE_NAME, false, consumer);
            while (true) {
                QueueingConsumer.Delivery delivery = consumer.nextDelivery();
                String message = new String(delivery.getBody());
                Thread.sleep(1000);
                System.out.println("消费者1:" + message);
                channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
            }
        }

        @Test
        public void work_consumer2() throws IOException, InterruptedException {
            //与work_consumer2()1相同
        }
    }
```

## 发布/订阅模式 (Publish/Subscribe)

![img](https://i.imgur.com/Gf1vCvU.png)

![img](https://i.imgur.com/xaLNMLd.png)

![img](https://i.imgur.com/6Gm3Kop.png)

- 向多个队列传递消息
- X表示Exchanger 交换机，此处交换机的type类型为fanout
  - 还需要将交换机和两个队列进行 binding（绑定）
> 没有队列绑定到交换中，消息将丢失，但这对我们来说是可以的;如果没有消费者在听，我们可以安全地丢弃这
个消息。

- Demo

```java

    public class PublishSubscribeDemo {
        private Connection connection;

        @Before
        public void initConnection() throws IOException {
            //与简单模式相同
        }

        @Test
        public void ps_producer() throws IOException {
            Channel channel = connection.createChannel();
            String EXCHANGER_NAME = "E1";

            // 声明交换机
            // ①参：交换机的名字
            // ②参：定义类型，fanout-发布订阅模式 direct-路由模式 topic-主题模式
            channel.exchangeDeclare(EXCHANGER_NAME, "fanout");

            for (int i = 0; i < 100; i++) {
                String message = "hh" + i + "";
                channel.basicPublish(EXCHANGER_NAME, "", null, message.getBytes());
                System.out.println(message + "<==message");
            }
            while (true)
                ;
        }

        @Test
        public void ps_consumer_1() throws Exception {
            Channel channel = connection.createChannel();
            // 要找对需要的交换机名字
            String EXCHANGER_NAME = "E1";
            // 为消费者1分配的队列名
            String QUEUE_NAME = "QC1";
            // 声明消费者1的队列
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            // 生命交换机
            channel.exchangeDeclare(EXCHANGER_NAME, "fanout");

            // 注意：别忘把交换机和队列绑定
            // ①参：队列名
            // ②参：交换机名 注意顺序
            channel.queueBind(QUEUE_NAME, EXCHANGER_NAME, "");

            channel.basicQos(1);

            QueueingConsumer consumer = new QueueingConsumer(channel);
            // 监听
            channel.basicConsume(QUEUE_NAME, false, consumer);

            while (true) {
                QueueingConsumer.Delivery delivery = consumer.nextDelivery();
                String message = new String(delivery.getBody());
                System.out.println("消费者1:" + message);
                Thread.sleep(1000);
                channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
            }
        }

        @Test
        public void ps_consumer_2() throws Exception {
            //与 ps_consumer_1() 相同
            //修改队列名
        }
    }
```
> 先启动消费者不让消息丢失

## 路由模式(Routing)
![img](https://i.imgur.com/EJ4VDAE.png)

![img](https://i.imgur.com/CNdyOAF.png)

![img](https://i.imgur.com/jQbPGgc.png)

- 的交换机的type类型=direct
- 根据路由键（routing key)和队列进行绑定

- Demo

```java
    public class RoutingDemo {
        private Connection connection;

        @Before
        public void initConnection() throws IOException {
            //与简单模式相同
        }

        @Test
        public void routing_producer() throws IOException {
            Channel channel = connection.createChannel();
            String EXCHANGER_NAME = "E2";

            // 声明交换机
            // ①参：交换机的名字
            // ②参：定义类型，fanout-发布订阅模式 direct-路由模式 topic-主题模式
            channel.exchangeDeclare(EXCHANGER_NAME, "direct");

            for (int i = 0; i < 100; i++) {
                String message = i + "";
                channel.basicPublish(EXCHANGER_NAME, "xx", null, message.getBytes());
            }
            while (true) ;
        }

        @Test
        public void routing_consumer_1() throws Exception {
            Channel channel = connection.createChannel();
            // 要找对需要的交换机名字
            String EXCHANGER_NAME = "E2";
            // 为消费者1分配的队列名
            String QUEUE_NAME = "QC3";
            // 声明消费者1的队列
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            // 生命交换机
            channel.exchangeDeclare(EXCHANGER_NAME, "direct");

            //注意：别忘把交换机和队列绑定
            //①参：队列名
            //②参：交换机名 注意顺序
            //③参：路由key
            channel.queueBind(QUEUE_NAME, EXCHANGER_NAME, "xx");

            channel.basicQos(1);

            QueueingConsumer consumer = new QueueingConsumer(channel);
            // 监听
            channel.basicConsume(QUEUE_NAME, false, consumer);

            while (true) {
                QueueingConsumer.Delivery delivery = consumer.nextDelivery();
                String message = new String(delivery.getBody());
                System.out.println("消费者1:" + message);
                Thread.sleep(1000);
                channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
            }
        }

        @Test
        public void routing_consumer_2() throws Exception {
            // 与1相同.修改队列名
        }
    }
```

## 主题模型（topics)
![img](https://i.imgur.com/lPjOh2O.png)

- topic类型的交换机支持更灵活的路由键（支持通配符）
- `*`
  - 一个任意标识符
- `#`
  - 零个或多个标识符

```java
    public class TopicDemo {
        private Connection connection;

        @Before
        public void initConnection() throws IOException {
            ConnectionFactory factory = new ConnectionFactory();
            factory.setHost("192.168.119.137");
            factory.setPort(5672);
            factory.setUsername("jtadmin");
            factory.setPassword("123456");
            factory.setVirtualHost("/jt");
            connection = factory.newConnection();
        }

        @Test
        public void topic_producer() throws IOException {
            Channel channel = connection.createChannel();
            String EXCHANGER_NAME = "EE1";

            // 声明交换机
            // ①参：交换机的名字
            // ②参：定义类型，fanout-发布订阅模式 direct-路由模式 topic-主题模式
            channel.exchangeDeclare(EXCHANGER_NAME, "topic");

            for (int i = 0; i < 100; i++) {
                String message = i + "";
                channel.basicPublish(EXCHANGER_NAME, "xx.x.xa", null, message.getBytes());
            }
            while (true) ;
        }

        @Test
        public void topic_consumer_1() throws Exception {
            Channel channel = connection.createChannel();
            // 要找对需要的交换机名字
            String EXCHANGER_NAME = "EE1";
            // 为消费者1分配的队列名
            String QUEUE_NAME = "QCC1";
            // 声明消费者1的队列
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            // 生命交换机
            channel.exchangeDeclare(EXCHANGER_NAME, "topic");

            //注意：别忘把交换机和队列绑定
            //①参：队列名
            //②参：交换机名 注意顺序
            //③参：路由key
            channel.queueBind(QUEUE_NAME, EXCHANGER_NAME, "xx.#");

            channel.basicQos(1);

            QueueingConsumer consumer = new QueueingConsumer(channel);
            // 监听
            channel.basicConsume(QUEUE_NAME, false, consumer);

            while (true) {
                QueueingConsumer.Delivery delivery = consumer.nextDelivery();
                String message = new String(delivery.getBody());
                System.out.println("消费者1:" + message);
                Thread.sleep(1000);
                channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
            }
        }

        @Test
        public void topic_consumer_2() throws Exception {
            //同1,修改队列 修改匹配符号为xx.*
        }

        @Test
        public void topic_consumer_3() throws Exception {
            //同1,修改队列 修改匹配符号为xx.x.xa
        }

    }
```

> 只有1和3能监听到数据

## 远程调用RPC

![img](https://i.imgur.com/ymWPqif.png)

## 相关链接

- [官网](http://www.rabbitmq.com/)
- [demo](http://www.rabbitmq.com/getstarted.html)

## 同类产品

- kafka

## RabbitMQ优势

1. 解耦
    - 消息队列在处理过程中间插入了一个隐含的、基于数据的接口层，两边的处理过程都要实现这一接口。
2. 异步通信
    - 消息队列提供了异步处理机制，允许你把一个消息放入队列，但并不立即处理它

3. 冗余
    - 持数据的持久化

4. 峰值处理能力
     - 关键组件顶住增长的访问压力，而不是因为超出负荷的请求而完全崩溃

5. 送达保证
    - ack

6. 排序保证
    - 消息队列本来就是排序的，FIFO（先进先出）

## docket 下安装

- `docker run -d --hostname my-rabbit -p 5672:5672 -p 15672:15672 rabbitmq:3.7.11-rc.2-management`