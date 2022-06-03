# 21_消息队列-RabbitMQ安装&MQ工作流详解&Exchange类型&SpringBoot整合MQ

# P248、商城业务消息队列-MQ简介

## 1、解耦：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033980.png)

## 2、异步

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033982.png)

## 3、削峰（流量控制）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033983.png)

# P249、商城业务消息队列-RabbitMQ简介

1. 大多应用中，可通过消息服务中间件来提升系统异步通信、扩展解耦能力
2. 消息服务中两个重要概念：消息代理(message broker)和目的地(destination)
    1. **消息代理**：可以简单的理解为安装了MQ的服务器
    2. **目的地**：消息发给谁

当消息发送者发送消息以后，将由消息代理接管，消息代理保证消息传递到指定目的地。

1. 消息队列主要有两种形式的**目的地**
    1. 队列(queue) ：[点对点](https://www.notion.so/21_-RabbitMQ-Exchange-SpringBoot-MQ-2254d62897ea4c818d36d5a51406a993)消息通信(point-to- point) .
    2. 主题(topic) ：[发布(publish) /订阅(subscribe)](https://www.notion.so/21_-RabbitMQ-Exchange-SpringBoot-MQ-2254d62897ea4c818d36d5a51406a993) 消息通信
2. 点对点式：
    - 消息发送者发送消息， 消息代理将其放入一一个队列中，消息接收者从队列中获取消息内容,消息读取后被移出队列
    - 消息只有唯一的发送者和接受者，但并不是说只能有一个接收者
3. 发布订阅式：
    1. 发送者(发布者)发送消息到主题，多个接收者(订阅者)监听(订阅)这个主题，那么就会在消息到达时同时收到消息

 6. JMS (Java Message Service) JAVA消息服务：

基于JVM消息代理的规范。ActiveMQ、 HornetMQ是JMS实现

1. AMQP (Advanced Message Queuing Protocol)
   
    高级消息队列协议，也是一个消息代理的规范，兼容JMSRabbitMQ是AMQP的实现
    

> JMS和AMQP的区别：
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033984.png)

# P250、消息队列-RabbitMQ 工作流程

## RabbitMQ相关概念：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033985.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033986.png)

## RabbitMQ 工作流程：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033987.png)

# P251、RabbitMQ安装-Docker安装RabbitMQ

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033988.png)

## Docker安装RabbitMQ命令：

```bash
docker run -d --name rabbitmq -p 5671:5671 -p 5672:5672 -p 4369:4369 -p 25672:25672 -p 15671:15671 -p 15672:15672 rabbitmq:management
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033989.png)

显示以下信息表示安装成功

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033990.png)

### Docker安装RabbitMQ各端口说明

```bash
4369, 25672 (Erlang发现&集群端口)

5672, 5671 (AMQP端口)

15672 (web管理后台端口)

61613, 61614 (STOMP协议端口)

1883, 8883 (MQTT协议端口)

https://www.rabbitmq.com/networking.html
```

## 开机自启动RabbitMQ：

```bash
[root@10 ~]# docker update rabbitmq --restart=always
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033991.png)

> 登录RabbitMQ
> 

浏览器访问：192.168.56.10:15672

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033992.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033993.png)

# P252、商城业务消息队列-Exchange类型

## RabbitMQ的运行机制【PPT】

### AMQP中的消息路由

AMQP中消息的路由过程和Java开发者熟悉的JMS存在一些差别，AMQP中增加了Exchange和Binding的角色。

生产者把消息发布到Exchange上，消息最终到达队列并被消费者接收，而Binding决定交换器的消息应该发送到那个队列。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033994.png)

Producer消息的生产者生成一个消息Message，消息先发给我们的消息代理Broker，由代理先将消息发给指定的交换机Exchange，该交换机下面可能会绑定Bindings非常多的队列Queues，即Bindings：Queues = m：n，同样的一个队列Queues也可以被多个多个交换机来绑定，有着复杂的绑定绑定关系，把交换机理解成男人中的一个海王也不足为过 🙃

## Exchange类型

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033995.png)

Exchange分发消息时根据类型的不同分发策略有区别，目前共四种类型：

1. direct：• 美 /dəˈrekt; daɪˈrekt/ 直接的
2. fanout：• 美 /fænaʊt/ 扇出
3. topic：• 美 /ˈtɑːpɪk/ 主题
4. ~~headers：• 英 /ˈhedəz/ 头信息~~ （PS：几乎用不到）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033996.png)

