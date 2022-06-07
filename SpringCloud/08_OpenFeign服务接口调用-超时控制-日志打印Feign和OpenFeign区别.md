# 08_OpenFeign服务接口调用-超时控制-日志打印Feign和OpenFeign区别

开具一张图，这节课的课程大纲如下：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618983.png)

# 一、概述

关于Feign的停更，目前已经使用OpenFeign进行替换。

## 1、OpenFeign是什么

Feign是一个声明式的web服务客户端，让编写web服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可。

GitHub：[https://github.com/spring-cloud/spring-cloud-openfeign](https://github.com/spring-cloud/spring-cloud-openfeign)

## 2、OpenFeign能干嘛

### Feign的作用：

Feign旨在使编写Java Http客户端变得更容易。

前面在使用Ribbon + RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模板化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，**往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端来包装这些依赖的调用**，所以Feign在这个基础上做了进一步封装，由他来帮助我们定义和实现依赖服务的接口定义。在Feign的实现下，**我们只需要创建一个接口，并使用注解的方式来配置它（以前是Dao接口上面标注Mapper注解，现在是微服务接口上标注一个Feign注解即可）**，即可完成服务提供方的接口绑定，简化了使用Spring Cloud Ribbon时，自动封装服务调用客户端的开发量。

### Feign集成Ribbon：

利用Ribbon维护了Payment的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是，**通过Feign只需要定义服务绑定接口且声明式的方法**，优雅而简单的实现了服务调用。

## 3、Feign和OpenFeign两者区别

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618984.png)

# 二、OpenFeign使用步骤（新建一个OpenFeign的小Demo）

- 1、接口+注解：[微服务调用接口+@FeignClient注解](https://www.notion.so/08_OpenFeign-65ffd7848ba14c3b8011e456d334da56) （图例）
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618985.png)
    
- 2、新建cloud-consumer-feign-order80：**Feign在消费端使用（Client：客户）**
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618986.png)
    
- 3、POM文件
  
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
    
        <artifactId>cloud-consumer-feign-order80</artifactId>
    
        <!--openfeign-->
        <dependencies>
            **<dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
            </dependency>**
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-common</artifactId>
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
    
- 4、YML：application.yml
  
    ```yaml
    server:
      port: 80
    eureka:
      client:
        register-with-eureka: false
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka
    ```
    
- 5、主启动：**@EnableFeignClients ：激活Feign组件**
  
    ```java
    @SpringBootApplication
    @EnableFeignClients //使用Feign 激活并开启 激活Feign组件
    public class OrderFeignMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderFeignMain80.class, args);
        }
    }
    ```
    
- 6、业务类：**@FeignClient：使用Feign组件**
    - 1、业务逻辑接口+@FeignClient配置调用provider服务
    - 2、新建PaymentFeignService接口并新增注解**@FeignClient：使用Feign组件**
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618987.png)
        
        ```java
        package com.youliao.springcloud.service;
        
        import com.youliao.springcloud.entities.CommonResult;
        import com.youliao.springcloud.entities.Payment;
        import feign.Param;
        import org.springframework.cloud.openfeign.FeignClient;
        import org.springframework.stereotype.Component;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        
        /**
         * @Author Dali
         * @Date 2021/07/05 下午 12:44
         * @Version 1.0
         * @Description
         */
        @Component
        @FeignClient(value = "CLOUD-PAYMENT-SERVICE") //value："微服务名称"
        public interface PaymentFeignService {
            @GetMapping(value = "/payment/get/{id}")
            public CommonResult getPaymentById(@PathVariable("id") Long id);
        }
        ```
        
    - 3、控制层Controller
      
        ```java
        package com.youliao.springcloud.controller;
        
        import com.youliao.springcloud.entities.CommonResult;
        import com.youliao.springcloud.entities.Payment;
        import com.youliao.springcloud.service.PaymentFeignService;
        import lombok.extern.slf4j.Slf4j;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RestController;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/07/05 下午 1:19
         * @Version 1.0
         * @Description
         */
        @RestController
        @Slf4j
        public class OrderFeignController {
            @Resource
            private PaymentFeignService paymentFeignService;
        
            @GetMapping(value = "/consumer/payment/get/{id}")
            public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
                return paymentFeignService.getPaymentById(id);
            }
        }
        ```
    
- 7、测试：[http://localhost/consumer/payment/get/](http://localhost/consumer/payment/get/31)10
  
    7.1 先启动2个eureka集群7001/7002
    
    7.2 再启动2个微服务8001/8002
    
    7.3 启动OpenFeign启动
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618988.png)
    
    测试结果：使用OpenFeign调用自己的微服务8001和8002（Eureka集群）实现负载均衡效果
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618989.gif)
    
