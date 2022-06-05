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
5、监听消息:使用@RabbitListener;必须有@EnableRabbit注解
    @RabbitListener:类+方法上
	@RabbitHandler:标在方法上


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

# P257、消息队列- RabbitTemplate的使用

因为我们之前已经引入了spring- boot-starter-amqp的pox依赖（俗称场景启动器）SpringBoot自动配置RabbitAutoConfiguration就会自动生效，给容器中自动配置了RabbitTemplate、AmqpAdmin、CachingConnectionFactory、 RabbitMessagingTemplatej等，我们通过@Autowired可以直接使用RabbitTemplate来进行发送消息。

```java
    @Autowired
    RabbitTemplate rabbitTemplate;

    @Test
    public void sendMessageTemplate() {
        // 1、发送消息
        String msg = "Hello World!";
        rabbitTemplate.convertAndSend("hello-java-exchange", "hello.java", msg);
        log.info("发送的消息内容为：【{}】", msg);
    }
```

通过MQ的后台我们可以看到，点击hello-java-queue找到GetMessage(s)

![image-20220603152352058](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031523235.png)

通过MQ发送对象，进行传输我们来看序列化之前和转成JSON之后对象的传输之间的变化。

```java

    @Autowired
    RabbitTemplate rabbitTemplate;

    @Test
    public void sendMessageTemplate() {
        // 发送消息，如果发送的消息是个对象，我们会使用序列化机制，将对象写出去。对象必须实现Serializable
        OrderReturnReasonEntity reasonEntity = new OrderReturnReasonEntity();
        reasonEntity.setId(1L);
        reasonEntity.setCreateTime(new Date());
        reasonEntity.setName("赫点茶");
        String jsonString = JSONObject.toJSONString(reasonEntity);
        // 1、发送消息
        String msg = "Hello World!";
        rabbitTemplate.convertAndSend("hello-java-exchange", "hello.java", jsonString);
        log.info("发送的消息内容为：【{}】", jsonString);
    }
```

![image-20220603155143031](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031551154.png)

如果不使用阿里巴巴出品的fastjson工具将实体对象转成JSON，我们还可以使用RabbitMQ中SpringBoot给我们提供的自动配置组件来进行对象的转换，写一个config类，添加一个配置类，如下

```java
package com.atguigu.gulimall.order.config;

import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Author Dali
 * @Date 2022/6/3 15:40
 * @Version 1.0
 * @Description: RabbitMQ消息格式转换组件
 */
@Configuration
public class MyRabbitConfig {

    /**
     * RabbitMQ消息格式转换组件
     *
     * @return
     */
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}

```

然后我们将代码写成这样

```java
    @Autowired
    RabbitTemplate rabbitTemplate;

    @Test
    public void sendMessageTemplate() {
        // 发送消息，如果发送的消息是个对象，我们会使用序列化机制，将对象写出去。对象必须实现Serializable
        OrderReturnReasonEntity reasonEntity = new OrderReturnReasonEntity();
        reasonEntity.setId(1L);
        reasonEntity.setCreateTime(new Date());
        reasonEntity.setName("绿妹");
        //String jsonString = JSONObject.toJSONString(reasonEntity);
        // 1、发送消息
        String msg = "Hello World!";
        rabbitTemplate.convertAndSend("hello-java-exchange", "hello.java", reasonEntity);
        log.info("发送的消息内容为：【{}】", reasonEntity);
    }

```

![mq](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031558849.png)

项目中数据的传输一般还是比较推荐JSON的形式，阿里巴巴出品的fastjson目前个人所处项目中用到的还是比较多的，组件配置的方式相对来说有其的优越性，那么你会怎么选？



# P258、商城业务消息队列-RabbitListener&RabbitHandler接收消息

```
监听消息:使用@RabbitListener;必须有@EnableRabbit
 @RabbitListener:类+方法上(监听哪些队列)
 @RabbitHandler:标在方法上（重载区分不同的消息）
```

