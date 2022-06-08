# 16_SpringCloud Alibaba Nacos服务注册和配置中心

# 一、Nacos服务注册和配置中心简介

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004902.png)

**为什么叫Nacos：**前四个字母分别为**Naming**和**Configuration**的前两个字母，最后的**s**为**Service。**

## Nacos是什么：

一个更易于构建云原生应用的动态服务发现，配置管理和服务。

Nacos：Dynamic Naming and Configuration Server。

**Nacos就是注册中心 + 配置中心的组合**。

**Nacos等价于**：Nacos = Eureka+Config+Bus。

## Nacos能干嘛：

1. 替代Eureka做服务注册中心；
2. 替代Config做服务配置中心；

## Nacos去哪里下载（附官方文档）：

Nacos下载地址：[https://github.com/alibaba/Nacos](https://github.com/alibaba/Nacos)

**Nacos官网文档：**

Nacos文档地址1：[https://nacos.io/zh-cn/index.html](https://nacos.io/zh-cn/index.html)

Nacos文档地址2：[https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_nacos_discovery](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_nacos_discovery)

## 各种注册中心Eureka/Zookeeper/Consul/Nacos比较：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004903.png)

# 二、安装并运行Nacos

前提：**本地Java8+Maven环境已经OK。**

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004904.png)

## 先从官网下载Nacos：启动Nacos

1、官网下载地址：[https://github.com/alibaba/nacos/releases/tag/1.1.4](https://github.com/alibaba/nacos/releases/tag/1.1.4)

2、解压安装包，直接运行**bin**目录下的`startup.cmd`

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004905.png)

3、命令运行成功后直接访问[http://localhost:8848/nacos](http://localhost:8848/nacos)：**默认账号密码都是nacos**

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004906.png)

# 三、Nacos作为服务注册中心演示

官网文档：参考上面——[https://nacos.io/zh-cn/docs/what-is-nacos.html](https://nacos.io/zh-cn/docs/what-is-nacos.html)

## 1、基于Nacos的服务提供者：

- 1、新建Module：cloudalibaba-provider-payment9001
- 2、POM
    - 父工程POM
      
        ```xml
        <!--spring cloud alibaba 2.1.0.RELEASE-->
        <dependency>
          <groupId>com.alibaba.cloud</groupId>
          <artifactId>spring-cloud-alibaba-dependencies</artifactId>
          <version>2.1.0.RELEASE</version>
          <type>pom</type>
          <scope>import</scope>
        </dependency>
        ```
        
    - 9001模块的POM
      
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
        
            <artifactId>cloudalibaba-provider-payment9001</artifactId>
        
            <dependencies>
                **<dependency>
                    <groupId>com.alibaba.cloud</groupId>
                    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
                </dependency>**
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
                <dependency>
                    <groupId>com.alibaba</groupId>
                    <artifactId>fastjson</artifactId>
                    <version>1.2.62</version>
                </dependency>
            </dependencies>
        </project>
        ```
    
- 3、YML
  
    ```yaml
    server:
      port: 9001
    
    spring:
      application:
        name: nacos-payment-provider
      **cloud:
        nacos:
          discovery:
            server-addr: localhost:8848 #配置Nacos地址**
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```
    
- 4、主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 上午 11:04
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableDiscoveryClient
    public class PaymentMain9001 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain9001.class, args);
        }
    }
    ```
    
- 5、业务类
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 上午 11:08
     * @Version 1.0
     * @Description
     */
    @RestController
    public class PaymentController {
        @Value("${server.port}")
        private String serverPort;
    
        @GetMapping(value = "/payment/nacos/{id}")
        public String getPayment(@PathVariable("id") Integer id) {
            return "nacos registry, serverPort: " + serverPort + "\t id" + id;
        }
    }
    
    ```
    
- 6、测试
  
    首先通过`startup.cmd`启动nacos（文件地址：D:\develop\Utils\nacos-server-1.1.4\nacos\bin)。然后浏览器登陆[http://localhost:8848/nacos](http://localhost:8848/nacos)（用户名和密码均为：**nacos**）
    
    启动PaymentMain9001服务：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004907.png)
    
    - 测试地址：[http://localhost:9001/payment/nacos/1](http://localhost:9001/payment/nacos/1)
      
        ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004908.png)
        
    - nacos控制台：nacos-payment-provider已经成功注册了
      
        nacos服务注册中心+服务提供者9001都ok了。
        
        ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004909.png)
        

---

- 1、新建Module：cloudalibaba-provider-payment9002
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
    
        <artifactId>cloudalibaba-provider-payment9002</artifactId>
    
        <dependencies>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>1.2.62</version>
            </dependency>
        </dependencies>
    </project>
    ```
    
- 3、YML
  
    ```yaml
    server:
      port: 9002
    
    spring:
      application:
        name: nacos-payment-provider
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848 #配置Nacos地址
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```
    
- 4、主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 12:11
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    public class PaymentMain9002 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain9002.class, args);
        }
    }
    ```
    
- 5、业务类
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 上午 11:08
     * @Version 1.0
     * @Description
     */
    @RestController
    public class PaymentController {
        @Value("${server.port}")
        private String serverPort;
    
        @GetMapping(value = "/payment/nacos/{id}")
        public String getPayment(@PathVariable("id") Integer id) {
            return "nacos registry, serverPort: " + serverPort + "\t id" + id;
        }
    }
    ```
    