- 8、小总结：各层级关系
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618990.png)
    

# 三、OpenFeign超时控制

- 1、超时设置，故意设置超时演示出错情况
    - 1.1 服务提供方8001故意写暂停程序
      
        ```java
        /**
             * 服务提供方8001故意写暂停程序：超时测试
             *
             * @return
             */
            @GetMapping(value = "/payment/feign/timeout")
            public String paymentFeignTimeout() {
                try {
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                return serverPort;
            }
        ```
        
    - 1.2 服务消费方80添加超时方法PaymentFeignService
      
        ```java
        @GetMapping(value = "/payment/feign/timeout")  //对应8001中的PaymentController同名方法
        public String paymentFeignTimeout();
        ```
        
    - 1.3 服务消费方80添加超时方法OrderFeignController
      
        ```java
        @GetMapping(value = "/consumer/payment/feign/timeout")
        public String paymentFeignTimeout(){
             //openfeign的底层是ribbon，客户端（也就是我们的消费者）一般默认等待1秒钟
             return paymentFeignService.paymentFeignTimeout();
        }
        ```
        
    - 1.4 测试：[http://localhost/consumer/payment/feign/timeout](http://localhost/consumer/payment/feign/timeout)
      
        启动以下四个微服务：
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618991.png)
        
        测试1：8001自测通过：[http://localhost:8001/payment/feign/timeout](http://localhost:8001/payment/feign/timeout)
        
        ![08_OpenFeign%E6%9C%8D%E5%8A%A1%E6%8E%A5%E5%8F%A3%E8%B0%83%E7%94%A8%20f333c3c8bacc4ca5837189f185a7dda1/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618992.png)
        
        测试2：[http://localhost/consumer/payment/feign/timeout](http://localhost/consumer/payment/feign/timeout)   如果出现404，看看看是不是把8002也启动了。
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618993.png)
    
- 2、OpenFeign超时控制是什么意思：
  
    默认Feign客户端只等待1秒钟， 但是服务端处理需要超过1秒钟，导致Feign客户端不想等待了，直接返回报错。为了避免这样的情况，有时候我们需要设置Feign客户端的超时控制。yml文件中开启配置
    
- 3、OpenFeign默认支持Ribbon
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618994.png)
    
- 4、YML文件里需要开启OpenFeign客户端超时控制
  
    ```yaml
    # 设置feign客户端超时时间(OpenFeign默认支持ribbon)
    ribbon:
      # 指的是建立连接所用的时间,适用于网络状态正常的情况下,两端连接所用的时间
      ReadTimeout: 5000
      # 指的是建立连接后从服务器读取到可用资源所用的时间
      ConnectTimeout: 5000
    ```
    
    对于上次报500的错误，再次测试：[http://localhost/consumer/payment/feign/timeout](http://localhost/consumer/payment/feign/timeout)
    
    发现报错消失：正常
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618995.png)
    

# 四、OpenFeign日志打印功能

- 1、OpenFeign日志打印功能是什么
  
    Feign提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解Feign中Http请求的细节。说白了就是**对Feign接口的调用情况进行监控和输出。**
    
- **2、OpenFeign的日志级别（4种）**
    - **NONE：**默认的，不显示任何日志;
    - **BASIC：** 仅记录请求方法、URL、响应状态码及执行时间;
    - **HEADERS：**除了BASIC中定义的信息之外,还有请求和响应的头信息;
    - **FULL：**除了HEADERS中定义的信息之外,还有请求和响应的正文及元数据。
- 3、配置日志bean：FULL级别日志
  
    ```java
    package com.youliao.springcloud.config;
    
    import feign.Logger;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    /**
     * @Author Dali
     * @Date 2021/07/05 下午 6:01
     * @Version 1.0
     * @Description
     */
    @Configuration
    public class FeignConfig {
        @Bean
        Logger.Level feignLoggerLevel(){
            return Logger.Level.FULL;
        }
    }
    ```
    
- 4、YML文件里需要开启日志的Feign客户端
  
    ```yaml
    logging:
      level:
        # feign日志以什么级别监控哪个接口
        com.youliao.springcloud.service.PaymentFeignService: debug
    ```
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618996.gif)
    
- 5、后台日志查看：[http://localhost/consumer/payment/get/10](http://localhost/consumer/payment/get/10)
  
    启动以下服务：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618997.png)
    
    测试结果：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071618998.png)

