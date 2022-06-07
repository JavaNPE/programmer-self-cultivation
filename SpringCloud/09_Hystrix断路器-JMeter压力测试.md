# 09_Hystrix断路器-JMeter压力测试

springcloud升级服务替换

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901316.png)

# 前言：分布式系统面临的问题？

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901318.png)

## 什么是服务雪崩：

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进引起系统崩溃，所谓的“雪崩效应”。

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。

所以，通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量,然后这个有问题的模块还调用了其他的模块,这样就会发生级联故障，或者叫雪崩。

# 一、Hystrix断路器概述

Hystrix官宣停更，官方推荐使用：resilence4j替换，同时国内Spring Cloud Alibaba 提出了**Sentinel**实现熔断和限流。

停更进维：被动修复bugs、不再接受合并请求、不再发布新版本。

## 1、Hystrix是什么：

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障之后,通过断路器的故障监控(类似熔断保险丝)，向调用方返回一个符合预期的、可处理的备选响应(FallBack) ，而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

## 2、Hystrix断路器作用：

- 服务降级
- 服务熔断
- 接近实时的监控
- 。。。。。

## 3、Hystrix官网相关资料

官网资料：[https://github.com/Netflix/Hystrix/wiki/How-To-Use](https://github.com/Netflix/Hystrix/wiki/How-To-Use)

源码地址：[https://github.com/Netflix/Hystrix](https://github.com/Netflix/Hystrix)

# 二、Hystrix重要概念

## 1、**服务降级：**

fallback，假设对方服务不可用了，那么至少需要返回一个兜底的解决方法，即向服务调用方返回一个符合预期的，可处理的备选响应。

例如：服务器忙，请稍候再试，不让客户端等待并立刻返回一个友好提示，fallback。

### 1.1 **哪些情况会触发降级：以下四种情况**

- 程序运行异常
- 超时
- 服务熔断触发服务降级
- 线程池/信号量打满也会导致服务降级

## 2、**服务熔断：（就是保险丝）**

break，类比保险丝达到了最大服务访问后，直接拒绝访问，拉闸断电，然后调用服务降级的方法并返回友好提示。

**一般过程：服务降级 -> 服务熔断 -> 恢复调用链路**

## 3、服务限流：

flowlimit，秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行。

# 三、Hystrix案例：支付微服务构建

## 1、支付微服务构建

- 新建cloud-provider-hystrix-payment8001
  
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
    
        <artifactId>cloud-provider-hystrix-payment8001</artifactId>
    
        <dependencies>
            <!--新增hystrix-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
    
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
    
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
    
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
      port: 8001
    
    eureka:
      client:
        register-with-eureka: true    #表识不向注册中心注册自己
        fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
        service-url:
          # defaultZone: http://eureka7002.com:7002/eureka/    #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
          defaultZone: http://eureka7001.com:7001/eureka/  #使用单机版，没有用集群节省时间
    #  server:
    #    enable-self-preservation: false
    spring:
      application:
        name: cloud-provider-hystrix-payment
    #    eviction-interval-timer-in-ms: 2000
    ```
    
- 4、主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/06 下午 6:10
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableEurekaClient
    public class PaymentHystrixMain8001 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentHystrixMain8001.class, args);
        }
    }
    ```
    
- 5、业务类
    - service
      
        ```java
        package com.youliao.springcloud.service;
        
        import org.springframework.stereotype.Service;
        
        import java.util.concurrent.TimeUnit;
        
        /**
         * @Author Dali
         * @Date 2021/07/06 下午 6:28
         * @Version 1.0
         * @Description
         */
        @Service
        public class PaymentService {
            /**
             * 正常访问 肯定ok
             *
             * @param id
             * @return
             */
            public String paymentInfo_OK(Integer id) {
                return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_OK,id：  " + id + "\t" + "哈哈哈";
            }
        
            //失败
            public String paymentInfo_TimeOut(Integer id) {
                int timeNumber = 3;
                try {
                    TimeUnit.SECONDS.sleep(timeNumber);
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_TimeOut,id：  " + id + "\t" + "呜呜呜" + " 耗时(秒)" + timeNumber;
            }
        
        }
        ```
        
    - controller
      
        ```java
        package com.youliao.springcloud.controller;
        
        import com.youliao.springcloud.service.PaymentService;
        import lombok.extern.slf4j.Slf4j;
        import org.springframework.beans.factory.annotation.Value;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RestController;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/07/06 下午 6:32
         * @Version 1.0
         * @Description
         */
        @RestController
        @Slf4j
        public class PaymentController {
            @Resource
            private PaymentService paymentService;
        
            @Value("${server.port}")
            private String serverPort;
        
            @GetMapping("/payment/hystrix/ok/{id}")
            public String paymentInfo_OK(@PathVariable("id") Integer id) {
                String result = paymentService.paymentInfo_OK(id);
                log.info("*******result:" + result);
                return result;
            }
        
            @GetMapping("/payment/hystrix/timeout/{id}")
            public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
                String result = paymentService.paymentInfo_TimeOut(id);
                log.info("*******result:" + result);
                return result;
            }
        }
        ```
    
- 6、正常测试
  
    1、启动eureka7001（已改成单机版）
    
    2、启动cloud-provider-hystrix-payment8001
    
    3、访问：
    
    - 3.1成功的方法：[http://localhost:8001/payment/hystrix/ok/10](http://localhost:8001/payment/hystrix/ok/10)
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901319.png)
        
    - 3.2每次调用耗费5秒钟：[http://localhost:8001/payment/hystrix/timeout/10](http://localhost:8001/payment/hystrix/timeout/10)
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901320.png)
        

上述module均OK，以上述为根基平台，从正确->错误->降级熔断->恢复

## 2、高并发测试

上述在非高并发情形下，还能勉强满足，but.....

### 2.1 Jmeter压测测试（附：Jmeter的使用教程）

开启[Jmeter](http://www.jmeter.com.cn/)，来20000个并发压死8001，20000个请求都去访问paymentInfo_TimeOut服务

- Jmeter的使用。
  
    安装好之后双击jmeter.bat文件
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901321.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901322.png)
    
    配置：线程数和循环次数
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901323.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901324.png)
    
    选中刚才保存的线程组202002鼠标右键添加→取样器→THHP请求
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901325.png)
    
    设置相关参数：服务器名称或IP、端口号、路径等。
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901326.png)
    
    启动jmeter
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901327.png)
    

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901328.png)

