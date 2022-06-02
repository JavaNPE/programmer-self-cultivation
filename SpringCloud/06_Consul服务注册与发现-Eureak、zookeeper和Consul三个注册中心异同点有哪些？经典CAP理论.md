# 06_Consul服务注册与发现-Eureak、zookeeper和Consul三个注册中心异同点有哪些？经典CAP理论

# 一、Consul简介

**1、Consul是什么**：[Consul是一开源的分布式服务发现和配置管理系统，由HashiCorp公司用Go语言开发。](https://www.consul.io/intro/index.html)

**2、Consul能干嘛**：

1. 服务发现：提供HTTP和DNS两种发现方式
2. 健康监测：支持多种协议，HTTP、 TCP、 Docker、 ShelI脚本定制化
3. KV存储：key , Value的存储方式
4. 多数据中心：Consul支持多数据中心
5. 可视化Web界面

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221576.png)

**3、去哪下：**[https://www.consul.io/downloads](https://www.consul.io/downloads)

**4、怎么玩（consul中文教程）：**[https://www.springcloud.cc/spring-cloud-consul.html](https://www.springcloud.cc/spring-cloud-consul.html)

# 二、安装并运行Consul

**官网安装说明：**[https://learn.hashicorp.com/tutorials/consul/get-started-install](https://learn.hashicorp.com/tutorials/consul/get-started-install)

consul下载地址：[https://www.consul.io/downloads](https://www.consul.io/downloads)

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221578.png)

下载完成后只有一个consul.exe文件， 硬盘路径下双击运行，查看版本信息。

consul.exe本地安装位置：‪D:\develop\Utils\consul_1.10.0_windows_amd64

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221579.png)

地址栏敲`cmd` 在弹出的黑窗口中输入**`consul -version`** 查看consul的版本信息。

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221580.png)

使用开发者模式启动consul命令：`**consul agent -dev` 。**启动成功页面如下图：

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221581.png)

通过以下地址 [http://localhost:8500](http://localhost:8500/) 可以访问Consul的首页:

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221582.png)

# 三、微服务提供者注册进去Consul中

- 1、新建Modul支付服务 cloud-providerconsul-payment8006
- 2、改POM文件
  
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
    
        <artifactId>cloud-providerconsul-payment8006</artifactId>
    
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-consul-discovery -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-consul-discovery</artifactId>
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
    
- 3、写YML：application.yml
  
    ```yaml
    #consul服务端口号
    server:
      port: 8006
    
    #对外暴漏的服务名称
    spring:
      application:
        name: consul-provider-payment
    #consul注册中心地址
      cloud:
        consul:
          host: localhost
          port: 8500
          discovery:
            #hostname:127.0.0.1
            service-name: ${spring.application.name}
    ```
    