- 6、测试
  
    启动以下服务：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004910.png)
    
    地址1：[http://localhost:9001/payment/nacos/1](http://localhost:9001/payment/nacos/1)
    
    地址2：[http://localhost:9002/payment/nacos/1](http://localhost:9002/payment/nacos/1)
    
    nacos地址：[http://localhost:8848/nacos](http://localhost:8848/nacos)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/%E5%BD%95%E5%88%B6_2021_07_24_12_22_16_568.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004911.gif)
    

## [小技巧]idea直接拷贝虚拟端口映射：

为了下一章节演示nacos的负载均衡，参照9001新建9002，新建cloudalibaba-provider-payment9002，9002其他步骤你懂的。或者取巧不想新建重复体力劳动，直接拷贝虚拟端口映射。

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004912.png)

在VM options选项中添加：`DServer.port=9011`

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004913.png)

启动我们刚才拷贝的虚拟映射：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004914.png)

浏览器输入：[http://localhost:9001/payment/nacos/1](http://localhost:9001/payment/nacos/1)发送请求，然后在nacos中的**服务列表**中查看：[http://localhost:8848/nacos](http://localhost:8848/nacos)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004915.png)

点击详情看集群ip及其端口：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004916.png)

## 2、基于Nacos的服务消费者：

- 1、新建Module：cloudalibaba-consumer-nacos-order83
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
    
        <artifactId>cloudalibaba-consumer-nacos-order83</artifactId>
        <dependencies>
            <!--SpringCloud ailibaba nacos -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <dependency>
                <groupId>com.atguigu.springcloud</groupId>
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
      port: 83
    
    spring:
      application:
        name: nacos-order-consumer
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
            
    # 消费者将要去访问的微服务名称（注册成功进nacos的微服务提供者）
    service-url:
      nacos-user-service: http://nacos-payment-provider
    ```
    
- 4、主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 12:35
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    public class OrderNacosMain83 {
        public static void main(String[] args) {
            SpringApplication.run(OrderNacosMain83.class, args);
        }
    }
    ```
    
- 5、业务类
  
    ApplicationContextConfig：因为nacos集成了Ribbon，因此需要配置RestTemplate，同时通过注解 `@LoadBalanced`实现负载均衡，默认是轮询的方式。
    
    ```java
    package com.youliao.springcloud.alibaba.config;
    
    import org.springframework.cloud.client.loadbalancer.LoadBalanced;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.client.RestTemplate;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 12:41
     * @Version 1.0
     * @Description
     */
    @Configuration
    class ApplicationContextConfig {
        @Bean
        @LoadBalanced
        public RestTemplate getRestTemplate() {
            return new RestTemplate();
        }
    }
    ```
    
    OrderNacosController：
    
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.client.RestTemplate;
    
    import javax.annotation.Resource;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 12:45
     * @Version 1.0
     * @Description
     */
    @RestController
    @Slf4j
    public class OrderNacosController {
        @Resource
        private RestTemplate restTemplate;
    
        @Value("${service-url.nacos-user-service}") //读取配置文件application.yml对应的信息。
        private String serverURL;
    
        @GetMapping(value = "/consumer/payment/nacos/{id}")
        public String paymentInfo(@PathVariable("id") Long id) {
            return restTemplate.getForObject(serverURL + "/payment/nacos/" + id, String.class);
        }
    }
    ```
    
- 6、测试
  
    启动以下服务：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004917.png)
    
    nacos控制台：[http://localhost:8848/nacos](http://localhost:8848/nacos)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004918.png)
    
    浏览器中输入[http://localhost:83/consumer/payment/nacos/13](http://localhost:83/consumer/payment/nacos/13)
    
    83访问9001/9002，轮询负载OK：我们发现只需要配置了nacos，就轻松实现负载均衡。
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/%E5%BD%95%E5%88%B6_2021_07_24_12_59_58_927.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004919.gif)
    

Nacos整合了ribbon，就可以用RestTemplate 。

### [为什么nacos支持负载均衡](https://www.notion.so/16_SpringCloud-Alibaba-Nacos-eb83edba45de429baa1685ff11431e4f)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004920.png)

### Nacos全景图所示：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004921.png)

### Nacos和CAP：

CAP：分别是一致性，可用性，分区容错性

我们从下图能够看到，nacos不仅能够和Dubbo整合，还能和K8s，也就是偏运维的方向。

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004922.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004923.png)

### Nacos支持AP和CP模式的切换

**C是指所有的节点同一时间看到的数据是一致的，而A的定义是所有的请求都会收到响应**

**合适选择何种模式？**

一般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如Spring Cloud 和 Dubbo服务，都是适合AP模式，AP模式为了服务的可用性而减弱了一致性，因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或存储配置信息，那么CP是必须，K8S服务和DNS服务则适用于CP模式。

CP模式下则支持注册持久化实例，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误

```bash
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
```

# 四、Nacos作为服务配置中心演示（重要）

所有配置中心，以前我们写到了github上面，然后用config结合bus来进行自动刷新配置和动态刷新。现在，我们可以少一些代码间的耦合，我们可以把我们的配置信息直接写在nacos中，然后再让nacos做类似SpringCloud Config这样的功能，从nacos上面抓取我们需要的配置信息。

## 1、**Nacos作为配置中心**-基础配置

**为什么配置两个YML文件：**

Nacos同SpringCloud Config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置之后，才能保证项目的正常启动。（现有共性再有个性）

**SpringBoot中配置文件的加载是存在优先级顺序的：bootstrap.yml优先级 高于 application.yml**

- 1、新建Module：cloudalibaba-config-nacos-client3377
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
    
        <artifactId>cloudalibaba-config-nacos-client3377</artifactId>
    
        <dependencies>
            **<!--nacos-config-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
            </dependency>**
            <!--nacos-discovery-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <!--web + actuator-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!--一般基础配置-->
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
    
- 3、YML：需要配置两个
    - bootstrap.yml
      
        ```yaml
        # Nacos配置
        server:
          port: 3377
        
        spring:
          application:
            name: nacos-config-client
          cloud:
            nacos:
              discovery:
                server-addr: localhost:8848 #Nacos服务注册中心地址
              config:
                server-addr: localhost:8848 #Nacos配置中心地址
                file-extension: yaml #指定yaml格式的配置
        
        #  ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
        ```
        
    - application.yml
      
        ```yaml
        spring:
          profiles:
            active: dev # 开发环境
            #active: test # 测试环境
            #active: info # 开发环境
        ```
    
- 4、主启动
  
    ```java
    package com.youliao.springcloud.alibaba;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 4:13
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    public class NacosConfigClientMain3377 {
        public static void main(String[] args) {
            SpringApplication.run(NacosConfigClientMain3377.class, args);
        }
    }
    ```
    
- 5、业务类：ConfigClientController
  
    **@RefreshScope：**通过SpringCloud原生注解`@RefreshScope` 实现**配置自动更新。**
    
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.cloud.context.config.annotation.RefreshScope;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/07/24 下午 4:14
     * @Version 1.0
     * @Description
     */
    @RestController
    @RefreshScope //通过Spring Cloud原生注解@RefreshScope 实现配置自动更新:
    public class ConfigClientController {
        @Value("${config.info}")
        private String configInfo;
    
        @GetMapping("/config/info")
        public String getConfigInfo() {
            return configInfo;
        }
    }
    ```
    