再来一个访问：

访问1：[http://localhost:8001/payment/hystrix/ok/10](http://localhost:8001/payment/hystrix/ok/10)

访问2：[http://localhost:8001/payment/hystrix/timeout/10](http://localhost:8001/payment/hystrix/timeout/10)

看演示结果：

1、两个都在自己转圈圈。

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901329.png)

2、为什么会被卡死：tomcat的默认的工作线程数被打满了，没有多余的线程来分解压力和处理。

### 2.2 Jmeter压测结论

上面还是服务提供者8001自己测试，假如此时外部的消费者80也来访问，那消费者只能干等，最终导致消费端80不满意，服务端8001直接被拖死。

### 2.3 看热闹不嫌弃事大，80新建加入

- 1、新建cloud-consumer-feign-hystrix-order80
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
    
        <artifactId>cloud-consumer-feign-hystrix-order80</artifactId>
    
        <dependencies>
            <!--新增hystrix-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
    
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
      port: 80
    
    eureka:
      client:
        register-with-eureka: true    #表识不向注册中心注册自己
        fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka/
    ```
    
- 4、主启动
  
    ```java
    @SpringBootApplication
    @EnableFeignClients
    public class OrderHystrixMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderHystrixMain80.class, args);
        }
    }
    ```
    
- 5、业务类
    - PaymentHystrixService：
      
        ```java
        package com.youliao.springcloud.service;
        
        import org.springframework.cloud.openfeign.FeignClient;
        import org.springframework.stereotype.Component;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        
        /**
         * @Author Dali
         * @Date 2021/07/07 下午 12:45
         * @Version 1.0
         * @Description
         */
        @Component
        @FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
        public interface PaymentHystrixService {
            @GetMapping("/payment/hystrix/ok/{id}")
            public String paymentInfo_OK(@PathVariable("id") Integer id);
        
            @GetMapping("/payment/hystrix/timeout/{id}")
            public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
        }
        ```
        
    - OrderHystrixController：
      
        ```java
        package com.youliao.springcloud.controller;
        
        import com.youliao.springcloud.service.PaymentHystrixService;
        import jdk.nashorn.internal.ir.IfNode;
        import lombok.extern.slf4j.Slf4j;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RestController;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/07/07 下午 12:55
         * @Version 1.0
         * @Description
         */
        @RestController
        @Slf4j
        public class OrderHystrixController {
            @Resource
            private PaymentHystrixService paymentHystrixService;
        
            @GetMapping("/consumer/payment/hystrix/ok/{id}")
            public String paymentInfo_OK(@PathVariable("id") Integer id){
                String result = paymentHystrixService.paymentInfo_OK(id);
                return result;
            }
        
            @GetMapping("/consumer/payment/hystrix/timeout/{id}")
            public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
                String result = paymentHystrixService.paymentInfo_TimeOut(id);
                return result;
            }
        }
        ```
    
- 6、正常测试：
  
    6.1 启动以下服务
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901331.png)
    
    6.2 测试链接：[http://localhost/consumer/payment/hystrix/ok/10](http://localhost/consumer/payment/hystrix/ok/10)   访问很快
    
- 7、高并发测试：故障：要么转圈圈等待，要么消费端报超时错误。
    - 开启Jmeter进行高并发测试
      
        [线程组202002.jmx](09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/202002.jmx)
        
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901332.png)
        
    
    2W个线程压8001、消费端80微服务再去访问正常的OK微服务8001地址。消费者80，呜呜呜，要么转圈圈等待，要么消费端报超时错误
    
    ![要么转圈圈等待](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901333.png)
    
    要么转圈圈等待
    
    ![要么消费端报超时错误](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901334.png)
    
    要么消费端报超时错误
    
- 8、上述：故障现象和导致原因
  
    8001同一层次的其他接口服务被困死，因为tomcat线程里面的工作线程已经被挤占完毕
    
    80此时调用8001，客户端访问响应缓慢，转圈圈
    
- 9、上诉结论：正因为有上述故障或不佳表现，才有我们的降级/容错/限流等技术诞生

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901335.png)

## 3、服务降级

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901336.png)

### 3.1 Hystrix之服务降级cloud-provider-hystrix-payment8001支付侧：fallback

- 1、降低配置：@HystrixCommand
- 2、cloud-provider-hystrix-payment8001：先从自身找问题
  
    设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作服务降级fallback。
    
    ```java
    
    ```
    
- 3、cloud-provider-hystrix-payment8001——fallback
    - 业务类启用：@HystrixCommand报异常后如何处理
      
        ```java
        package com.youliao.springcloud.service;
        
        import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
        import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
        import org.springframework.stereotype.Service;
        
        import java.util.concurrent.TimeUnit;
        
        /**
         * @Author Dali
         * @Date 2021/07/06 下午 6:28
         * @Version 1.0
         * @Description
         */
        @Service
        public class PaymentService {
            /**
             * 正常访问 肯定ok
             *
             * @param id
             * @return
             */
            public String paymentInfo_OK(Integer id) {
                return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_OK,id：  " + id + "\t" + "哈哈哈";
            }
        
            //服务降级：3秒以内走正常的逻辑，（业务逻辑需要5秒）超过3秒，则出现超时的错误（或者计算异常），我们走兜底方案：paymentInfo_TimeOutHandler
            @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler", commandProperties = {
                    @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")  //峰值：3秒
            })
            public String paymentInfo_TimeOut(Integer id) {
                //int timeNumber = 5; //业务逻辑需要5秒，超时异常
                int age = 10 / 0;   //计算异常
                /*try {
                    TimeUnit.SECONDS.sleep(timeNumber);
                } catch (Exception e) {
                    e.printStackTrace();
                }*/
                return "线程池：" + Thread.currentThread().getName() + "   paymentInfo_TimeOut,id：  " + id + "\t" + "哈哈哈" + " 耗时(秒)" /*+ timeNumber*/;
            }
        		//兜底的方案：paymentInfo_TimeOutHandler
            public String paymentInfo_TimeOutHandler(Integer id) {
                return "线程池：" + Thread.currentThread().getName() + "   系统繁忙或者运行报错,请稍后再试,id：  " + id + "\t" + "/(ㄒoㄒ)/~~";
        
            }
        
        }
        ```
        
    - 主启动类激活：添加新注解@EnableCircuitBreaker
      
        ```java
        package com.youliao.springcloud;
        
        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
        import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
        
        /**
         * @Author Dali
         * @Date 2021/07/06 下午 6:10
         * @Version 1.0
         * @Description
         */
        @SpringBootApplication
        @EnableEurekaClient
        @EnableCircuitBreaker
        public class PaymentHystrixMain8001 {
            public static void main(String[] args) {
                SpringApplication.run(PaymentHystrixMain8001.class, args);
            }
        }
        ```
        
    - 测试：[http://localhost:8001//payment/hystrix/timeout/10](http://localhost:8001//payment/hystrix/timeout/10)
      
        小圈圈转了转，因为是计算异常，服务降级最后还是走我们的兜底方案
        
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901337.png)
        
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901338.png)
        

### 3.2 Hystrix之服务降级cloud-consumer-feign-hystrix-order80订单侧：fallback【一般做服务降级都是在客户（消费）端进行】

80订单微服务，也可以更好的保护自己，自己也依样画葫芦进行客户端降级保护。

我们自己配置过的热部署方式对java代码的改动明显，但对@HystrixCommand内属性的修改建议重启微服务。

- YML
  
    ```yaml
    server:
      port: 80
    
    eureka:
      client:
    #    register-with-eureka: true    #表识不向注册中心注册自己
        register-with-eureka: false    #表识不向注册中心注册自己
    #    fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka/
    
    #spring:
    #  application:
    #    name: cloud-provider-hystrix-order
    
    feign:
      hystrix:
        enabled: true #如果处理自身的容错就开启。开启方式与生产端不一样。
    ```
    
- 主启动：@EnableHystrix
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    import org.springframework.cloud.netflix.hystrix.EnableHystrix;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    import org.springframework.cloud.openfeign.FeignClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/07 下午 12:42
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableFeignClients
    @EnableHystrix
    public class OrderHystrixMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderHystrixMain80.class, args);
        }
    }
    ```
    