- 4、主启动类
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2/7/2021 下午 10:00
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableDiscoveryClient
    public class PaymentMain8006 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain8006.class, args);
        }
    }
    ```
    
- 5、业务类：Controller
  
    ```java
    @RestController
    @Slf4j
    public class PaymentController {
        @Value("${server.port}")
        private String serverPort;
    
        @GetMapping(value = "/payment/consul")
        public String paymentConsul() {
            return "springcloud with consul:" + serverPort + "\t" + UUID.randomUUID().toString();
        }
    }
    ```
    
- 6、验证测试
  
    **测试1：** idea中启动我们的8006微服务（启动8006主启动类）浏览器地址栏中输入：[http://localhost:8500/](http://localhost:8500/) 可以看到
    
    ![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221583.png)
    
    **测试2：**[http://localhost:8006/payment/consul](http://localhost:8006/payment/consul)
    
    ![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221584.png)
    

# 四、微服务消费者注册进去Consul中

- 1、新建Modul消费服务 cloud-consumerconsul-order80
- 2、改POM文件
  
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
    
        <artifactId>cloud-consumerconsul-order80</artifactId>
    
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-consul-discovery -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-consul-discovery</artifactId>
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
    
- 3、写YML：application.yml
  
    ```yaml
    #consul服务端口号
    server:
      port: 80
    
    spring:
      application:
        name: consul-consumer-order
    #consul注册中心地址
      cloud:
        consul:
          host: localhost
          port: 8500
          discovery:
            #hostname: 127.0.0.1
            service-name: ${spring.application.name}
    ```
    
- 4、主启动类
  
    ```java
    @SpringBootApplication
    @EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
    public class OrderConsulMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderConsulMain80.class,args);
        }
    }
    ```
    
- 5、配置Bean
  
    ```java
    package com.youliao.springcloud.config;
    
    import org.springframework.cloud.client.loadbalancer.LoadBalanced;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.client.RestTemplate;
    
    /**
     * @Author Dali
     * @Date 2/7/2021 下午 7:46
     * @Version 1.0
     * @Description
     */
    @Configuration
    public class ApplicationContextConfig {
        @Bean
        @LoadBalanced
        public RestTemplate getRestTemplate() {
            return new RestTemplate();
        }
    }
    ```
    
- 6、配置Controller
  
    ```java
    package com.youliao.springcloud.controller;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.client.RestTemplate;
    
    import javax.annotation.Resource;
    
    /**
     * @Author Dali
     * @Date 3/7/2021 上午 12:16
     * @Version 1.0
     * @Description
     */
    @RestController
    @Slf4j
    public class OrderConsulController {
        public static final String INVOKE_URL = "http://consul-provider-payment";
    
        @Resource
        private RestTemplate restTemplate;
    
        @GetMapping(value = "/consumer/payment/consul")
        public String paymentInfo() {
            String result = restTemplate.getForObject(INVOKE_URL + "/payment/consul", String.class);
            return result;
        }
    }
    ```
    
- 7、验证测试
  
    **测试1：**[http://localhost:8500/](http://localhost:8500/)   idea中启动我们的cloud-consumerconsul-order80服务和cloud-providerconsul-payment8006，在浏览器中输入测试地址，如图：
    
    ![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221585.png)
    
    **测试2：**[http://localhost/consumer/payment/consul](http://localhost/consumer/payment/consul)
    
    ![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221586.png)
    

# 五、Eureak、zookeeper和Consul三个注册中心异同点有哪些？




## SpringCloud相关组件停更后的替代产品图：

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221587.png)

[三个注册中心的区别](https://www.notion.so/8b066179311949e1964a2e59a8343f7a)

## [CAP是啥意思:](https://www.notion.so/CAP-5ee4e50fc5ff45a4903d7cd5ba286383)

CAP理论关注粒度是数据，而不是整体系统设计的策略

1. **C：**Consistency （强一致性）
2. **A：**Availability （可用性）
3. **P：**Partition tolerance （分区容错性）分布式微服务架构一定是要保证这个 **P** 的，其余的任意保证一个即可。
- **AP架构：Eureka** （理念：好死不如赖活着）
  
    Eureka的自我保护机制，更强调的是AP，保证微服务的高可用，好死不如赖活着。你就是偶尔宕机掉线了，Eureka也不会立即把某微服务删除踢走。

AP架构（Eureka）当网络分区出现后，为了保证可用性，系统B可以返回旧值,保证系统的可用性。

结论：违背了一致性C的要求，只满足可用性和分区容错，即AP。

![image-20220602222330650](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022223799.png)

- **CP架构：Consul和Zookeeper**
  
    当网络分区出现后，为了保证一致性, 就必须拒接请求，否则无法保证一致性
    
    结论：违背了可用性A的要求，只满足一致性和分区容错,即CP
    
    ![image-20220602222438776](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022224900.png)
    
    
    

CP是数据，就必须要一致，如果不一致，系统挂或者报错，举例：京东网站在双十一的时候主要保证的是【A:可用性】**可用**（分布式微服务必须是保证P的），也就是说京东购物网站这个系统必须是AP的，至于数据部分不相关的，我们允许它一定范围内出错，比如一件热门商品，都有用户的点赞数，分享数等数据传入后台，但是我们可以在一定程度上允许传入后台的与实际的数据不一致（！C）（后期我们在通过相关技术base理论和柔性事务补充修正数据）牺牲这个C（强一致性）来保证A（可用性），不能因为数据不一致导致整个系统瘫痪吧。

## [经典CAP图：](https://www.notion.so/CAP-5ee4e50fc5ff45a4903d7cd5ba286383)

![06_Consul%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%20fd481d7a5d9848cd9fc4f5a4f5dd9f35/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022221590.png)