- 6、在Nacos中添加配置信息： 💚
  
    # 1、Nacos中的匹配规则：
    
    ## 1.1 理论：
    
    Nacos中的**dataid**的组成格式与SpringBoot配置文件中的匹配规则。
    
    最后公式：
    
    **${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}**
    
    Nacos官网文档：[https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html](https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004924.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004925.png)
    
    ## 1.2 实操：
    
    ### 1.2.1 配置新增：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004926.png)
    
    ### 1.2.2 Nacos界面配置对应：
    
    这里需要注意的是，在`config:` 的后面必须加上一个空格。注意缩进，养成良好习惯。
    
    ```yaml
    config: 
        info: nacos config center,version = 1
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004927.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004928.png)
    
    **为什么Data ID要添加**：**nacos-config-client-dev.yml？ 这串配置从何而来？**
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004930.png)
    
    ### 1.2.3 设置DataId相关说明
    
    > 公式：
    > 
    
    **${[spring.application.name](http://spring.application.name/)}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}**
    
    1. prefix默认为spring.application.name的值。
    2. spring.profile.active既为当前环境对应的profile,可以通过配置项spring.profile.active 来配置。
    3. file-exetension为配置内容的数据格式，可以通过配置项spring.cloud.nacos.config.file-extension配置
    4. 小总结说明：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004931.png)
    
- 7、测试：启动前需要在**nacos客户端**-**配置管理**-**配置管理**栏目下**有没有对应的yaml配置文件**
  
    确定有上述的**yaml**配置文件（而不是yml后缀），然后运行**cloud-config-nacos-client:3377的主启动类，**调用接口查看配置信息。
    
    启动3377时报错：Caused by: java.lang.IllegalArgumentException: Could not resolve placeholder '[config.info](http://config.info/)' in value "${[config.info](http://config.info/)}"
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004932.png)
    
    这是因为无法读取配置所引起的，解决方案就是我们的文件名不能用 .yml 而应该是 .yaml。
    
    ```yaml
    config: 
        info: from nacos config center, nacos-config-client-dev.yaml, version=1
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004933.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004934.png)
    
    改完`.yaml`之后重启我们的**cloud-config-nacos-client:3377的主启动类 发现没有报错，正常启动了**
    
    调用接口查看配置信息：[http://localhost:3377/config/info](http://localhost:3377/config/info)
    
    ![发现与nacos中的配置信息一样，说明成功获取到了nacos中的配置信息。](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004935.png)
    
    发现与nacos中的配置信息一样，说明成功获取到了nacos中的配置信息。
    
- 8、**nacos自带动态刷新**：修改下Nacos中的yaml配置文件，再次调用查看配置的接口，就会发现配置已经刷新
  
    测试地址：[http://localhost:3377/config/info](http://localhost:3377/config/info)；
    
    nacos改完发布之后 浏览器自动得到最新的数据。
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004936.png)
    

## 2、Nacos作为配置中心-分类配置

<aside>
💡 问题：多环境多项目管理：
> **问题1：**

在实际开发中，通常一个系统会准备

- dev开发环境
- test测试环境
- prod生产环境

如何保证指定环境启动时，服务能正确读取到Nacos上相应环境的配置文件呢？

> **问题2：**
> 

同时，一个大型分布式微服务系统会有很多微服务子项目，每个微服务子项目又都会有相应的开发环境，测试环境，预发环境，正式环境，那怎么对这些微服务配置进行管理呢？

### Nacos的图形化管理界面：

> 配置管理：
> 

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004937.png)

> 命名空间：
> 

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004938.png)