- 业务类
  
    ```java
    package com.youliao.springcloud.controller;
    
    import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
    import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
    import com.youliao.springcloud.service.PaymentHystrixService;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    import javax.annotation.Resource;
    
    /**
     * @Author Dali
     * @Date 2021/07/07 下午 12:55
     * @Version 1.0
     * @Description
     */
    @RestController
    @Slf4j
    public class OrderHystrixController {
        @Resource
        private PaymentHystrixService paymentHystrixService;
    
        @GetMapping("/consumer/payment/hystrix/ok/{id}")
        public String paymentInfo_OK(@PathVariable("id") Integer id) {
            String result = paymentHystrixService.paymentInfo_OK(id);
            return result;
        }
    
        @GetMapping("/consumer/payment/hystrix/timeout/{id}")
        @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
                @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")  //1.5秒钟以内就是正常的业务逻辑
        })//峰值：1.5秒 如果超过1.5秒 采用我们兜底的方法：paymentTimeOutFallbackMethod，如果没有超过走我们正常的逻辑
    
        public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
            String result = paymentHystrixService.paymentInfo_TimeOut(id);
            return result;
        }
    
        //兜底方法
        public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
            return "我是消费者80，对付支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,(┬＿┬)";
        }
    
    }
    ```
    

### 3.3 对于3.1和3.2各自进行服务降级目前存在的问题

