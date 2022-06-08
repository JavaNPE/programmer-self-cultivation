# 13_SpringCloud Stream消息驱动

# 一、消息驱动概述

## 为什么出现SpringCloud Stream这项技术的原因：

首先看到消息驱动，我们会想到，消息中间件

- ActiveMQ
- RabbitMQ
- RocketMQ
- Kafka

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939096.png)

存在的问题就是，中台和后台 可能存在两种MQ，那么他们之间的实现都是不一样的，这样会导致多种问题出现，而且上述我们也看到了，目前主流的MQ有四种，我们不可能每个都去学习。

这个时候的痛点就是：有没有一种新的技术诞生，让我们不在关注具体MQ的细节，我们只需要用一种适配绑定的方式，自动的给我们在各种MQ内切换。

这个时候，SpringCloudStream就运营而生，解决的痛点就是屏蔽了消息中间件的底层的细节差异，我们操作Stream就可以操作各种消息中间件了，从而降低开发人员的开发成本。

## SpringCloud Stream消息驱动是什么？

一句话概括：屏蔽底层消息中间件的差异，降低切换版本，统一消息的编程模型。

这就有点像Hibernate，它同时支持多种数据库，同时还提供了Hibernate Session的语法，也就是HQL语句，这样屏蔽了SQL具体实现细节，我们只需要操作HQL语句，就能够操作不同的数据库。

### **什么是SpringCloudStream**

官方定义 SpringCloudStream是一个构件消息驱动微服务的框架。

应用程序通过inputs或者outputs来与SpringCloudStream中binder对象（绑定器）交互。

通过我们配置来binding(绑定)，而SpringCloudStream的binder对象负责与消息中间件交互

所以，我们只需要搞清楚如何与SpringCloudStream交互，就可以方便的使用消息驱动的方式。

通过使用SpringIntegration来连接消息代理中间件以实现消息事件驱动。

SpringCloudStream为一些供应商的消息中间件产品提供了个性化的自动化配置实现，引用了发布-订阅，消费组，分区的三个核心概念

**目前仅支持RabbitMQ 和 Kafka**

## 官网：

[Spring Cloud Stream](https://spring.io/projects/spring-cloud-stream#overview)

[Spring Cloud Stream Reference Documentation](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.1.RELEASE/reference/html/)

Spring Cloud Stream中文指导手册：

[Spring Cloud Stream中文指导手册](https://m.wang1314.com/doc/webapp/topic/20971999.html)

## SpringCloud Stream设计思想？

### 标准MQ

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939098.png)

- 生产者/消费者之间靠消息媒介传递消息内容：Message
- 消息必须走特定的通道：消息通道MessageChannel
- 消息通道里的消息如何被消费呢，谁负责收发处理
    - 消息通道MessageChannel的子接口SubscribableChannel，由MessageHandler消息处理器所订阅

### 为什么用Cloud Stream？

RabbitMQ和Kafka，由于这两个消息中间件的架构上不同。

像RabbitMQ有exchange，kafka有Topic和Partitions分区。

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939099.png)

这些中间件的差异导致我们实际项目开发给我们造成了一定的困扰，我们如果用了两个消息队列的其中一种，后面的业务需求，我们想往另外一种消息队列进行迁移，这时候无疑就是灾难性的，一大堆东西都要推到重新做，因为它根我们的系统耦合了，这时候SpringCloudStream给我们提供了一种解耦的方式

这个时候，我们就需要一个绑定器，可以想成是翻译官，用于实现两种消息之间的转换。

### stream凭什么可以统一底层差异

在没有绑定器这个概念的情况下，我们的SpringBoot应用要直接与消息中间件进行消息交互的时候，由于各消息中间件构建的初衷不同，它们的实现细节上会有较大的差异性。通过定义绑定器作为中间件，**完美的实现了应用程序与消息中间件细节之间的隔离**。

通过向应用程序暴露统一的Channel通道，使得应用程序不需要在考虑各种不同消息中间件的实现。

通过定义绑定器Binder作为中间层，实现了**应用程序与消息中间件细节之间的隔离。**

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939100.png)

### **Binder**

- input：对应消费者
- output：对应生产者