### Namespace+Group+Data ID三者关系？为什么这么设计？

> Namespace + Group + Data ID 三者关系：
> 

1、是什么

这种分类的设计思想，就类似于java里面的package名 和 类名，最外层的namespace是可以用于区分部署环境的，Group 和 DataID逻辑上区分两个目标对象。

2、三者情况

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004939.png)

3、默认情况：

**Namespace=public，Group=DEFAULT_GROUP，默认Cluster是DEFAULT**。

Nacos默认的命名空间是public，Namespace主要用来实现隔离。

比如说我们现在有三个环境：开发，测试，生产环境，我们就可以建立三个Namespace，不同的Namespace之间是隔离的。

**Group默认是DEFAULT_GROUP**，Group可以把不同微服务划分到同一个分组里面去。

Service就是微服务，一个Service可以包含多个Cluster（集群），Nacos默认Cluster是DEFAULT，Cluster是对指定微服务的一个虚拟划分。比如说为了容灾，将Service微服务分别部署在了杭州机房，这时就可以给杭州机房的Service微服务起一个集群名称（HZ），给广州机房的Service微服务起一个集群名称，还可以尽量让同一个机房的微服务相互调用，以提升性能，最后Instance，就是微服务的实例。

## 三种方案加载配置：DataID方案、Group方案、Namespace方案

### 1、DataID方案：

- 指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置。（常用）
- 通过spring.profile.active属性就能进行多环境下配置文件的读取
  
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004940.png)
    

默认空间+默认分组+新建dev和test两个DataID。

> **1.1 新建dev配置DataID：**
> 

```yaml
config: 
    info: from nacos config center, nacos-config-client-test.yaml, version=2
```

![讲义配图：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004941.png)

讲义配图：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004942.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2038.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004943.png)

> **1.2 新建test配置DataID：**
> 

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2039.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004944.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2040.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004945.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2041.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004946.png)

只启动`**NacosConfigClientMain3377**`，浏览器测试：[http://localhost:3377/config/info](http://localhost:3377/config/info)

![配置是什么就加载什么：test](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004947.png)

配置是什么就加载什么：test

### 2、Group方案：

通过Group实现环境区分，**新建DEV_GROUP和TEST_GROUP**

```yaml
config: 
    info: nacos-config-client-info.yaml, DEV_GROUP

```

[https://www.notion.so](https://www.notion.so)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2043.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004948.png)

```yaml
config: 
    info: nacos-config-client-info.yaml, TEST_GROUP
```

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2044.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004949.png)

Nacos配置列表首页图如下：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2045.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004950.png)

**结合bootstrap+application**：**在config下增加一条group的配置即可。可配置为DEV_GROUP或TEST_GROUP**

```yaml
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos配置中心地址
        file-extension: yaml #指定yaml格式的配置
        **#group: TEST_GROUP
        group: DEV_GROUP**
```

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2046.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004951.png)

然后重启：NacosConfigClientMain3377，浏览器刷新：[http://localhost:3377/config/info](http://localhost:3377/config/info)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2047.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004952.png)

### 3、Namespace方案：

- 3.1 新建dev/test的Namespace
  
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2048.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004953.png)
    
    新建dev命名空间：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2049.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004954.png)
    
    新建test命名空间：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2050.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004955.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2051.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004956.png)
    
- 3.2 回到服务管理-服务列表查看
  
    创建完成后，回到**服务管理-服务列表查**看，我们会发现，多出了几个命名空间切换。
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2052.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004957.png)
    
- 3.3 按照域名配置填写
  
    我们以dev为例填写三个以下三个
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2053.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004958.png)
    
    > 新建1：DEFAULT_GROUP
    > 
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2054.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004959.png)
    
    ```yaml
    config: 
        info: 2e0d56cd-cfc9-4d62-802f-67aa45aefda0 dev namespace
    
    修改版：
    config: 
        info: 2e0d56cd-cfc9-4d62-802f-67aa45aefda0 , DEFAULT_GROUP ,nacos-config-client-dev.yaml
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2055.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004960.png)
    
    > 新建2：DEV_GROUP
    > 
    
    ```yaml
    config: 
        info: 2e0d56cd-cfc9-4d62-802f-67aa45aefda0 , DEV_GROUP ,nacos-config-client-dev.yaml
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2056.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004961.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2057.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004962.png)
    
    > 新建3：TEST_GROUP
    > 
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2058.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004963.png)
    
    返回到配置列表查看dev命名空间下的组信息：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2059.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004964.png)
    
    然后测试：
    
    1、浏览器输入：[http://localhost:3377/config/info](http://localhost:3377/config/info) 
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2060.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004965.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2061.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004966.png)
    
    浏览器回显：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2062.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004967.png)
    

# 五、Nacos集群和持久化配置（重要）

## Nacos集群官网说明

Nacos集群官网地址：[https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html](https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html)

官网架构图（写的(┬＿┬)）一个不太明白：

![官网架构图](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004968.png)

官网架构图

上图官网翻译，真实情况：

![对官网给出的图例进行翻译解释，容易看懂就是这么一个图](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004969.png)

对官网给出的图例进行翻译解释，容易看懂就是这么一个图

**默认Nacos使用嵌入数据库实现数据的存储**，所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题的。为了解决这个问题，**Nacos采用了集中式存储的方式来支持集群化部署**，目前只支持MySQL的存储。

## Nacos支持三种部署模式

- 单机模式：用于测试和单机使用
- **集群模式：用于生产环境，确保高可用**
- 多集群模式：用于多数据中心场景