由于cloud-provider-hystrix-payment8001服务提供端（支付端）进行fallback（服务降级）和cloud-consumer-feign-hystrix-order80订单侧（消费端）也进行fallback（服务降级）**会导致每个业务方法对应一个兜底的方法，使得代码比较膨胀。**

目前异常处理的方法，和业务代码耦合，这就造成耦合度比较高。

解决方法就是使用统一的服务降级方。

那么我们要做的就是把统一的和自定义的分开。

### 3.4 关于各服务之间都进行服务降级的问题的解决方案

### 3.4.1 **方法1：解决代码膨胀的问题**

除了个别重要核心业务有专属，其它普通的可以通过`@DefaultProperties(defaultFallback = "")`，这样通用的和独享的各自分开，避免了代码膨胀，合理减少了代码量。

可以在Controller处设置 `@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")`

- Controller：OrderHystrixController
  
    ```java
    package com.youliao.springcloud.controller;
    
    import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
    import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
    import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
    import com.youliao.springcloud.service.PaymentHystrixService;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    import javax.annotation.Resource;
    
    /**
     * @Author Dali
     * @Date 2021/07/07 下午 12:55
     * @Version 1.0
     * @Description
     */
    @RestController
    @Slf4j
    @DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")  //全局的
    public class OrderHystrixController {
        @Resource
        private PaymentHystrixService paymentHystrixService;
    
        @GetMapping("/consumer/payment/hystrix/ok/{id}")
        public String paymentInfo_OK(@PathVariable("id") Integer id) {
            String result = paymentHystrixService.paymentInfo_OK(id);
            return result;
        }
    
        @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    //    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {    //找特定的兜底方法
    //            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")  //1.5秒钟以内就是正常的业务逻辑
    //    })//峰值：1.5秒 如果超过1.5秒 采用我们兜底的方法：paymentTimeOutFallbackMethod，如果没有超过走我们正常的逻辑
        @HystrixCommand //此处依旧照写@HystrixCommand注解标签，不写的话表示不进行服务降级，反之出现异常进行全局兜底处理
        public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
            int age = 10/0;
            String result = paymentHystrixService.paymentInfo_TimeOut(id);
            return result;
        }
    
        //兜底方法
        public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
            return "我是消费者80，对付支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,(┬＿┬)";
        }
    
        //下面是全局fallback方法
        public String payment_Global_FallbackMethod() {
            return "Global异常处理信息，请稍后再试,(┬＿┬)";
        }
    }
    ```
    
