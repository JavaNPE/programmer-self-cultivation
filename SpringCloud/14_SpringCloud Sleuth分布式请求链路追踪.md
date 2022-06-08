# 14_SpringCloud Sleuth分布式请求链路追踪

# 一、SpringCloud Sleuth概述

## 为什么会出现这个技术？需要解决哪些问题？

在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的服务节点调用来协同产生最后的请求结果，每一个前端请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引起整个请求最后的失败。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946861.png)

极端情况下链路之间的调用要是这样的，怎么办？

![14_SpringCloud%20Sleuth%E5%88%86%E5%B8%83%E5%BC%8F%E8%AF%B7%E6%B1%82%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA%2089a3510099744263bed08e17b775bc95/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946862.png)

## SpringCloud Sleuth是什么?

### 官网地址：

[GitHub - spring-cloud/spring-cloud-sleuth: Distributed tracing for spring cloud](https://github.com/spring-cloud/spring-cloud-sleuth)

Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且兼容支持了zipkin。

解决：

![14_SpringCloud%20Sleuth%E5%88%86%E5%B8%83%E5%BC%8F%E8%AF%B7%E6%B1%82%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA%2089a3510099744263bed08e17b775bc95/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946863.png)

# 二、搭建链路监控步骤

## 1.zipkin

SpringCloud从F版起已不需要自己构建Zipkin server了，只需要调用jar包即可。

下载：[https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/](https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/)（课件地址：已失效）

**新的zipkin下载地址**：[https://repo1.maven.org/maven2/io/zipkin/zipkin-server/](https://repo1.maven.org/maven2/io/zipkin/zipkin-server/)

*zipkin-server-2.12.9.exec.jar的下载地址：*

[https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec](https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec)

### **运行jar：**

找到zipkin-server-2.12.9.exec.jar在硬盘中的存放位置，然后在cmd中执行以下命令：`java -jar zipkin-server-2.12.9-exec.jar`

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946864.png)

### 运行控制台

地址：[http://localhost:9411/zipkin/](http://localhost:9411/zipkin/)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946865.png)

### 术语：Trace、Span名称解释

- Trace：类似于树结构的Span集合，表示一条调用链路，存在唯一标识
- Span：表示调用链路来源，通俗的理解span就是一次请求信息

### 完整的调用链路：

表示一请求链路， 一条链路通过Trace ID唯一标识，Span标识发起请求信息，各span通过parent id关联起来。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946866.png)

一条链路通过Trace Id唯一标识，Span表示发起的请求信息，各span通过parent id关联起来。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946867.png)

整个链路的依赖关系如下：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946868.png)

## 2.服务提供者

- 1、修改：cloud-provider-payment8001
- 2、POM
  
    ```xml
    <!--链路监控包含sleuth+zipkin-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
    ```
    
- 3、YML
  
    ```yaml
    server:
      port: 8001
    spring:
      application:
        name: cloud-payment-service
      **zipkin:
        base-url: http://localhost:9411
      sleuth:
        sampler:
        # 采集率介于0到1之间，1表示全部采集
          probability: 1
    .........
    ..............**
    ```
    
- 4、业务类PaymentController
  
    ```java
    @GetMapping("/payment/zipkin")
    public String paymentZipkin() {
      return "hi ,i'am paymentzipkin server fall back，welcome to atguigu，O(∩_∩)O哈哈~";
    }
    ```
    

## 3.服务消费者（调用方）

- 1、修改cloud-consumer-order80
- 2、POM
  
    ```xml
    <!--链路监控包含sleuth+zipkin-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
    ```
    
- 3、YML
  
    ```yaml
    server:
      port: 80
    
    spring:
      application:
        name: cloud-order-service
      **zipkin:
        base-url: http://localhost:9411
      sleuth:
        sampler:
          # 采集率介于0到1之间，1表示全部采集
          probability: 1
    .......
    ...................**
    ```
    
- 4、业务类OrderController
  
    ```java
    // ====================> zipkin+sleuth
        @GetMapping("/consumer/payment/zipkin")
        public String paymentZipkin() {
            String result = restTemplate.getForObject("http://localhost:8001" + "/payment/zipkin/", String.class);
            return result;
        }
    ```
    
- 

## 4.依次启动eureka7001/PaymentMain8001/OrderMain80

80调用8001几次测试下，

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946869.png)

## 5.打开浏览器访问:http:localhost:9411

浏览器访问：[http://localhost/consumer/payment/zipkin](http://localhost/consumer/payment/zipkin)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946870.png)

在访问地址：[http://localhost:9411](http://localhost:9411/zipkin/)

会出现以下界面：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946871.gif)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946872.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946873.png)

查看：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946874.png)

查看依赖关系：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946875.png)

原理：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081946876.png)