## windows启动Nacos：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2065.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004971.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2066.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004972.png)

## **单机模式支持mysql**

[Nacos支持三种部署模式](https://nacos.io/zh-cn/docs/deployment.html)

在0.7版本之前，在单机模式下nacos使用嵌入式数据库实现数据的存储，不方便观察数据存储的基本情况。0.7版本增加了支持mysql数据源能力，具体的操作流程：

- 安装数据库，版本要求：5.6.5 +
- 初始化数据库，数据库初始化文件：nacos-mysql.sql
- 修改conf/application.properties文件，增加mysql数据源配置，目前仅支持mysql，添加mysql数据源的url，用户名和密码

```sql
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://11.162.196.16:3306/nacos_devtest?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=nacos_devtest
db.password=youdontknow
```

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2067.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004973.png)

再次以单机模式启动nacos，nacos所有写嵌入式数据库的数据都写到了mysql中。

## Nacos持久化配置解释：（nacos数据库derby）

nacos自带derby数据库：github源码查看：[https://github.com/alibaba/nacos/blob/develop/config/pom.xml](https://github.com/alibaba/nacos/blob/develop/config/pom.xml)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2068.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004974.png)

## 要想Nacos持久化必须完成derby到mysql的切换，附配置步骤：

Nacos默认自带的是嵌入式数据库derby，部署集群的时候难免会造成数据的不统一，因此我们需要完成derby到mysql切换配置步骤：

- nacos-server-1.1.4\nacos\conf目录下找到SQL脚本

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2069.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004975.png)