direct、fanout, topic、 headers 。headers 匹配AMQP消息的header而不是路由键，headers 交换器和direct 交换器完全一致，这二者都是JMS中所说的点对点通讯， 但headers 性能比较底下，目前几乎用不到了，fanout, topic属于发布订阅式的，所以直接着另外三种类型：

> 类型一：direct Exchange直接交换机
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033997.png)

> 类型二：Fanout Exchange
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033998.png)

假设Fanout 类型的交换机绑定了多个Queues队列，是用来实现发布订阅式的广播类型，不关心路由键是什么，无条件将消息Message发送给所有绑定好的队列Queues。

> 类型三：Topic Exchange  主题发布订阅模式（根据主题部分广播）
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033999.png)

关心路由键是什么，根据匹配的路由键将消息Message发送给所有绑定好的队列Queues。

发消息是发给交换机Exchange，交换机通过绑定关系将消息发给了队列，监听消息是监听队列Queues

### 如何创建一个交换机Exchange

页面：Exchange→Add a new exchange

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033000.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033001.png)

创建的my.exchange.direct交换机必须和队列Queues绑定才能工作，消息要发给交换机Exchange，那么我们就需要先创建一个队列

### 如何创建一个队列Queues

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033002.png)

### 将创建好的交换机和队列进行绑定

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033003.png)

# P253、商城业务-消息队列- Direct- Exchange

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033004.png)

> 第一步：先创建四个队列Queues
> 
1. atguigu
2. atguigu.news
3. atguigu.emps
4. gulixueyuan.news

其他同理，依次创建，相应的队列。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033005.png)

创建好的队列

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033006.png)

> 第二部：创建交换机Exchange
> 
1. [exchange.direct](https://www.notion.so/21_-RabbitMQ-Exchange-SpringBoot-MQ-2254d62897ea4c818d36d5a51406a993)
2. [exchange.fanout](https://www.notion.so/21_-RabbitMQ-Exchange-SpringBoot-MQ-2254d62897ea4c818d36d5a51406a993)
3. exchange.topic

创建exchange.direct类型的交换机

![exchange.direct](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033007.png)

exchange.direct

将exchange.direct交换机绑定对应的Queues队列

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033008.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033009.png)

依次绑定好之后，我们测试通过Exchange往指定的队列发消息，

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033010.png)

点击进入到队列Queues选项

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033011.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033012.png)

创建exchange.fanout类型的交换机

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033013.png)

添加好交换机之后，我们需要绑定队列，因为是fanout类型的交换机，无论路由键是什么，都会把消息派发出去，本例Routing Key与队列名保持一致。（fanout就没必要指定路由键了把）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033014.png)

测试fanout类型的交换机发送Queues消息

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033015.png)

切换到Queues队列，查看接收到的消息请求，如下发现每个队列都接收到了消息，也就是说fanout类型的Exchange发消息的时候，无论路由键Routing Key用哪一个，写或不写，所有的队列都会收到消息。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033016.png)

# P254、商城业务消息队列-Topic- Exchange

创建toptic类型的交换机，距我目前工作的一个情况来看，这是在我们这个项目中开发人员自我配置最多的一个类型的交换机，比如说发短信这块内容。开发人员需要自己配置一些短信模板，并将短信模板导入到mq中。

创建exchange.topic类型的交换机。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033017.png)

toptic这种类型的交换机，主题订阅式，指定具体的路由键Routing Key

![绑定路由规则：Toptic](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033018.png)

绑定路由规则：Toptic

指定类型：路由匹配规则

**#**：代表匹配0个或多个单词（字符）

*：代表前面必须有一个单词或字符

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033019.png)

点击创建的exchange.toptic进行队列绑定，路由键Routing Key：**`atguigu.#`**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033021.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033022.png)

按照规则绑定关系如下图：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031033023.png)



# P255、消息队列-SpringBoot整合RabbitMQ
> 整合步骤

1. 引入spring- boot-starter-amqp的pox依赖（俗称场景启动器）
2. application.yml配置
3. 测试RabbitMQ
   1. AmqpAdmin：管理组件
   2. RabbitTemplate：消息发送处理组件

