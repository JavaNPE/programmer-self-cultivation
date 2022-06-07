# 10_Gateway新一代网关

# 一、Gateway概述简介

zuul目前已经出现了分歧，zuul 升级到 Zuul2的时候出现了内部分歧，并且导致Zuul的核心人员的离职，导致Zuul2一直跳票，等了两年，目前造成的局面是Zuul已经没人维护，Zuul2一直在开发中。

目前主流的服务网关采用的是Spring Cloud 社区推出了 Gateway。

## zuul 1.X和gateway官网：

上一代zuul 1.X：[https://github.com/Netflix/zuul/wiki](https://github.com/Netflix/zuul/wiki)

当前gateway：[https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/)

## Gateway是什么：

Cloud全家桶中有个很重要的组件就是网关，在1 .x版本中都是采用的Zuul网关;
但在2.x版本中，zuul的升级一直跳票， SpringCloud最后自己研发了一个网关替代Zuul,
**那就是SpringCloud Gateway一句话: gateway是原zuul1.x版的替代**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910315.png)

**Gateway概述：**

Gateway是在Spring生态系统之上构建的API网关服务,基于Spring 5, Spring Boot 2和Project Reactor等技术。
Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能， 例如: 熔断、限流、重试等。

![SpringCloudGateway](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910316.png)

SpringCloud Gateway是Spring Cloud的一个全新项目,基于Spring 5.0+Spring Boot 2.0和Project Reactor等技术开发的网关,它旨在为微服务架构提供一种简单有效的统一的API路由管理方式。

SpringCloud Gateway作为Spring Cloud生态系统中的网关，目标是替代Zuul,在Spring Cloud 2.0以上版本中，没有对新版本的Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty。

Spring Cloud Gateway的目标提供统-的路由方式且基于Filter 链的方式提供了网关基本的功能,例如:安全,监控/指标,和限流。

**源码架构：**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910318.png)

## gateway能干嘛：反向代理/鉴权/流量控制/熔断/日志监控

- 反向代理
- 鉴权
- 流量控制
- 熔断
- 日志监控

## 微服务架构中网关在哪里

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910319.png)

## 有了Zuul了怎么又出来了gateway?

### 我们为什么选择Gatway?

**1.neflix不太靠谱，zuul2.0一直跳票,迟迟不发布**

一方面因为Zuul1.0已经进入 了维护阶段,而且Gateway是SpringCloud团队研发的, 亲儿子产品，值得信赖。而且很多功能ZuuI都没有用起来也非常的简单便捷。
Gateway是基于异步非阻塞模型上进行开发的,性能方面不需要担心。虽然Netflix早就发布了最新的Zuul 2.x,但Spring Cloud貌似没有整合计划。而且Netflix相关组件都宣布进入维护期;不知前景如何?
多方面综合考虑Gateway是很理想的网关选择。

**2.SpringCloud Gateway具有如下特性：**
**基于Spring Framework 5, Project Relactor和Spring Boot 2.0进行构建;**
**动态路由：**能够匹配任何请求属性;
可以对路由指定Predicate (断言)和Filter (过滤器) ;
集成Hystrix的断路器功能;
集成Spring Cloud服务发现功能;
易于编写的Predicate (断言)和Filter (过滤器) ;
请求限流功能;
支持路径重写。

**3.SpringCloud Gateway与Zuul的区别：**

在SpringCloud Finchley 正式版之前，Spring Cloud推荐的网关是Netlix提供的Zuul:
1、Zuul1.x, 是-个基于阻塞|/ 0的API Gateway

2、Zuul 1.x**基于Servlet 2. 5**使用阻塞架构它不支持任何长连接(如WebSocket) Zuul的设计模式和Nginx较像,每次I/ 0操作都是从工作线程中选择一个执行， 请求线程被阻塞到工作线程完成，但是差别是Nginx用C++实现，Zuul 用Java实现，而JVM本身会有第一次加载较慢的情况，使得Zuul的性能相对较差。

3、Zuul 2.x理念更先进,想基于Netty非阻塞和支持长连接,但SpringCloud目前还没有整合。 Zuul 2.x的性能较Zuul 1.x有较大提升,在性能方面,根据官方提供的基准测试， Spring Cloud Gateway的RPS (每秒请求数) Zuul的1. 6倍。

4、Spring Cloud Gateway建立在Spring Framework 5、Project Reactor和Spring Boot2之上，使朝非阻塞API。