Stream对消息中间件的进一步封装，可以做到代码层面对中间件的无感知，甚至于动态的切换中间件（RabbitMQ切换Kafka），使得微服务开发的高度解耦，服务可以关注更多的自己的业务流程。

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939101.png)

通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离。

Stream中的消息通信方式遵循了发布-订阅模式，Topic主题进行广播，在RabbitMQ中就是Exchange，在Kafka中就是Topic。

### Stream中的消息通信方式遵循了发布-订阅模式

Topic主题进行广播：

- 在RabbitMQ就是Exchange
- 在kafka中就是Topic

## Spring Cloud Stream标准流程套路：

我们的消息生产者和消费者只和Stream交互

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939102.png)

- Binder：很方便的连接中间件，屏蔽差异
- Channel：通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的没接，通过Channel对队列进行配置
- Source和Sink：简单的可以理解为参照对象是SpringCloudStream自身，从Stream发布消息就是输出，接受消息就是输入。

## 编码API和常用注解：

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939103.png)

# 二、案例说明：消息驱动之生产者

前提是已经安装好了RabbitMQ

工程中新建三个子模块：

- cloud-stream-rabbitmq-procider8801，作为消息生产者进行发消息模块
- cloud-stream-rabbitmq-procider8802，消息接收模块
- cloud-stream-rabbitmq-procider8803，消息接收模块
- 新建Module：cloud-stream-rabbitmq-provider8801
- POM
  
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <parent>
            <artifactId>cloud2021</artifactId>
            <groupId>com.youliao.springcloud</groupId>
            <version>1.0-SNAPSHOT</version>
        </parent>
        <modelVersion>4.0.0</modelVersion>
    
        <artifactId>cloud-stream-rabbitmq-procider8801</artifactId>
    
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-server -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
    
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <scope>runtime</scope>
                <optional>true</optional>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </project>
    ```
    
- YML
  
    ```yaml
    server:
      port: 8801
    
    spring:
      application:
        name: cloud-stream-provider
      cloud:
        stream:
          binders: # 在此处配置要绑定的rabbitmq的服务信息；
            defaultRabbit: # 表示定义的名称，用于于binding整合
              type: rabbit # 消息组件类型
              environment: # 设置rabbitmq的相关的环境配置
                spring:
                  rabbitmq:
                    host: localhost
                    port: 5672
                    username: guest
                    password: guest
          bindings: # 服务的整合处理
            output: # 这个名字是一个通道的名称
              destination: studyExchange # 表示要使用的Exchange名称定义
              content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
              binder: defaultRabbit # 设置要绑定的消息服务的具体设置
    
    eureka:
      client: # 客户端进行Eureka注册的配置
        service-url:
          defaultZone: http://localhost:7001/eureka
      instance:
        lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
        lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
        instance-id: send-8801.com  # 在信息列表时显示主机名称
        prefer-ip-address: true     # 访问的路径变为IP地址
    ```
    
- 主启动类StreamMQMain8801
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    /**
     * @Author Dali
     * @Date 2021/07/22 下午 6:59
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    public class StreamMQMain8801 {
        public static void main(String[] args) {
            SpringApplication.run(StreamMQMain8801.class, args);
        }
    }
    ```
    
- 业务类
    - 发送消息接口
      
        ```java
        package com.youliao.springcloud.service;
        
        /**
         * @Author Dali
         * @Date 2021/07/22 下午 7:00
         * @Version 1.0
         * @Description:发送消息接口
         */
        public interface IMessageProvider {
            public String send();
        }
        ```
        
    - 发送消息接口实现类
      
        ```java
        package com.youliao.springcloud.service.impl;
        
        import com.youliao.springcloud.service.IMessageProvider;
        import org.springframework.cloud.stream.annotation.EnableBinding;
        import org.springframework.cloud.stream.messaging.Source;
        import org.springframework.integration.support.MessageBuilder;
        import org.springframework.messaging.MessageChannel;
        
        import javax.annotation.Resource;
        
        import java.util.UUID;
        
        /**
         * @Author Dali
         * @Date 2021/07/22 下午 7:01
         * @Version 1.0
         * @Description: 发送消息接口实现类
         */
        @EnableBinding(Source.class) //定义消息的推送管道
        public class MessageProviderImpl implements IMessageProvider {
            @Resource
            private MessageChannel output; // 消息发送管道
        
            @Override
            public String send() {
                String serial = UUID.randomUUID().toString();
                output.send(MessageBuilder.withPayload(serial).build());
                System.out.println("*****serial: "+serial);
                return null;
            }
        }
        ```
        
    - Controller：定义一个REST接口，调用的时候，发送一个消息
      
        ```java
        package com.youliao.springcloud.controller;
        
        import com.youliao.springcloud.service.IMessageProvider;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.RestController;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/07/22 下午 7:03
         * @Version 1.0
         * @Description
         */
        @RestController
        public class SendMessageController {
            @Resource
            private IMessageProvider messageProvider;
        
            @GetMapping(value = "/sendMessage")
            public String sendMessage() {
                return messageProvider.send();
            }
        }
        ```
    