- 执行脚本：
  
    ```sql
    CREATE DATABASE nacos_config;
    USE nacos_config;
     
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info   */
    /******************************************/
    CREATE TABLE `config_info` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT    'data_id',
      `group_id` varchar(255) DEFAULT NULL,
      `content` longtext NOT NULL COMMENT 'content',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      `app_name` varchar(128) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      `c_desc` varchar(256) DEFAULT NULL,
      `c_use` varchar(64) DEFAULT NULL,
      `effect` varchar(64) DEFAULT NULL,
      `type` varchar(64) DEFAULT NULL,
      `c_schema` text,
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_aggr   */
    /******************************************/
    CREATE TABLE `config_info_aggr` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(255) NOT NULL COMMENT 'group_id',
      `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
      `content` longtext NOT NULL COMMENT '内容',
      `gmt_modified` datetime NOT NULL COMMENT '修改时间',
      `app_name` varchar(128) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';
     
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_beta   */
    /******************************************/
    CREATE TABLE `config_info_beta` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL COMMENT 'content',
      `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_tag   */
    /******************************************/
    CREATE TABLE `config_info_tag` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
      `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL COMMENT 'content',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_tags_relation   */
    /******************************************/
    CREATE TABLE `config_tags_relation` (
      `id` bigint(20) NOT NULL COMMENT 'id',
      `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
      `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
      `nid` bigint(20) NOT NULL AUTO_INCREMENT,
      PRIMARY KEY (`nid`),
      UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
      KEY `idx_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = group_capacity   */
    /******************************************/
    CREATE TABLE `group_capacity` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
      `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
      `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
      `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
      `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
      `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
      `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_group_id` (`group_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = his_config_info   */
    /******************************************/
    CREATE TABLE `his_config_info` (
      `id` bigint(64) unsigned NOT NULL,
      `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
      `data_id` varchar(255) NOT NULL,
      `group_id` varchar(128) NOT NULL,
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL,
      `md5` varchar(32) DEFAULT NULL,
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
      `src_user` text,
      `src_ip` varchar(20) DEFAULT NULL,
      `op_type` char(10) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`nid`),
      KEY `idx_gmt_create` (`gmt_create`),
      KEY `idx_gmt_modified` (`gmt_modified`),
      KEY `idx_did` (`data_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';
     
     
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = tenant_capacity   */
    /******************************************/
    CREATE TABLE `tenant_capacity` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
      `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
      `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
      `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
      `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
      `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
      `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';
     
     
    CREATE TABLE `tenant_info` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `kp` varchar(128) NOT NULL COMMENT 'kp',
      `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
      `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
      `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
      `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
      `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
      `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
      KEY `idx_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';
     
    CREATE TABLE users (
        username varchar(50) NOT NULL PRIMARY KEY,
        password varchar(500) NOT NULL,
        enabled boolean NOT NULL
    );
     
    CREATE TABLE roles (
        username varchar(50) NOT NULL,
        role varchar(50) NOT NULL
    );
     
    INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);
     
    INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');
    ```
    

[nacos_config.sql](16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/nacos_config.sql)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2070.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004976.png)

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2071.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004977.png)

- nacos-server-1.1.4\nacos\conf目录下找到application.properties
  
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2072.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004978.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2073.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004979.png)
    
    ```sql
    spring.datasource.platform=mysql
     
    db.num=1
    db.url.0=jdbc:mysql://11.162.196.16:3306/nacos_devtest?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
    db.user=nacos_devtest
    db.password=youdontknow
     
    ##################################################
     
    **spring.datasource.platform=mysql
     
    db.num=1
    db.url.0=jdbc:mysql://localhost:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
    db.user=root
    db.password=root**
    ```
    
- 启动nacos，可以看到是个全新的空记录界面，以前是记录进derby
  
    记录着derby中的数据
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2074.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004980.png)
    
    文件地址：D:\develop\Utils\nacos-server-1.1.4\nacos\bin
    
    关闭nacos：双击：`shutdown.cmd` ，再次启动nacos：`startup.cmd` ；
    
    再次打开nacos管理页面：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2075.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004981.png)
    
    在配置列表中新建一个：nacos地址：[http://localhost:8848/nacos](http://localhost:8848/nacos)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2076.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004982.png)
    
    在数据库中：查看nacos_config表：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2077.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004983.png)
    

## Linux版Nacos+MySQL生产环境配置： 🤩

预计需要，**1个nginx**+**3个nacos注册中心**+**1个mysql**；

所有的请求过来，首先先打到**nginx**上；

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2078.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004985.png)

### Nacos下载linux版本：

Linux版Nacos下载地址：

[Release 1.1.4(Oct 24th, 2019) · alibaba/nacos](https://github.com/alibaba/nacos/releases/tag/1.1.4)

官网下载对应版本的nacos压缩包：

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2079.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004986.png)

### nacos-server-1.1.4.tar.gz，解压后安装：

```bash
[root@localhost opt]# tar -zxvf nacos-server-1.1.4.tar.gz
```

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2080.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004987.png)

将opt目录下的nacos文件夹拷贝到根目录下的**mynacos**文件夹中(没有自动创建)

### Linux复制拷贝命令：

```bash
[root@localhost opt]# cp -r nacos /mynacos/
```

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2081.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004988.png)

### Linux下启动nacos：所在目录：**/mynacos/bin**

![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2082.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004989.png)

## 集群配置步骤（重点） 🥰

# CentOS7中登录mysql数据库：问题（附链接）

### 1.Linux服务器上mysql数据库配置

- SQL脚本在哪里
  
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2083.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004990.png)
    
- sql语句源文件：nacos-mysql.sql
  
    ```sql
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info   */
    /******************************************/
    CREATE TABLE `config_info` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(255) DEFAULT NULL,
      `content` longtext NOT NULL COMMENT 'content',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      `app_name` varchar(128) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      `c_desc` varchar(256) DEFAULT NULL,
      `c_use` varchar(64) DEFAULT NULL,
      `effect` varchar(64) DEFAULT NULL,
      `type` varchar(64) DEFAULT NULL,
      `c_schema` text,
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_aggr   */
    /******************************************/
    CREATE TABLE `config_info_aggr` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(255) NOT NULL COMMENT 'group_id',
      `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
      `content` longtext NOT NULL COMMENT '内容',
      `gmt_modified` datetime NOT NULL COMMENT '修改时间',
      `app_name` varchar(128) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_beta   */
    /******************************************/
    CREATE TABLE `config_info_beta` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL COMMENT 'content',
      `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_info_tag   */
    /******************************************/
    CREATE TABLE `config_info_tag` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
      `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL COMMENT 'content',
      `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      `src_user` text COMMENT 'source user',
      `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = config_tags_relation   */
    /******************************************/
    CREATE TABLE `config_tags_relation` (
      `id` bigint(20) NOT NULL COMMENT 'id',
      `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
      `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
      `data_id` varchar(255) NOT NULL COMMENT 'data_id',
      `group_id` varchar(128) NOT NULL COMMENT 'group_id',
      `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
      `nid` bigint(20) NOT NULL AUTO_INCREMENT,
      PRIMARY KEY (`nid`),
      UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
      KEY `idx_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = group_capacity   */
    /******************************************/
    CREATE TABLE `group_capacity` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
      `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
      `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
      `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
      `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
      `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
      `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_group_id` (`group_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = his_config_info   */
    /******************************************/
    CREATE TABLE `his_config_info` (
      `id` bigint(64) unsigned NOT NULL,
      `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
      `data_id` varchar(255) NOT NULL,
      `group_id` varchar(128) NOT NULL,
      `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
      `content` longtext NOT NULL,
      `md5` varchar(32) DEFAULT NULL,
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
      `src_user` text,
      `src_ip` varchar(20) DEFAULT NULL,
      `op_type` char(10) DEFAULT NULL,
      `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
      PRIMARY KEY (`nid`),
      KEY `idx_gmt_create` (`gmt_create`),
      KEY `idx_gmt_modified` (`gmt_modified`),
      KEY `idx_did` (`data_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';
    
    /******************************************/
    /*   数据库全名 = nacos_config   */
    /*   表名称 = tenant_capacity   */
    /******************************************/
    CREATE TABLE `tenant_capacity` (
      `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
      `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
      `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
      `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
      `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
      `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
      `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
      `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
      `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
      `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';
    
    CREATE TABLE `tenant_info` (
      `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
      `kp` varchar(128) NOT NULL COMMENT 'kp',
      `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
      `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
      `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
      `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
      `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
      `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
      PRIMARY KEY (`id`),
      UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
      KEY `idx_tenant_id` (`tenant_id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';
    
    CREATE TABLE users (
    	username varchar(50) NOT NULL PRIMARY KEY,
    	password varchar(500) NOT NULL,
    	enabled boolean NOT NULL
    );
    
    CREATE TABLE roles (
    	username varchar(50) NOT NULL,
    	role varchar(50) NOT NULL
    );
    
    INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);
    
    INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');
    ```
    
- 我这里使用的是通过本地的Navicat远程连接虚拟机中的MySQL数据库的：
  
    我这里使用的是通过本地的Navicat远程连接虚拟机中的MySQL数据库的：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2084.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004991.png)
    
    新建查询：发现报错：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2085.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004992.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2086.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004993.png)
    
    那么我们就**新建一个nacos_config数据库**：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2087.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004994.png)
    
    然后选中我们创建的nacos_config数据库 鼠标右键 运行SQL文件
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2088.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004995.png)
    
    执行后结果：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2089.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004996.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2090.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004997.png)
    

