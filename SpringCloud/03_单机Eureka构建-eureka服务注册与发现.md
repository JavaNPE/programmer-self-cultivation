# 03_单机Eureka构建-eureka服务注册与发现

# 一、IDEA生成eurekaServer端服务注册中心类似物业公司

## 工程架构图：

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157682.png)

 

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157684.png)

## 1、建Module——cloud-eureka-server7001

## 2、改POM:

注意1.X和2.X的对比说明：

```xml
    以前的老版本(当前使用2018)
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    现在新版本(当前使用2020.2)
    <dependency>
        <groupId>org.springframework.cloud</ groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
```

```xml
<dependencies>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-se
       rver-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>com.youliao.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--
       https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--
       https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--
       https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
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
        </dependency>
        <!--https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
```

## 3、写YML

```yaml
server:
  port: 7001
  
eureka:
  instance:
    hostname: localhost  #eureka服务端的实例名字
  client:
    register-with-eureka: false    #表示不向注册中心注册自己
    fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

## 4、主启动

```java
@SpringBootApplication
[**@EnableEurekaServer**](https://www.notion.so/01_Eureka-c1b35e2878a841aa8241ca6639510d2e)
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
```

## 5、测试单机版Eureka：

[](http://localhost:7001/)

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157685.png)

# 二、支付微服务8001入驻进eurekaServer

EurekaClient端cloud-provider-payment8001将注册进EurekaServer成为服务提供者provider,类似尚硅谷学校对外提供授课服务。

## 1、改pom

在cloud-provider-payment8001中的pom文件中添加eureka-client依赖。

```xml
<dependency> <!--Eureka Client-->
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

## 2、改yml

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为rue。
    register-with-eureka: true
    #是否从Eurekaserver抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka  #类比物业公司的地址
```

图例：

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157686.png)

## [3、主启动中PaymentMain8001添加@EnableEurekaClient注解](https://www.notion.so/01_Eureka-c1b35e2878a841aa8241ca6639510d2e)

```java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```

## 4、测试：8001 服务提供者

### 4.1先启动EurekaService:7001

### 4.2在启动EurekaClient:8001

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157687.png)

测试地址：

[http://localhost:7001/](http://localhost:7001/)

结果页：

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157688.png)

### 微服务注册名配置说明

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157689.png)

### Eureka的自我保护机制：

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157690.png)

# 二、订单微服务80入驻进eurekaServer

EurekaClient端cloud-consumer-order80将注册进EurekaServer成为服务消费者consumer,类似来尚硅谷上课消费的各位同学。

## 1、改POM

在

在cloud-consumer-order80模块的pom文件中添加eureka-client依赖。

```xml
<dependency> <!--Eureka Client-->
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

## 2、改YAM

```yaml
spring:
  application:
    name: cloud-order-service

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为rue。
    register-with-eureka: true
    #是否从Eurekaserver抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka  #类比物业公司的地址
```

## [3、主启动OrderMain80添加@EnableEurekaClient注解](https://www.notion.so/01_Eureka-c1b35e2878a841aa8241ca6639510d2e)

```java
@SpringBootApplication
@EnableEurekaClient
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class, args);
    }
}
```

### 测试：

先启动EurekaService 7001服务

在启动服务提供者provider,8001服务

eureka服务器

测试地址：[http://localhost:7001/](http://localhost:7001/)

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157691.png)

此时我们随机测试80消费者：

[http://localhost/consumer/payment/get/7](http://localhost/consumer/payment/get/7)

![03_%E5%8D%95%E6%9C%BAEureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%2001c4d2b62758438d89bdb44d2f075a02/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022157692.png)