- 测试
    - 启动7001eureka：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
      
        ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939104.png)
        
        ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939105.png)
        
    - 启动rabbitmq：`rabbitmq-server start` (注意是在sbin目录下执行此命令)
      
        ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Snipaste_2021-07-22_22-28-22.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939106.png)
        
        cmd命令窗口尝试输入： `rabbitmq-plugins enable rabbitmq_management` 
        
        我们进入RabbitAdmin页面，浏览器中输入[http://localhost:15672/](http://localhost:15672/) 用户名和密码均为：**guest**
        
        ![会发现它已经成功创建了一个studyExchange的交换机，这个就是我们上面配置的](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939107.png)
        
        会发现它已经成功创建了一个studyExchange的交换机，这个就是我们上面配置的
        
    - 启动8801访问：[http://localhost:8801/sendMessage](http://localhost:8801/sendMessage) 尝试多次刷新
      
        以后就会通过studyExchange这个交换机进行消息的消费。
        
        我们运行下列代码，进行测试消息发送 `http://localhost:8801/sendMessage`
        
        能够发现消息已经成功被RabbitMQ捕获，这个时候就完成了消息的发送
        
        ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939108.png)
        

# 三、消息驱动之消费者

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939109.png)

- 1、新建Module： cloud-stream-rabbitmq-consumer8802
- 2、POM
  
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <parent>
            <artifactId>cloud2021</artifactId>
            <groupId>com.youliao.springcloud</groupId>
            <version>1.0-SNAPSHOT</version>
        </parent>
        <modelVersion>4.0.0</modelVersion>
    
        <artifactId>cloud-stream-rabbitmq-consumer8802</artifactId>
    
        <dependencies>
            **<dependency><!--Stream-->
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
            </dependency>**
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-server -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <scope>runtime</scope>
                <optional>true</optional>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </project>
    ```
    
- 3、YML
  
    ```yaml
    server:
      port: 8802
    
    spring:
      application:
        name: cloud-stream-consumer
      cloud:
        stream:
          binders: # 在此处配置要绑定的rabbitmq的服务信息；
            defaultRabbit: # 表示定义的名称，用于于binding整合
              type: rabbit # 消息组件类型
              environment: # 设置rabbitmq的相关的环境配置
                spring:
                  rabbitmq:
                    host: localhost
                    port: 5672
                    username: guest
                    password: guest
          bindings: # 服务的整合处理
            input: # 这个名字是一个通道的名称
              destination: studyExchange # 表示要使用的Exchange名称定义
              content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
              binder: defaultRabbit  # 设置要绑定的消息服务的具体设置
    
    eureka:
      client: # 客户端进行Eureka注册的配置
        service-url:
          defaultZone: http://localhost:7001/eureka
      instance:
        lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
        lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
        instance-id: receive-8802.com  # 在信息列表时显示主机名称
        prefer-ip-address: true     # 访问的路径变为IP地址
    ```
    
- 4、主启动类StreamMQMain8802
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    /**
     * @Author Dali
     * @Date 2021/07/22 下午 10:52
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    public class StreamMQMain8802 {
        public static void main(String[] args) {
            SpringApplication.run(StreamMQMain8802.class, args);
        }
    }
    ```
    