### 2.application.properties配置

- 1、需要修改的配置文件的位置
  
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2091.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004998.png)
    
- 2、需要修改application.properties的配置文件的内容
  
    ```sql
    ##############################################
    
    spring.datasource.platform=mysql
     
    db.num=1
    db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
    db.user=root
    db.password=root
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2092.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004999.png)
    
    我是直接通过Xfty工具直接通过记事本修改的，记得保存。
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2093.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004000.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2094.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004001.png)
    
    ```sql
    mysql  授权远程访问
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
    flush privileges;
    ```
    

### 3.Linux服务器上nacos的集群配置cluster.conf：

- 3、Linux服务器上nacos的集群配置cluster.conf：
  
    梳理出3台nacos机器的不同服务端口号。
    
    复制出cluster.conf：（cluster.conf.example是系统自带的文件，我们复制一份改名为cluster.conf并修改，然后将系统自带的cluster.conf.example保留备份）
    
    ![拷贝副本](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004002.png)
    
    拷贝副本
    
    如果是多个nacos：3333,4444,5555
    
    ![注意IP地址](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004003.png)
    
    注意IP地址
    
    在/mynacos/conf目录下的cluster.conf文件中添加以下内容：
    
    192.168.187.150:3333
    192.168.187.150:4444
    192.168.187.150:5555
    
    ![记得把之前的内容前面加#注释掉](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004004.png)
    
    记得把之前的内容前面加#注释掉
    
    ## 查询ip地址——CentOS7中
    
    **这个IP不能写127.0.0.1,**必须是Linux命令`hostname -i`能够识别的IP：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2098.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004005.png)
    
    但是我在Linux中通过`hostname -i` 识别到的IP就是 ::1 127.0.0.1呀。
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%2099.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004006.png)
    
    通过 `hostname -I` 查看到的IP为：192.168.187.150
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20100.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004007.png)
    
    通过`ifconfig`查看IP地址
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20101.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004008.png)
    
    通过`ip addr` 或者 `ip address` 查询到的IP地址：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20102.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004009.png)
    

### [4.编辑Nacos的启动脚本startup.sh](http://4.xn--nacosstartup-bh7t29nlt0guk9brp4avuh7v5e.sh/)，使它能够接受不同的启动端

- 4、[4.编辑Nacos的启动脚本startup.sh](http://4.xn--nacosstartup-bh7t29nlt0guk9brp4avuh7v5e.sh/)，使它能够接受不同的启动端
  
    /mynacos/bin目录下有startup.sh；
    
    在什么地方，修改什么，怎么修改
    
    > 思考：
    > 
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20103.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004010.png)
    
    > 修改内容：
    > 
    
    改之前记得先备份：
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20104.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004011.png)
    
    添加执行权限：`chmod 755 [startup.sh](http://startup.sh/)`
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20105.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004012.png)
    
    配置说明：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004013.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20107.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004014.png)
    
    `[root@localhost bin]# ls -al` 查看隐藏文件
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20108.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004015.png)
    
    1、用命令恢复非正常文件，vim -r 非正常文件，然后再删除.swap文件，再次编辑文件时，不会再提示警告。
    
    2、用ls -al命令查询出.swap隐藏文件，并删除，下载再编辑文件时，不会再提示警告。
    
    ```sql
    [root@localhost bin]# rm -r .startup.sh.swp 
    rm：是否删除普通文件 ".startup.sh.swp"？Y  #敲回车
    [root@localhost bin]# ls -al   #查看隐藏文件
    ```
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20109.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004016.png)
    
    - 修改内容startup.sh
      
        ```bash
        while getopts ":m:f:s:p:" opt
        do
            case $opt in
                m)
                    MODE=$OPTARG;;
                f)
                    FUNCTION_MODE=$OPTARG;;
                s)
                    SERVER=$OPTARG;;
                p)
                    PORT=$OPTARG;;
                ?)
                echo "Unknown parameter"
                exit 1;;
            esac
        done
        
        ....................................
        
        # start
        echo "$JAVA ${JAVA_OPT}" > ${BASE_DIR}/logs/start.out 2>&1 &
        nohup $JAVA -Dserver.port=${PORT} ${JAVA_OPT} nacos.nacos >> ${BASE_DIR}/logs/start.out 2>&1 &
        echo "nacos is starting，you can check the ${BASE_DIR}/logs/start.out"
        ```
        
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20110.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004017.png)
    
    修改startup.s
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004018.png)
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20112.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004019.png)
    

### 执行nacos：启动nacos启动:

[ Nacos启动报错解决：which: no javac in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)_非著名运维的博客-CSDN博客](https://www.notion.so/Nacos-which-no-javac-in-usr-local-sbin-usr-local-bin-usr-sbin-usr-bin-root-bin-_--1a10aaaa8ac64b22b28fec87eb6b17cc)

[root@localhost bin]# ./startup.sh -p 3333

[root@localhost bin]# ./startup.sh -p 4444

[root@localhost bin]# ./startup.sh -p 5555

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004020.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004021.png)

如果是一个nacos：启动 8848即可

如果是多个nacos：3333,4444,5555