5、Spring Cloud Gateway还支持WebSocket，组与Spring紧密集成拥有 更好的开发体验。

### Zuul1.x模型：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910320.png)

上述模式的缺点：

servlet是一个简单的网络IO模型，当请求进入Servlet container时，servlet container就会为其绑定一个线程，在并发不高的场景下，这种网络模型是适用的，但是一旦高并发（Jmeter测试），线程数就会上涨，而线程资源代价是昂贵的（上下文切换，内存消耗大），严重影响了请求的处理时间。在一些简单业务场景下，不希望为每个Request分配一个线程，只需要1个或几个线程就能应对极大并发的请求，这种业务场景下Servlet模型没有优势。

所以Zuul 1.X是基于Servlet之上的一种阻塞式锤模型，即Spring实现了处理所有request请求的Servlet（DispatcherServlet）并由该Servlet阻塞式处理，因此Zuul 1.X无法摆脱Servlet模型的弊端。

### GateWay模型：

**WebFlux是什么？**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910321.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910322.png)

### **WebFlux框架**

传统的Web框架，比如Struts2，Spring MVC等都是基于Servlet API 与Servlet容器基础之上运行的，但是在Servlet 3.1之后有了异步非阻塞的支持，而WebFlux是一个典型的非阻塞异步的框架，它的核心是基于Reactor的相关API实现的，相对于传统的Web框架来说，它可以运行在如 Netty，Undertow 及支持Servlet3.1的容器上。非阻塞式 + 函数式编程（Spring5必须让你使用Java8）

Spring WebFlux是Spring 5.0引入的新的响应式框架，区别与Spring MVC，他不依赖Servlet API，它是完全异步非阻塞的，并且基于Reactor来实现响应式流规范。

# 二、Gateway三大核心概念

### **Route 路由**

路由就是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为True则匹配该路由

### **Predicate 断言**

参考的Java8的 `java.util.function.Predicate`

开发人员可以匹配HTTP请求中的所有内容，例如请求头和请求参数，如果请求与断言想匹配则进行路由

### **Filter 过滤**

指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

## 总结：

Web请求通过一些匹配条件，定位到真正的服务节点，并在这个转发过程的前后，进行了一些精细化的控制。

Predicate就是我们的匹配条件，而Filter就可以理解为一个无所不能的拦截器，有了这两个元素，在加上目标URL，就可以实现一个具体的路由了。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910323.png)

# 三、Gateway工作流程

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910324.png)

客户端向Spring Cloud Gateway发出请求，然后在Gateway Handler Mapping中找到与请求相匹配的路由，将其发送到Gateway Web Handler。

Handler在通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。

过滤器之间用虚线分开是因为过滤器可能会在发送代理请求前（pre）或之后（post）执行业务逻辑。

Filter在 Pre 类型的过滤器可以做参数校验，权限校验，流量监控，日志输出，协议转换等。

在 Post类型的过滤器中可以做响应内容，响应头的修改，日志的输出，流量监控等有着非常重要的作用。

**Gateway的核心逻辑：路由转发 + 执行过滤链**

# 四、Gateway入门配置：Gateway9527Module搭建