- 5、业务类
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.Message;
    import org.springframework.stereotype.Component;
    
    /**
     * @Author Dali
     * @Date 2021/07/22 下午 10:53
     * @Version 1.0
     * @Description
     */
    @Component
    [@EnableBinding(Sink.class)](https://www.notion.so/13_SpringCloud-Stream-ad6f83d2714746789989240e0772027f)
    public class ReceiveMessageListenerController {
        @Value("${server.port}")
        private String serverPort;
    
        @StreamListener(Sink.INPUT)
        public void input(Message<String> message) {
            System.out.println("消费者1号,----->接受到的消息: " + message.getPayload() + "\t  port: " + serverPort);
        }
    }
    ```
    
- 6、测试8801发送8802接收消息：[http://localhost:8801/sendMessage](http://localhost:8801/sendMessage)
  
    启动以下微服务：
    
    ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939110.png)
    
    idea控制台输出打印：
    
    ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939111.png)
    
    ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939112.png)
    

# 四、分组消费与持久化（工作中很重要）

## 分组消费：

依照8802，clone出来一份运行8803：cloud-stream-rabbitmq-consumer8803

- 1、**新建module：cloud-stream-rabbitmq-consumer8803**
- 2、POM
  
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <parent>
            <artifactId>cloud2021</artifactId>
            <groupId>com.youliao.springcloud</groupId>
            <version>1.0-SNAPSHOT</version>
        </parent>
        <modelVersion>4.0.0</modelVersion>
    
        <artifactId>cloud-stream-rabbitmq-consumer8803</artifactId>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            **<dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
            </dependency>**
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!--基础配置-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <scope>runtime</scope>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </project>
    ```
    
- 3、YML
  
    ```yaml
    server:
      port: 8803
    
    spring:
      application:
        name: cloud-stream-consumer
      cloud:
        stream:
          binders: # 在此处配置要绑定的rabbitmq的服务信息；
            defaultRabbit: # 表示定义的名称，用于于binding整合
              type: rabbit # 消息组件类型
              environment: # 设置rabbitmq的相关的环境配置
                spring:
                  rabbitmq:
                    host: localhost
                    port: 5672
                    username: guest
                    password: guest
          bindings: # 服务的整合处理
            input: # 这个名字是一个通道的名称
              destination: studyExchange # 表示要使用的Exchange名称定义
              content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
              binder: defaultRabbit # 设置要绑定的消息服务的具体设置
              group: atguiguA
    
    eureka:
      client: # 客户端进行Eureka注册的配置
        service-url:
          defaultZone: http://localhost:7001/eureka
      instance:
        lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
        lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
        instance-id: receive-8803.com  # 在信息列表时显示主机名称
        prefer-ip-address: true     # 访问的路径变为IP地址
    ```
    
- 4、业务类
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.Message;
    import org.springframework.stereotype.Component;
    
    /**
     * @Author Dali
     * @Date 2021/07/23 上午 10:36
     * @Version 1.0
     * @Description
     */
    
    @Component
    @EnableBinding(Sink.class)
    public class ReceiveMessageListenerController {
        @Value("${server.port}")
        private String serverPort;
    
        @StreamListener(Sink.INPUT)
        public void input(Message<String> message) {
            System.out.println("消费者2号,----->接受到的消息: " + message.getPayload() + "\t  port: " + serverPort);
        }
    }
    ```
    

**启动以下服务：**

- RabbitMQ：消息中间件
- 7001：服务注册
- 8801：消息生产
- 8802：消息消费
- 8803：消息消费

运行后两个问题：

- 有重复消费问题
- 消息持久化问题

## **消费**

测试地址：[http://localhost:8801/sendMessage](http://localhost:8801/sendMessage)

目前8802 、8803同时都收到了，存在重复消费的问题。

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939113.png)

**如何解决：**使用分组和持久化属性 group来解决(重要)。

比如在如下场景中，订单系统我们做集群部署，都会从RabbitMQ中获取订单信息，那如果一个订单同时被两个服务获取到，那么就会造成数据错误，我们得避免这种情况，这时我们就可以使用Stream中的消息分组来解决。

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939114.png)

注意：在Stream中处于同一个group中的多个消费者是竞争关系，就能够保证消息只能被其中一个消费一次

**不同组是可以全面消费的（重复消费）**

**同一组会发生竞争关系，只能其中一个可以消费**

分布式微服务应用为了实现高可用和负载均衡，实际上都会部署多个实例，这里部署了8802 8803

多数情况下，生产者发送消息给某个具体微服务时，只希望被消费一次，按照上面我们启动两个应用的例子，虽然它们同属一个应用，但是这个消息出现了被重复消费两次的情况，为了解决这个情况，在SpringCloudStream中，就提供了 消费组 的概念。

![https://gitee.com/moxi159753/LearningNotes/raw/master/SpringCloud/SpringCloud2020/11_%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8SpringCloudStream/images/image-20200414130034279.png](https://gitee.com/moxi159753/LearningNotes/raw/master/SpringCloud/SpringCloud2020/11_%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8SpringCloudStream/images/image-20200414130034279.png)

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939115.png)

## 分组：

**原理：**

微服务应用放置于同一个group中，就能够保证消息只会被其中一个应用消费一次。**不同的组是可以消费的，同一个组内会发生竞争关系，只有其中一个可以消费。**

### **8802/8803都变成不同组，group两个不同：**

`group: atguiguA、atguiguB`

**8802修改YML**：`group: atguiguA`

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939116.png)

**8803修改YML**：**`group: atguiguB`**

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939117.png)

**我们自己配置**：[http://localhost:15672/#/queues](http://localhost:15672/#/queues)

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939118.png)

分布式微服务应用为了实现高可用和负载均衡，实际上都会部署多个实例,本例我启动了两个消费微服务(8802/8803)
多数情况，生产者发送消息给某个具体微服务时只希望被消费-次, 按照上面我们启动两个应用的例子，虽然它们同属一个应用，但是这个消息出现了被重复消费两次的情况。为了解决这个问题，在Spring Cloud Stream中提供了消费组的概念。

**结论：还是重复消费**

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939119.png)

8802/8803实现了轮询分组，**每次只有一个消费者 8801模块的发的消息只能被8802或8803其中一个接收到，这样避免了重复消费。**

### 8802/8803都变成相同组，group两个相同：

`group: atguiguA`

8802修改YML：`group: atguiguA`

8803修改YML：`group: atguiguA`

启动：以下服务

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939120.png)

浏览器输入：[http://localhost:8801/sendMessage](http://localhost:8801/sendMessage)尝试多次（2次）发送，然后在控制台查看控制台打印的内容，然后在浏览器中查看MQ的详细信息h[ttp://localhost:15672/](http://localhost:15672/)

![8801有两条记录](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939121.png)

8801有两条记录

![8802只有1条记录](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939122.png)

8802只有1条记录

![8803只有1条记录](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939123.png)

8803只有1条记录

查看我们的：h[ttp://localhost:15672/](http://localhost:15672/)

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/%E5%BD%95%E5%88%B6_2021_07_23_13_30_51_375.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939124.gif)

**说明：**为什么我们的8802和8803的group都改为atguiguA依旧出现了atguiguA和atguiguB。

![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939125.png)

**结论：同一个组的多个微服务实例，每次只会有一个拿到**

## 持久化：

### 案例演示：

通过上述，解决了重复消费问题，再看看持久化，

- 停止8802和8803，并移除8802的group，保留8803的group
- 8801先发送4条消息到RabbitMQ：[http://localhost:8801/sendMessage](http://localhost:8801/sendMessage)
- 8801控制台信息
  
    ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939126.png)
    
- 在启动8802，**无分组属性配置**，后台没有打出来消息（在没有启动8802之前已经发送了4条消息到RabbitMQ）
- 8802控制台信息
  
    ![13_SpringCloud%20Stream%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8%20bc55def9f0754e039ac54f612f546f97/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939127.png)
    
- 在启动8803，**有分组属性配置**，后台打出来了MQ上的消息
- 8803控制台信息
  
    ![这就说明消息已经被持久化了，等消费者登录后，会自动从消息队列中获取消息进行消费](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081939128.png)
    
    这就说明消息已经被持久化了，等消费者登录后，会自动从消息队列中获取消息进行消费