那么就需要修改startup.sh里面的，传入端口号

步骤：

- Linux服务器上mysql数据库配置
- application.properties配置
- Linux服务器上nacos的集群配置cluster.conf
    - 梳理出3台nacos集群的不同服务端口号
    - 复制出cluster.conf（备份）
    - 修改

### 5.Nginx的配置，由它作为负载均衡器

- 5.Nginx的配置，由它作为负载均衡器
  
    > 修改nginx的配置文件
    > 
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004022.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004023.png)
    
    > nginx.conf
    > 
    
    ```bash
    upstream cluster{                                                        
     
        server 127.0.0.1:3333;
        server 127.0.0.1:4444;
        server 127.0.0.1:5555;
    }
    ```
    
    ```bash
    server{
                              
        listen 1111;
        server_name localhost;
        location /{
             proxy_pass http://cluster;
                                                            
        }
    ....省略
    ```
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004024.png)
    
    我的改完之后：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004025.png)
    
    > 按照指定启动
    > 
    
    ![16_SpringCloud%20Alibaba%20Nacos%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E5%92%8C%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83%2035594f3e266c44a7b215346b6e39ca7b/Untitled%20119.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004026.png)
    

## CentOS 7中启动nacos：

- 启动nacos：`/mynacos/bin`目录下执行 `[root@localhost bin]# ./startup.sh -p 3333`
  
    [root@localhost bin]# ./startup.sh -p 3333
    
    [root@localhost bin]# ./startup.sh -p 4444
    
    [root@localhost bin]# ./startup.sh -p 5555
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004027.png)
    
    启动完三个：通过`[root@localhost bin]# ps -ef|grep nacos|grep -v grep |wc -l` 命令查看启动了几个(服务)nacos，发现数目为：2。而不是3
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004028.png)
    
    此时我们考虑是不是Centos内存不够了，那么我们增大虚拟机的内存试一试：数量为1或者2的先**关闭虚拟机（是关机而不是挂起）**，然后编辑此虚拟机，增大内存至4G。重新启动nacos集群：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004029.png)
    
    尝试再次启动nacos集群
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004031.png)
    

## CentOS 7中启动nginx：按照指定启动nginx

- 启动nginx：按照指定启动nginx
  
    `cd /usr/local/nginx/sbin/` 进入以下目录：
    
    执行以下命令：
    
    `[root@localhost sbin]# **./nginx -c /usr/local/nginx/conf/nginx.conf**`
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004032.png)
    
    `[root@localhost sbin]# **ps -ef|grep nginx**` 妥了成功启动nginx
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004033.png)
    
    ### 查看进程并结束进程：
    
    先查看进程PID：`[root@localhost sbin]# **ps -ef|grep nginx**` 
    
    然后使用 `[root@localhost sbin]# **kill -9 3093**` 杀掉进程，3093为进程号。
    

## CentOS 7中启动MySQL

- CentOS 7中启动mysql
  
    执行以下命令：`[root@localhost /]# mysql -u root -p` 输入密码即可
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004034.png)
    
    可以通过以下命令启动我们的MySQL服务：
    
    `service mysqld start`
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004035.png)
    

### 查看启动了几个MySQL服务：

- 查看启动了几个MySQL服务：
  
    `[root@localhost sbin]# ps -ef|grep mysql|grep -v grep|wc -l`
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004036.png)
    

## 6.截止到此处，1个Nginx+3个nacos注册中心+1个mysql

- 6.截止到此处，1个Nginx+3个nacos注册中心+1个mysql
  
    > 测试通过nginx访问nacos：
    > 
    
    地址公式：[写你自己虚拟机的ip:1111/nacos/#/login](https://xn--ip-0p3c87fqtym4g97hw46aipvvek:1111/nacos/#/login)
    
    ## CentOS7虚拟机nacos登录地址：
    
    [http://192.168.187.150:1111/nacos/#/login](http://192.168.187.150:1111/nacos/#/login)
    
    [](http://192.168.187.150:1111/nacos/#/login)
    
    通过集群的方式搭建nacos
    
    **这里一定要关掉虚拟机防火墙或者配置入站规则**
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004037.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004038.png)
    
    在**配置列表**中新建 配置
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004039.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004040.png)
    
    > 方式一：通过Navicate 本地连接远程服务器（CentOS7）数据库
    > 
    
    验证我们的nacos新建的配置列表是否保存到了我们自己的MySQL数据库中：
    
    `SELECT * FROM config_info`
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004041.png)
    
    > 方式二：通过一下命令：`mysql -u root -p` 然后输入密码：
    > 
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004042.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004043.png)
    

## 7.微服务cloudalibaba-provider-payment9002启动注册进nacos集群

- 7.微服务cloudalibaba-provider-payment9002启动注册进nacos集群
  
    application.yml：
    
    ```yaml
    server:
      port: 9002
    
    spring:
      application:
        name: nacos-payment-provider
      cloud:
        nacos:
          discovery:
            #server-addr: localhost:8848 #配置Nacos地址 本地nacas访问地址
            **server-addr: 192.168.187.150:1111 # 换成nginx的1111端口，做负债均衡**
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```
    
    IDEA启动9002服务：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004044.png)
    
    登录nacos：[http://192.168.187.150:1111/nacos/#/login](http://192.168.187.150:1111/nacos/#/login) 查看idea中nacos-payment-provider是否已经注册进我们的nacos中。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004045.png)
    

## 8.高可用小结：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082004046.png)