- 1、新建Module：cloud-gateway-gateway9527
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
    
        <artifactId>cloud-gateway-gateway9527</artifactId>
    
        <dependencies>
            <!--新增gateway-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-gateway</artifactId>
            </dependency>
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>1.0-SNAPSHOT</version>
            </dependency>
    
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
      port: 9527
    spring:
      application:
        name: cloud-gateway
      cloud:
        gateway:
          routes:
            - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
              uri: http://localhost:8001   #匹配后提供服务的路由地址
              predicates:
                - Path=/payment/get/**   #断言,路径相匹配的进行路由
    
            - id: payment_routh2
              uri: http://localhost:8001
              predicates:
                - Path=/payment/lb/**   #断言,路径相匹配的进行路由
    
    eureka:
      instance:
        hostname: cloud-gateway-service
      client:
        service-url:
          register-with-eureka: true
          fetch-registry: true
          defaultZone: http://eureka7001.com:7001/eureka
    ```
    
- 4、业务类：无
- 5、主启动类
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/11 下午 2:56
     * @Version 1.0
     * @Description
     */
    
    @SpringBootApplication
    @EnableEurekaClient
    public class GateWayMain9527 {
        public static void main(String[] args) {
            SpringApplication.run(GateWayMain9527.class, args);
        }
    }
    ```
    
- 6、9527网关如何做路由映射那？？？
    - cloud-provider-payment8001看看controller的访问地址
      
        get
        
        lb
        
    - 我们目前不想暴露8001端口，希望在8001外面套一层9527
- 7、YML新增网关配置
  
    ```yaml
    server:
      port: 9527
    spring:
      application:
        name: cloud-gateway
      cloud:
        gateway:
          routes:
        - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001   #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**   #断言,路径相匹配的进行路由
    
        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/lb/**   #断言,路径相匹配的进行路由
    
    eureka:
      instance:
        hostname: cloud-gateway-service
      client:
        service-url:
          register-with-eureka: true
          fetch-registry: true
          defaultZone: http://eureka7001.com:7001/eureka
    ```
    
- 8、测试
    - 启动：启动7001、cloud-provider-payment8001、启动9527网关
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910325.png)
        
    - eureka地址：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910326.png)
        
    - 访问说明：
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910327.png)
        
        - 添加网关前：[http://localhost:8001/payment/get/10](http://localhost:8001/payment/get/10)
          
            ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910328.png)
            
        - 添加网关后：[http://localhost:9527/payment/get/10](http://localhost:9527/payment/get/10)
          
            ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910329.png)
            
        - 添加网关后：[http://localhost:9527/payment/lb](http://localhost:9527/payment/lb)
          
            ![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910330.png)
    
- 9、Gateway网关路由有两种配置方式：
    - 9.1 在配置文件yml中配置：见前面步骤
    - 9.2 代码中注入RouteLocator的Bean
      
        官网案例：
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910331.png)
        
        百度国内新闻网址，需要外网：[http://news.baidu.com/guonei](http://news.baidu.com/guonei)
        
        业务需求：通过9527网关访问到外网的百度新闻网址
        
        cloud-gateway-gateway9527实现业务config
        
        测试地址：[http://localhost:9527/guonei](http://localhost:9527/guonei)
        
        ```java
        package com.youliao.springcloud.config;
        
        import org.springframework.cloud.gateway.route.RouteLocator;
        import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        
        /**
         * @Author Dali
         * @Date 2021/07/11 下午 3:25
         * @Version 1.0
         * @Description
         */
        @Configuration
        public class GateWayConfig {
            // 配置了一个id为route-name的路由规则，当访问地址 http://localhost:9527/guonei时，会自动转发到
            // 地址 http;//news.baidu.com/guonei
            @Bean
            public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder) {
                RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
                routes.route("path route atguigu",
                        r -> r.path("/guonei").uri("https://www.baidu.com")).build();
                return routes.build();
            }
        }
        
        ```
        
        测试结果：[http://localhost:9527/guonei](http://localhost:9527/guonei)
        
        ![[http://localhost:9527/guonei](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910332.png)](10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2016.png)
        
        [http://localhost:9527/guonei](http://localhost:9527/guonei)
        
        其他案例：
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910333.png)
        

# 五、通过微服务名实现动态路由

默认情况下Gateway会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能。

首先需要开启从注册中心动态创建路由的功能，利用微服务名进行路由

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910334.png)

- 启动：一个eureka7001+两个服务提供者8001/8002
- POM：
  
    ```xml
    <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
    ```
    
- YML：
  
    ```yaml
    server:
      port: 9527
    spring:
      application:
        name: cloud-gateway
      cloud:
        gateway:
          discovery:
            locator:
              enabled: true  #开启从注册中心动态创建路由的功能，利用微服务名进行路由
          routes:
            - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
              #uri: http://localhost:8001   #匹配后提供服务的路由地址
              uri: lb://cloud-payment-service
              predicates:
                - Path=/payment/get/**   #断言,路径相匹配的进行路由
    
            - id: payment_routh2
              #uri: http://localhost:8001   #匹配后提供服务的路由地址
              uri: lb://cloud-payment-service
              predicates:
                - Path=/payment/lb/**   #断言,路径相匹配的进行路由
    
    eureka:
      instance:
        hostname: cloud-gateway-service
      client:
        service-url:
          register-with-eureka: true
          fetch-registry: true
          defaultZone: http://eureka7001.com:7001/eureka
    ```
    
- 测试：[http://localhost:9527/payment/lb](http://localhost:9527/payment/lb)
  
    启动:
    
    ![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910335.png)
    
    8001/8002两个端口切换：测试结果 8001和8002依次轮流出现
    
    ![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910336.png)
    

# 六、Predicate（断言）的使用

## Predicate是什么：

启动我们的gatewat9527

![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910337.png)

## Route Predicate Factories这个是什么东东？

![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910338.png)

![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910339.png)

## 常用的Route Predicate：

![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910340.png)

![10_Gateway%E6%96%B0%E4%B8%80%E4%BB%A3%E7%BD%91%E5%85%B3%201e16358ed3fb482e8eab1c929c264324/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910341.png)

- **所有的Route Predicate**
  
    ```yaml
    server:
      port: 9527
    spring:
      application:
        name: cloud-gateway
      cloud:
        gateway:
          discovery:
            locator:
              enabled: true  #开启从注册中心动态创建路由的功能，利用微服务名进行路由
          routes:
            - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
              #uri: http://localhost:8001   #匹配后提供服务的路由地址
              uri: lb://cloud-payment-service
              predicates:
                - Path=/payment/get/**   #断言,路径相匹配的进行路由
     
            - id: payment_routh2
              #uri: http://localhost:8001   #匹配后提供服务的路由地址
              uri: lb://cloud-payment-service
              predicates:
                - Path=/payment/lb/**   #断言,路径相匹配的进行路由
                #- After=2020-03-08T10:59:34.102+08:00[Asia/Shanghai]
                #- Cookie=username,zhangshuai #并且Cookie是username=zhangshuai才能访问
                #- Header=X-Request-Id, \d+ #请求头中要有X-Request-Id属性并且值为整数的正则表达式
                #- Host=**.atguigu.com
                #- Method=GET
                #- Query=username, \d+ #要有参数名称并且是正整数才能路由
     
     
    eureka:
      instance:
        hostname: cloud-gateway-service
      client:
        service-url:
          register-with-eureka: true
          fetch-registry: true
          defaultZone: http://eureka7001.com:7001/eureka
    ```
    

总结：说白了，Predicate就是为了实现一组匹配规则，让请求过来找到对应的Route进行处理。

- After Route Predicate：在什么时间之后执行
  
    ![https://gitee.com/moxi159753/LearningNotes/raw/master/SpringCloud/SpringCloud2020/8_%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3Gateway/images/image-20200409161713254.png](https://gitee.com/moxi159753/LearningNotes/raw/master/SpringCloud/SpringCloud2020/8_%E6%9C%8D%E5%8A%A1%E7%BD%91%E5%85%B3Gateway/images/image-20200409161713254.png)
    
- Before Route Predicate：在什么时间之前执行
- Between Route Predicate：在什么时间之间执行
- Cookie Route Predicate：Cookie级别
  
    常用的测试工具：
    
    - jmeter
    - postman
    - curl
    
    `// curl命令进行测试，携带Cookie
    curl http://localhost:9527/payment/lb --cookie "username=zzyy"`
    
- Header Route Predicate：携带请求头
- Host Route Predicate：什么样的URL路径过来
- Method Route Predicate：什么方法请求的，Post，Get
- Path Route Predicate：请求什么路径 `Path=/api-web/**`
- Query Route Predicate：带有什么参数的

# 七、Filter的使用

### **概念**

路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应，路由过滤器只能指定路由进行使用

Spring Cloud Gateway内置了多种路由过滤器，他们都由GatewayFilter的工厂类来产生的。

### **Spring Cloud Gateway Filter**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910342.png)

生命周期：only Two：pre，Post

种类：Only Two

- GatewayFilter
- GlobalFilter

### **自定义过滤器**

主要作用：

- 全局日志记录
- 统一网关鉴权

需要实现接口：`implements GlobalFilter, Ordered`

全局过滤器代码如下：

```java
package com.youliao.springcloud.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

/**
 * @Author Dali
 * @Date 2021/07/12 下午 12:31
 * @Version 1.0
 * @Description
 */
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("come in global filter: {}", new Date());

        ServerHttpRequest request = exchange.getRequest();
        String uname = request.getQueryParams().getFirst("uname");
        if (uname == null) {
            log.info("用户名为null，非法用户");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        // 放行
        return chain.filter(exchange);
    }

    /**
     * 过滤器加载的顺序 越小,优先级别越高
     *
     * @return
     */
    @Override
    public int getOrder() {
        return 0;
    }
}
```

启动：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071910343.png)

测试：正确：[http://localhost:9527/payment/lb?uname=z3](http://localhost:9527/payment/lb?uname=z3)