- 测试：[http://localhost/consumer/payment/hystrix/timeout/10](http://localhost/consumer/payment/hystrix/timeout/10)
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901339.png)
    

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901340.png)

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901341.png)

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901342.png)

### 3.4.1 **方法2：解决代码和业务逻辑混乱导致的耦合度高的问题**

我们现在还发现，**兜底的方法** 和 **我们的业务代码**耦合在一块比较混乱，服务降级，客户端去调用服务端，碰上服务端宕机或关闭，本次案例服务降级处理是在客户端80实现完成的，与服务端8001没有关系，只需要为Feign客户端定义的接口添加一个服务降级处理的实现类即可实现解耦。

我们可以在feign调用的时候，增加hystrix的服务降级处理的实现类，这样就可以进行解耦

格式：`@FeignClient(fallback = PaymentFallbackService.class)`

未来我们要面对的异常：

- 运行
- 超时
- 宕机

---

- 再看我们的业务类PaymentController
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901343.png)
    
- 修改cloud-consumer-feign-hystrix-order80
- PaymentFallbackService
  
    根据cloud-consumer-feign-hystrix-order80已经有的PaymentHystrixService接口，重新新建一个类（PaymentFallbackService）实现该接口，统一为接口里面的方法进行异常处理
    
    ```java
    package com.youliao.springcloud.service;
    
    import org.springframework.stereotype.Component;
    
    /**
     * @Author Dali
     * @Date 2021/07/08 上午 11:48
     * @Version 1.0
     * @Description
     */
    @Component
    public class PaymentFallbackService implements PaymentHystrixService {
        @Override
        public String paymentInfo_OK(Integer id) {
            return "-----PaymentFallbackService fall back-paymentInfo_OK ,/(ㄒoㄒ)/~~";
        }
    
        @Override
        public String paymentInfo_TimeOut(Integer id) {
            return "-----PaymentFallbackService fall back-paymentInfo_TimeOut ,/(ㄒoㄒ)/~~";
        }
    }
    ```
    