接下来我们来测试一下接收消息，首先我们要接收来自于这个队列里面的消息，那么就需要来监听这个队列里面的内容，想要监听队列这件事情呢，Spring也会做的非常简单，Spring为我们抽取了一个注解，叫RabbitListener来看一下，这个注解呢，翻译过来它就叫rabbit的监听器，它的作用就是来监听我们指定的队列，只要这个里边有内容，我们就可以收到内容。



先使用上面的测试类中的代码发送消息



![image-20220603164710092](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031647290.png)



使用@RabbitListener(queues = {"hello-java-queue"})注解监听队列中的消息如下图所示

![image-20220603165123181](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031651348.png)

# P259、商城业务消息队列可靠投递发送端确认

## RabbitMQ消息确认机制-可靠抵达

* 保证消息不丢失，可靠抵达，可以使用事务消息，性能下降250倍，为此引入确认机制。
* **publisher confirmCallback**确认模式。
* **publisher returnCallback**未投递到queue退回模式。
* **consumer ack**机制。

![image-20220603205753923](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206032057970.png)

### 1、可靠抵达-ConfirmCallback：确认回调

可靠抵达是在生成者publisher发送至代理对象Broker过程中

* `spring.rabbitmq.publisher-confirms=true`
  * 在创建connectionFactory的时候设置PublisherConfirms(true)选项，开启confirmcallback。
  * CorrelationData:用来表示当前消息唯一性。
  * 消息只要被broker接收到就会执行confirmCallback,如果是cluster模式，需要所有broker接收到才会调用confirmCalback。
  * 被broker接收到只能表示message已经到达服务器，并不能保证消息一定会被投递到目标queue里。所以需要用到接下来的returnCallback。

首先在gulimall-order中的application.properties配置文件中配置

```yaml
spring.rabbitmq.host=192.168.56.10
spring.rabbitmq.port=5672
spring.rabbitmq.virtual-host=/

# 开启发送端确认（以下是新加的）
spring.rabbitmq.publisher-confirms=true
```

代码1：

```java
package com.atguigu.gulimall.order.config;

import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;

import javax.annotation.PostConstruct;

/**
 * @Author Dali
 * @Date 2022/6/3 15:40
 * @Version 1.0
 * @Description: RabbitMQ消息格式转换组件
 */
@Configuration
public class MyRabbitConfig {

    @Autowired
    RabbitTemplate rabbitTemplate;

    /**
     * RabbitMQ消息格式转换组件
     *
     * @return
     */
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }

    /**
     * 定制RabbitTemplate
     * 步骤一： 开启发送端确认 spring.rabbitmq.publisher-confirms=true
     * 步骤二： 设置确认回调
     */
    @PostConstruct // MyRabbitConfig对象创建完成之后，才执行这个方法
    public void initRabbitTemplate() {
        rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
            /**
             *
             * @param correlationData 当前消息的唯一关联数据（这个消息的唯一id）
             * @param ack 消息是否成功收到
             * @param cause 失败的原因
             */
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                System.out.println("confirm...correlationData[" + correlationData + "]==>ack[" + ack + "]==>cause[" + cause + "]");
            }
        });
    }
}

```

代码2：

```java
package com.atguigu.gulimall.order.controller;

import com.atguigu.gulimall.order.entity.OrderEntity;
// 省略

import java.util.Date;
import java.util.UUID;

/**
 * @Author Dali
 * @Date 2022/6/4 6:23
 * @Version 1.0
 * @Description
 */
@RestController
public class RabbitController {
    @Autowired
    RabbitTemplate rabbitTemplate;

    @GetMapping("/sendMq")
    public String sendMq(@RequestParam(value = "num", defaultValue = "10") Integer num) {
        for (int i = 0; i < num; i++) {
            if (i % 2 == 0) {
                OrderReturnReasonEntity reasonEntity = new OrderReturnReasonEntity();
                reasonEntity.setId(1L);
                reasonEntity.setCreateTime(new Date());
                reasonEntity.setName("赫点茶--" + i);
                rabbitTemplate.convertAndSend("hello-java-exchange", "hello.java", reasonEntity);
            } else {
                OrderEntity orderEntity = new OrderEntity();
                orderEntity.setOrderSn(UUID.randomUUID().toString());
                rabbitTemplate.convertAndSend("hello-java-exchange", "hello.java", orderEntity);
            }
        }
        return "ok";
    }
}

```