```java
使用 RabbitMQ步骤
1、引入spring- boot-starter-amqp的pox依赖（俗称场景启动器）RabbitAutoConfiguration就会自动生效
2、给容器中自动配置了
     RabbitTemplate、AmqpAdmin、CachingConnectionFactory、 RabbitMessagingTemplatej
     所有的属性都是spring. rabbitmq
         @ConfigurationProperties(prefix = "spring. rabbi tmq")
         public class RabbitProperties
3、给配置文件中配置spring.rabbitmq 信息
4、@EnableRabbit: @EnabLeXxxxx开启功能
```



引入spring- boot-starter-amqp的pox依赖（俗称场景启动器）

```java
        <!--RabbitMQ-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
```

在gulimall-order中的application.properties中配置rabbitmq的相关属性

```json
spring.rabbitmq.host=192.168.56.10
spring.rabbitmq.port=5672
spring.rabbitmq.virtual-host=/
```

# P256、消息队列-AmqpAdmin使用-使用Java代码创建交换机-队列以及他们之间的绑定关系

前面我们整合了RabbitMq，接下来我们就来测试它的第一个属性，AmqpAdmin的使用，它是一个管理组件，它可以帮我们来创建队列，绑定关系等等，包括消费者队列，所以我们后台管理这一块儿能做到更改功能。

接下来拿到这个先来测试，第一个创建一个交换机。假设我们现在创建一个交换机，这个交换机的名字我们叫hello-java-exchange。我们使用这个代码创建，我们叫这是我们这个交换机的名字呢，想要创建这个款，我们就把这个AmqpAdmin肯定拿过来。

![image-20220603114113158](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031141224.png)

看看有哪些方法，首先呢，那么可以来声明一个绑定关系，声明一个交换机，声明一个队列，所以我们将来就是用declareExchange这个方法来声明一个交换机。



![image-20220603114323467](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031143649.png)

创建hello-java-exchange交换机

```java
package com.atguigu.gulimall.order;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;

@Slf4j
@RunWith(SpringRunner.class)
@SpringBootTest
public class GulimallOrderApplicationTests {

    @Autowired
    AmqpAdmin amqpAdmin;
    /**
     * 1、测试RabbitMQ之如何通过代码的方式创建Exchange[hello.java.exchange]、Queue、Bingding绑定关系
     *      方式一：使用RabbitAutoConfiguration自动配置中的AmqpAdmin进行创建
     * 2、测试如何收发消息
     */
    // public DirectExchange(String name, boolean durable, boolean autoDelete, Map<String, Object> arguments)
    @Test
    public void createExchange() {
        // 传个名字就可以了 后面那2个值都是默认设置了的
        DirectExchange directExchange = new DirectExchange("hello-java-exchange", true, false);
        amqpAdmin.declareExchange(directExchange);
        log.info("Exchange[{}]创建成功", "hello-java-exchange");
    }

}

```

RabbitMq管理台查看我们刚刚创建好的交换机hello-java-exchange，连接失败的一定要看看5672端口有没有映射成功，使用云服务器的去安全组放开端口访问权限，空指针测试类加:@RunWith(SpringRunner.class)。

![image-20220603115731483](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031157640.png)

创建hello-java-queue队列

```java
    @Test
    public void createQueue() {
        // public Queue(String name, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments)
        // 选择import org.springframework.amqp.core.Queue;
        Queue queue = new Queue("hello-java-queue", true, false, false);
        amqpAdmin.declareQueue(queue);
        log.info("Queue队列[{}]创建成功", "hello-java-queue");
    }
```

[RabbitMQ Management](http://192.168.56.10:15672/#/queues) 管理台查看刚才创建的hello-java-queue队列

![image-20220603120645590](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031206753.png)

创建交换机hello-java-exchange与队列hello-java-queue之间的绑定关系

```java
    /**
     * 创建交换机hello-java-exchange与队列hello-java-queue之间的绑定关系
     */
    @Test
    public void createBinding() {
        // import org.springframework.amqp.core.Binding;
        // public Binding(String destination【目的地】,
        // DestinationType destinationType【目的地类型】,
        // String exchange【交换机】,
        // String routingKey【路由键】,
        // Map<String, Object> arguments【自定义参数】)
        //将exchange指定的交换机和destination目的地进行绑定，使用routingKey作为指定的路由键
        Binding binding = new Binding("hello-java-queue", Binding.DestinationType.QUEUE, "hello-java-exchange",
                "hello.java", null);
        amqpAdmin.declareBinding(binding);
        log.info("Binding绑定关系[{}]创建成功", "hello-java-binding");

    }
```

如图，通过管理台查看绑定关系。

![image-20220603121936831](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031219987.png)