- PaymentFallbackService类实现PaymentFeignClientService接口
- YML
  
    ```yaml
    server:
      port: 80
    
    eureka:
      client:
    #    register-with-eureka: true    #表识不向注册中心注册自己
        register-with-eureka: false    #表识不向注册中心注册自己
    #    fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka/
    
    #spring:
    #  application:
    #    name: cloud-provider-hystrix-order
    
    feign:
      hystrix:
        enabled: true #如果处理自身的容错就开启。开启方式与生产端不一样。
    ```
    
- PaymentFeignClientService接口
  
    ```java
    package com.youliao.springcloud.service;
    
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.stereotype.Component;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    
    /**
     * @Author Dali
     * @Date 2021/07/07 下午 12:45
     * @Version 1.0
     * @Description
     */
    @Component
    @FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",fallback = PaymentFallbackService.class)
    public interface PaymentHystrixService {
        @GetMapping("/payment/hystrix/ok/{id}")
        public String paymentInfo_OK(@PathVariable("id") Integer id);
    
        @GetMapping("/payment/hystrix/timeout/{id}")
        public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
    }
    ```
    
- 测试：
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901344.png)
    
    - 单个eureka先启动7001
    - PaymentHystrixMain8001启动
    - 正常访问测试：[http://localhost/consumer/payment/hystrix/ok/10](http://localhost/consumer/payment/hystrix/ok/10)
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901346.png)
        
    - 故意关闭微服务8001
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901347.png)
        
    - 客户端自己调用提升
      
        此时服务端provider已经down了，但是我们做了服务降级处理，让客户端在服务端不可用时也会获得提示信息而不会挂起耗死服务器
        

# 4、服务熔断：也是服务降级的一个特例

## 断路器：一句话就是家里保险丝

## 熔断是什么：

**熔断机制概述**
熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时,会进行服务的降级，进而熔断该节点微服务的调用,快速返回错误的响应信息。
**当检测到该节点微服务调用响应正常后,恢复调用链路。**

在Spring Cloud框架里,熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败,就会启动熔断机制。熔断机制的注解是@HystrixC

来源，微服务提出者马丁福勒：[https://martinfowler.com/bliki/CircuitBreaker.html](https://martinfowler.com/bliki/CircuitBreaker.html)ommand.

Martin Fowler大神的论文：[https://martinfowler.com/bliki/CircuitBreaker.html](https://martinfowler.com/bliki/CircuitBreaker.html)

1、调用失败会触发降级，而降级会调用fallback方法。
2、但无论如何降级的流程一 定会先调用正常方法再调用falIback方法。
3、假如单位时间内调用失败次数过多， 也就是降级次数过多，则触发熔断
4、熔断以后就会跳过正常方法直接调用fallback方法
5、所谓“熔断后服务不可用”就是因为跳过了正常方法直接执行fallback。

## 服务熔断实操案例：


</aside>

- 1、修改cloud-provider-hystrix-payment8001
- 2、PaymentService
  
    ```java
    //===================服务熔断====================
        @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
                @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),  //是否开启断路器
                @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),   //请求次数
                @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),  //时间范围
                @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"), //失败率达到(60%)多少后跳闸
        })
        public String paymentCircuitBreaker(@PathVariable("id") Integer id){
            if (id < 0){
                throw new RuntimeException("*****id 不能负数");
            }
            String serialNumber = IdUtil.simpleUUID();  //等价于  UUID.randomUUID().toString()
    
            return Thread.currentThread().getName()+"\t"+"调用成功,流水号："+serialNumber;
        }
        public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id){
            return "id 不能负数，请稍候再试,(┬＿┬)/~~     id: " +id;
        }
    ```
    
    why配置这些参数？
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901348.png)
    
- 3、PaymentController
  
    ```java
    //=========================服务熔断=================================
        @GetMapping("/payment/circuit/{id}")
        public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
            String result = paymentService.paymentCircuitBreaker(id);
            log.info("*******result:" + result);
            return result;
        }
    ```
    
- 4、测试
    - 启动以下服务
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2030.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901349.png)
        
    - 自测cloud-provider-hystrix-payment8001
    - 正确：[http://localhost:8001/payment/circuit/10](http://localhost:8001/payment/circuit/10)
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901350.png)
        
    - 错误：[http://localhost:8001/payment/circuit/-10](http://localhost:8001/payment/circuit/-10)
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901351.png)
        
    - 点击一次正确，然后狂点错误的，使得错误率达到60%再次点击正确的。
      
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/_2021_07_08_17_15_44_687.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901352.gif)
        
    - 重点测试：多次错误,然后慢慢正确，发现刚开始不满足条件，就算是正确的访问地址也不能进行访问，需要慢慢的恢复链路