启动gulimall-order服务，浏览器访问：[localhost:9010/sendMq](http://localhost:9010/sendMq)，回车，页面返回一个ok，然后去idea的管理台查看日志，发现ack为true，说明 ，可靠抵达-ConfirmCallback：确认回调了。只要消息抵达Broker代理服务器那么ack返回的就是true，跟是否接收到消息无必然关联。

![image-20220603205753923](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206040638915.png)

![image-20220604063501614](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206040635816.png)

### 2、可靠抵达-ReturnCallback

同样是参考下图，可靠抵达ReturnCallback是在e->q阶段，也就是Exchange交换机将消息发送给队列Queue的过程中。

![image-20220603205753923](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206040652184.png)

同样我们使用可靠抵达-ReturnCallback的话，首先需要在gulimall-order中的application.properties配置文件中开启以下这两个配置

```yaml
# 开启发送端消息抵达队列的确认
spring.rabbitmq.publisher-returns=true
# 只要抵达队列，以异步的方式发送优先回调我们这个returnConfirm
spring.rabbitmq.template.mandatory=true
```

**ConfirmCallback**模式只能保证消息到达broker也就是从publish到broker这个过程中，不能保证消息准确投递到目标队列queue里，在有些业务场景下，我们需要保证消息一定要投递到目标队列queue里，比时就需要用到return退回模式。

这样如果未能投递到目标queue里将调用returnCallback，可以记录下详细的投递数据，定期的巡检或者自动纠错都需要这些数据。



```java
        // 设置消息抵达队列的确认回调
        rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
            /**
             *  只要消息没有投递给指定的队列，就触发这个失败回调
             *
             * @param message   消息投递失败的详细信息
             * @param replyCode 回复的状态码
             * @param replyText 回复的文本内容
             * @param exchange  当时这个消息发给哪个交换机
             * @param routingKey    当时这个消息用的哪个路由键
             */
            @Override
            public void returnedMessage(Message message, int replyCode, String replyText, String exchange,
                                        String routingKey) {
                System.out.println("Fail Message[" + message + "]==>replyCode[" + replyCode + "]==>replyText[" + replyText +
                        "]==>exchange[" + exchange + "]==>routingKey[" + routingKey + "]");
            }
        });
```

只要消息没有投递给指定的队列，就触发这个失败回调，那么我们只需要将路由键routingKey写错，就可以故意模拟这种投递失败的场景案例了，比如：将sendMq()方法中的路由键故意写成未配置的”hello.22.java“。重启服务，然后浏览器访问[localhost:9010/sendMq](http://localhost:9010/sendMq)，查看idea的控制台

```java
rabbitTemplate.convertAndSend("hello-java-exchange", "hello222.java", orderEntity);
```

![image-20220604072116010](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206040721115.png)

idea控制台输出打印如下内容

```json
2022-06-04 07:23:38.310  INFO 27472 --- [nio-9010-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2022-06-04 07:23:38.318  INFO 27472 --- [nio-9010-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 8 ms
confirm...correlationData[null]==>ack[true]==>cause[null]
Fail Message[(Body:'{"id":null,"memberId":null,"orderSn":"ea5b63a3-7867-4da7-ad41-707c7bde27b5","couponId":null,"createTime":null,"memberUsername":null,"totalAmount":null,"payAmount":null,"freightAmount":null,"promotionAmount":null,"integrationAmount":null,"couponAmount":null,"discountAmount":null,"payType":null,"sourceType":null,"status":null,"deliveryCompany":null,"deliverySn":null,"autoConfirmDay":null,"integration":null,"growth":null,"billType":null,"billHeader":null,"billContent":null,"billReceiverPhone":null,"billReceiverEmail":null,"receiverName":null,"receiverPhone":null,"receiverPostCode":null,"receiverProvince":null,"receiverCity":null,"receiverRegion":null,"receiverDetailAddress":null,"note":null,"confirmStatus":null,"deleteStatus":null,"useIntegration":null,"paymentTime":null,"deliveryTime":null,"receiveTime":null,"commentTime":null,"modifyTime":null}' MessageProperties [headers={__TypeId__=com.atguigu.gulimall.order.entity.OrderEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, deliveryTag=0])]==>replyCode[312]==>replyText[NO_ROUTE]==>exchange[hello-java-exchange]==>routingKey[hello222.java]
confirm...correlationData[null]==>ack[true]==>cause[null]
Fail Message[(Body:'{"id":null,"memberId":null,"orderSn":"8c2a3b06-ff6f-4253-a74d-0c51b0b50591","couponId":null,"createTime":null,"memberUsername":null,"totalAmount":null,"payAmount":null,"freightAmount":null,"promotionAmount":null,"integrationAmount":null,"couponAmount":null,"discountAmount":null,"payType":null,"sourceType":null,"status":null,"deliveryCompany":null,"deliverySn":null,"autoConfirmDay":null,"integration":null,"growth":null,"billType":null,"billHeader":null,"billContent":null,"billReceiverPhone":null,"billReceiverEmail":null,"receiverName":null,"receiverPhone":null,"receiverPostCode":null,"receiverProvince":null,"receiverCity":null,"receiverRegion":null,"receiverDetailAddress":null,"note":null,"confirmStatus":null,"deleteStatus":null,"useIntegration":null,"paymentTime":null,"deliveryTime":null,"receiveTime":null,"commentTime":null,"modifyTime":null}' MessageProperties [headers={__TypeId__=com.atguigu.gulimall.order.entity.OrderEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, deliveryTag=0])]==>replyCode[312]==>replyText[NO_ROUTE]==>exchange[hello-java-exchange]==>routingKey[hello222.java]
confirm...correlationData[null]==>ack[true]==>cause[null]
confirm...correlationData[null]==>ack[true]==>cause[null]
Fail Message[(Body:'{"id":null,"memberId":null,"orderSn":"08c93790-8691-48c1-adf9-2654a3bdee3b","couponId":null,"createTime":null,"memberUsername":null,"totalAmount":null,"payAmount":null,"freightAmount":null,"promotionAmount":null,"integrationAmount":null,"couponAmount":null,"discountAmount":null,"payType":null,"sourceType":null,"status":null,"deliveryCompany":null,"deliverySn":null,"autoConfirmDay":null,"integration":null,"growth":null,"billType":null,"billHeader":null,"billContent":null,"billReceiverPhone":null,"billReceiverEmail":null,"receiverName":null,"receiverPhone":null,"receiverPostCode":null,"receiverProvince":null,"receiverCity":null,"receiverRegion":null,"receiverDetailAddress":null,"note":null,"confirmStatus":null,"deleteStatus":null,"useIntegration":null,"paymentTime":null,"deliveryTime":null,"receiveTime":null,"commentTime":null,"modifyTime":null}' MessageProperties [headers={__TypeId__=com.atguigu.gulimall.order.entity.OrderEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, deliveryTag=0])]==>replyCode[312]==>replyText[NO_ROUTE]==>exchange[hello-java-exchange]==>routingKey[hello222.java]
confirm...correlationData[null]==>ack[true]==>cause[null]
confirm...correlationData[null]==>ack[true]==>cause[null]
Fail Message[(Body:'{"id":null,"memberId":null,"orderSn":"48f5c9b7-7957-43e6-b42c-1b1cf43b3c0b","couponId":null,"createTime":null,"memberUsername":null,"totalAmount":null,"payAmount":null,"freightAmount":null,"promotionAmount":null,"integrationAmount":null,"couponAmount":null,"discountAmount":null,"payType":null,"sourceType":null,"status":null,"deliveryCompany":null,"deliverySn":null,"autoConfirmDay":null,"integration":null,"growth":null,"billType":null,"billHeader":null,"billContent":null,"billReceiverPhone":null,"billReceiverEmail":null,"receiverName":null,"receiverPhone":null,"receiverPostCode":null,"receiverProvince":null,"receiverCity":null,"receiverRegion":null,"receiverDetailAddress":null,"note":null,"confirmStatus":null,"deleteStatus":null,"useIntegration":null,"paymentTime":null,"deliveryTime":null,"receiveTime":null,"commentTime":null,"modifyTime":null}' MessageProperties [headers={__TypeId__=com.atguigu.gulimall.order.entity.OrderEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, deliveryTag=0])]==>replyCode[312]==>replyText[NO_ROUTE]==>exchange[hello-java-exchange]==>routingKey[hello222.java]
confirm...correlationData[null]==>ack[true]==>cause[null]
confirm...correlationData[null]==>ack[true]==>cause[null]
Fail Message[(Body:'{"id":null,"memberId":null,"orderSn":"33f2baeb-7f6e-4a48-84e4-60eb25539859","couponId":null,"createTime":null,"memberUsername":null,"totalAmount":null,"payAmount":null,"freightAmount":null,"promotionAmount":null,"integrationAmount":null,"couponAmount":null,"discountAmount":null,"payType":null,"sourceType":null,"status":null,"deliveryCompany":null,"deliverySn":null,"autoConfirmDay":null,"integration":null,"growth":null,"billType":null,"billHeader":null,"billContent":null,"billReceiverPhone":null,"billReceiverEmail":null,"receiverName":null,"receiverPhone":null,"receiverPostCode":null,"receiverProvince":null,"receiverCity":null,"receiverRegion":null,"receiverDetailAddress":null,"note":null,"confirmStatus":null,"deleteStatus":null,"useIntegration":null,"paymentTime":null,"deliveryTime":null,"receiveTime":null,"commentTime":null,"modifyTime":null}' MessageProperties [headers={__TypeId__=com.atguigu.gulimall.order.entity.OrderEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, deliveryTag=0])]==>replyCode[312]==>replyText[NO_ROUTE]==>exchange[hello-java-exchange]==>routingKey[hello222.java]
confirm...correlationData[null]==>ack[true]==>cause[null]
confirm...correlationData[null]==>ack[true]==>cause[null]
2022-06-04 07:23:38.413  INFO 27472 --- [ntContainer#0-1] c.a.g.o.s.impl.OrderItemServiceImpl      : 接收到了消息...内容为：{}(Body:'{"id":1,"name":"赫点茶--0","sort":null,"status":null,"createTime":1654298618338}' MessageProperties [headers={spring_listener_return_correlation=c2bcd4e7-0a02-4e4f-b39e-39d0645a4d4e, __TypeId__=com.atguigu.gulimall.order.entity.OrderReturnReasonEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=hello-java-exchange, receivedRoutingKey=hello.java, deliveryTag=1, consumerTag=amq.ctag-4keUvD75d0jiQJ_r7kVqTQ, consumerQueue=hello-java-queue])==>类型：class org.springframework.amqp.core.Message
2022-06-04 07:23:38.414  INFO 27472 --- [ntContainer#0-1] c.a.g.o.s.impl.OrderItemServiceImpl      : 接收到了消息...内容为：{}(Body:'{"id":1,"name":"赫点茶--2","sort":null,"status":null,"createTime":1654298618387}' MessageProperties [headers={spring_listener_return_correlation=c2bcd4e7-0a02-4e4f-b39e-39d0645a4d4e, __TypeId__=com.atguigu.gulimall.order.entity.OrderReturnReasonEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=hello-java-exchange, receivedRoutingKey=hello.java, deliveryTag=2, consumerTag=amq.ctag-4keUvD75d0jiQJ_r7kVqTQ, consumerQueue=hello-java-queue])==>类型：class org.springframework.amqp.core.Message
2022-06-04 07:23:38.414  INFO 27472 --- [ntContainer#0-1] c.a.g.o.s.impl.OrderItemServiceImpl      : 接收到了消息...内容为：{}(Body:'{"id":1,"name":"赫点茶--4","sort":null,"status":null,"createTime":1654298618389}' MessageProperties [headers={spring_listener_return_correlation=c2bcd4e7-0a02-4e4f-b39e-39d0645a4d4e, __TypeId__=com.atguigu.gulimall.order.entity.OrderReturnReasonEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=hello-java-exchange, receivedRoutingKey=hello.java, deliveryTag=3, consumerTag=amq.ctag-4keUvD75d0jiQJ_r7kVqTQ, consumerQueue=hello-java-queue])==>类型：class org.springframework.amqp.core.Message
2022-06-04 07:23:38.414  INFO 27472 --- [ntContainer#0-1] c.a.g.o.s.impl.OrderItemServiceImpl      : 接收到了消息...内容为：{}(Body:'{"id":1,"name":"赫点茶--8","sort":null,"status":null,"createTime":1654298618392}' MessageProperties [headers={spring_listener_return_correlation=c2bcd4e7-0a02-4e4f-b39e-39d0645a4d4e, __TypeId__=com.atguigu.gulimall.order.entity.OrderReturnReasonEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=hello-java-exchange, receivedRoutingKey=hello.java, deliveryTag=4, consumerTag=amq.ctag-4keUvD75d0jiQJ_r7kVqTQ, consumerQueue=hello-java-queue])==>类型：class org.springframework.amqp.core.Message
2022-06-04 07:23:38.415  INFO 27472 --- [ntContainer#0-1] c.a.g.o.s.impl.OrderItemServiceImpl      : 接收到了消息...内容为：{}(Body:'{"id":1,"name":"赫点茶--6","sort":null,"status":null,"createTime":1654298618390}' MessageProperties [headers={spring_listener_return_correlation=c2bcd4e7-0a02-4e4f-b39e-39d0645a4d4e, __TypeId__=com.atguigu.gulimall.order.entity.OrderReturnReasonEntity}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=hello-java-exchange, receivedRoutingKey=hello.java, deliveryTag=5, consumerTag=amq.ctag-4keUvD75d0jiQJ_r7kVqTQ, consumerQueue=hello-java-queue])==>类型：class org.springframework.amqp.core.Message

```

# P260、消息队列-可靠投递消费端确认

## 可靠抵达Ack消息确认机制

同样是参考下图，这部分主要发生在q->c阶段，也就是队列Queue发送消息到consume这个过程中的消息确认机制。

![image-20220603205753923](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206051027258.png)

消费者获取到消息，成功处理，可以回复Ack给Broker

- basic.ack用于肯定确认;  broker将移除此消息
- basic.nack用于否定确认; 可以指定broker是否丢弃此消息，可以批量
- basic.reject用于否定确认; 同上，但不能批量

默认，消息被消费者收到，就会从broker的queue中移除，queue无消费者，消息依然会被存储，直到消费者消费。

消费者（C）收到消息，默认会自动ack，但是如果无法确定此消息是否被处理完成，或者成功处理，我们可以开启手动ack模式：

- 消息处理成功，ack()，接受下一个消息， 此消息broker就会移除。
- 消息处理失败，nack()/reject（)，重新发送给其他人进行处理，或者容错处理后ack
- 消息一直没有调用ack/nack方法，broker认为此消息正在被处理，不会投递给别人，此时客户端断开，消息不会被broker移除， 会投递给别人

```yaml
# 手动ack消息
spring.rabbitmq.listener.simple.acknowledge-mode=manual
```

总结：结合发送端的消息确认机制和消费端的消息确认机制（即P259和P260），就能最终保证我们的消息一定发出去，也一定会被别人接收到，即使没有接收到，还可以进行重写发送，保证消息的不丢失。