- hutool工具：[https://www.hutool.cn/](https://www.hutool.cn/)     hu'tool参考文档：[https://www.hutool.cn/docs/#/](https://www.hutool.cn/docs/#/)

## 熔断类型：

### 熔断打开：

请求不再进行调用当前服务，内部设置时钟一般为MTTR(平均故障处理时间)，当打开时长达到所设时钟则进入熔断状态。

### 熔断关闭：

熔断关闭不会对服务进行熔断。

### 熔断半开：

部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断。

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901353.png)

如果失败次数太多，直接跳闸，即便你输入正确的也不行，他需要到一定程度，正确率高了，错误了降下来了，慢慢的半开状态，过去一部分，如果最终成功了，最终达到合并——Closed。

## 官网断路器流程图：

### 官网步骤：

![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901354.png)

### 断路器在什么情况下开始起作用：

- 断路器在什么情况下开始起作用：
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901355.png)
    

### 断路器开启或者关闭的条件：

1. 当满足一定阀值的时候（默认10秒内超过20个请求次数）
2. 当失败率达到一定的时候（默认10秒内超过50%请求失败）
3. 到达以上阀值，断路器将会开启
4. 当开启的时候，所有请求都不会进行转发
5. 一段时间之后（默认是5秒），这个时候断路器是半开状态，会让其中一个请求进行转发。如果成功，断路器会关闭，若失败，继续开启。重复4和5

### 断路器打开之后:

- 断路器打开之后:
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2036.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901356.png)
    

### 关于断路器的所有配置信息：

- 服务熔断
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901357.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2038.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901358.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2039.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901359.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2040.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901361.png)
    
- 官网断路器流程图
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2041.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901362.png)
    

# 5、hystrix工作流程

官网地址：[https://github.com/Netflix/Hystrix/wiki/How-it-Works](https://github.com/Netflix/Hystrix/wiki/How-it-Works)

- hystrix官网流程图：
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2042.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901363.png)
    
    **蓝色：**调用路径
    
    **红色：**返回路径
    
    完整的请求路线：
    
    1. 选择一个Hystrix注册方式
    2. 二选一即可
    3. 判断缓存中是否包含需要返回的内容（如果有直接返回）
    4. 断路器是否为打开状态（如果是，直接跳转到8，返回）
    5. 断路器为健康状态，判断是否有可用资源（没有，直接跳转8）
    6. 构造方法和Run方法
    7. 将正常，超时，异常的消息发送给断路器
    8. 调用getFallback方法，也就是服务降级
    9. 直接返回正确结果
- hystrix工作流程的步骤说明
  
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2043.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901364.png)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2044.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901365.png)
    

# 6、服务监控hystrixDashboard

## 1、概述

除了隔离依赖服务的调用以外，Hystrix还提供了**准实时的调用监控(Hystrix Dashboard)** , Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息，以统计报表和图形的形式展示给用户， 包括每秒执行多少请求多少成功,多少失败等。Netflix通过hystrix-metrics-event-stream项目实现了对以上指标的监控。Spring Cloud也提供了Hystrix Dashboard的整合，对监控内容转化成可视化界面。

## 2、服务监控hystrixDashboard仪表盘9001

- 1、新建cloud-consumer-hystrix-dashboard9001
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
    
        <artifactId>cloud-consumer-hystrix-dashboard9001</artifactId>
    
        <dependencies>
            <!--新增hystrix dashboard-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
    
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
    
- 3、YML：application.yml
  
    ```yaml
    server:
      port: 9001
    ```
    
- 4、HystrixDashboardMain9001+新注解@EnableHystrixDashboard
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;
    
    /**
     * @Author Dali
     * @Date 2021/07/08 下午 5:59
     * @Version 1.0
     * @Description 服务监控hystrixDashboard:仪表盘9001
     */
    @SpringBootApplication
    @EnableHystrixDashboard
    public class HystrixDashboardMain9001 {
        public static void main(String[] args) {
            SpringApplication.run(HystrixDashboardMain9001.class,args);
        }
    }
    ```
    
- 5、所有Provider微服务提供类（8001/8002/8003）都需要监控依赖配置
  
    ```xml
    <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ```
    
- 6、启动cloud-consumer-hystrix-dashboard9001该微服务后续将监控微服务8001：[http://localhost:9001/hystrix](http://localhost:9001/hystrix)
  
    输入以下地址，进入Hystrix的图形化界面：[http://localhost:9001/hystrix](http://localhost:9001/hystrix)
    
    ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2045.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901366.png)
    

## 3、断路器演示(服务监控hystrixDashboard)：

### cloud-consumer-hystrix-dashboard9001来监控我们的cloud-provider-hystrix-payment8001

- 1、修改cloud-provider-hystrix-payment8001
  
    注意：新版本Hystrix需要在主启动类PaymentHystrixMain8001中指定监控路径
    
    ```java
    /**
         * 此配置是为了服务监控而配置，与服务容错本身无观，springCloud 升级之后的坑
         * ServletRegistrationBean因为springboot的默认路径不是/hystrix.stream
         * 只要在自己的项目中配置上下面的servlet即可
         * @return
         */
        @Bean
        public ServletRegistrationBean getServlet(){
            HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
            ServletRegistrationBean<HystrixMetricsStreamServlet> registrationBean = new ServletRegistrationBean<>(streamServlet);
            registrationBean.setLoadOnStartup(1);
            registrationBean.addUrlMappings("/hystrix.stream");
            registrationBean.setName("HystrixMetricsStreamServlet");
            return registrationBean;
        }
    ```
    
- 2、同时，最后我们需要注意，每个服务类想要被监控的，都需要在pom文件中，添加一下注解
  
    ```xml
    <!--web和监控-->
    <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ```
    
- 3、监控测试：
    - 启动1个eureka或者3个eureka集群均可：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
      
        为了方便，这里我们先启动一个eureka服务
        
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2046.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901368.png)
        
        ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2047.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901369.png)
        
    - 观察监控窗口
        - 9001监控8001：监控地址：[http://localhost:8001/hystrix.stream](http://localhost:8001/hystrix.stream)
          
            ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2048.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901370.png)
            
        - 测试地址：
          
            **正确地址**：[http://localhost:8001/payment/circuit/10](http://localhost:8001/payment/circuit/10)
            
            ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2049.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901371.png)
            
            **错误地址**：[http://localhost:8001/payment/circuit/-10](http://localhost:8001/payment/circuit/-10)
            
            ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2050.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901372.png)
            
            先访问正确地址，再访问错误地址，再正确地址，会发现图示断路器都是慢慢放开的
            
            - 监控结果，成功
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2051.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901373.png)
                
            - 监控结果，失败
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2052.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901374.png)
                
            - 监控动图：gif
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/_2021_07_08_18_38_26_266.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901375.gif)
                
            
            如何看懂图：首先是七种颜色
            
            ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2053.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901377.png)
            
            然后是里面的圆
            
            实心圆：共有两种含义。它通过颜色的变化代表了实例的健康程度，它的健康程度从
            
            绿色 < 黄色 < 橙色 <红色，递减
            
            该实心圆除了颜色变化之外，它的大小也会根据实例的请求流量发生变化，流量越大该实心圆就越大，所以通过该实心圆的展示，就可以快速在大量的实例中快速发现故障实例和高压力实例。
            
            曲线：用于记录2分钟内流量的相对变化，可以通过它来观察到流量的上升和下降趋势。
            
            ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2054.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901378.png)
            
            - 7色
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2055.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901379.png)
                
            - 1圈
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2056.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901380.png)
                
            - 1线
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2057.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901381.png)
                
            - 整图说明
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2058.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901382.png)
                
            - 整图说明2
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2059.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901383.png)
                
            - 搞懂一个才能看懂复杂的
              
                ![09_Hystrix%E6%96%AD%E8%B7%AF%E5%99%A8%2093a22a519f584440964a04f9b4d3ccb6/Untitled%2060.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071901384.png)