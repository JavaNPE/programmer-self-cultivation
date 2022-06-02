# 04_集群Eureka构建-eureka服务注册与发现

Eureka集群原理说明：

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201565.png)

<aside>
💡 问题:微服务RPC远程服务调用最核心的是什么？
</aside>
**高可用**，试想你的注册中心【Eureka Server】只有一个only one，它出故障 了那就呵呵C v~ )" 了，会导致整个为服务环境不可用。

<aside>
✏️ 什么是RPC

</aside>

RPC(Remote Procedure Call)：远程过程调用，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的思想。

RPC 是一种技术思想而非一种规范或协议，常见 RPC 技术和框架有：

- 应用级的服务框架：阿里的 Dubbo/Dubbox、Google gRPC、Spring Boot/Spring Cloud。
- 远程通信协议：RMI、Socket、SOAP(HTTP XML)、REST(HTTP JSON)。
- 通信框架：MINA 和 Netty。

<aside>
💡 解决办法：

</aside>

搭建**Eureka注册中心集群**，实现**负载均衡+故障容错**

# Eureka集群原理:

**互相注册，相互守望**

两台Eureka

![两台Eureka](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201567.png)

三台eureka

![三台eureka](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201568.png)

# EurekaServer集群环境构建步骤：



![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201569.png)

参考cloud-eureka-server7001我们新建一个cloud-eureka-server7002.

## 1、改7002的pom文件

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

    <artifactId>cloud-eureka-server7002</artifactId>
    <dependencies>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-se
       rver-->

        <dependency><!-- eureka-server -->
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

        <dependency> <!--引入自定义的api通用包，可以使用Payment支付Entities-->
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
        <dependency>  <!--监控-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->

        <!--以下为一般通用配置-->
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
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
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
</project>
```

## 2、修改映射配置：

### 2.1 找到C:\Windows\System32\drivers\etc路径下的hosts文件

[hosts](04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/hosts.txt)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201570.png)

### 2.2 修改映射配置添加进hosts文件

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201571.png)

添加以下部分内容：因为我们只是搭建两台eureka注册中心所以就先添加以下两条内容。

```xml
##################SpringCloud2020.1.2####################
127.0.0.1	eureka7001.com
127.0.0.1	eureka7002.com
```

## 3、新建application.yml文件

### 单机版7001注册中心的配置文件为：

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

参考这个图例进行思考：

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201572.png)

现在我们要配置7001和7002这两台Eureka注册中心，如果两台都叫`localhost`是不是就重名了，容易分不清！**Eureka搭建集群，要相互注册，相互守望**，那么是不是7001要注册7002,7002注册进去7001。

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201573.png)

### **集群版7001注册中心配置文件：**

但是如果使用了集群后，我们的eureka就需要相互注册了，也就是 7001的需要注册到7002, 而7002注册7001，同时 hostname也不能重复，需要有两个主机的ip

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201574.png)

### 7001的application.yml

```yaml
#集群版Eureka注册中心配置模板
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com    #eureka服务端的实例名字
  client:
    register-with-eureka: false    #表识不向注册中心注册自己
    fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7002.com:7002/eureka/ #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址 相互注册，相互守望！
```

### 7002的application.yml

```yaml
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名字
  client:
    register-with-eureka: false    #表识不向注册中心注册自己
    fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/     #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
```

## 4、7002的主启动：

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7002 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7002.class, args);
    }
}
```

## 5、浏览器进行测试

先按照原始的方法进行测试：

1、[http://localhost:7001/](http://localhost:7001/)

2、[http://localhost:7002/](http://localhost:7001/)

由于是多台Eureka注册中心，我们改过application.yml中的`hostname: eureka7001.com` #eureka服务端的实例名字和 **defaultZone:** `http://eureka7001.com:7001/eureka/`

现在通过这种方式进行

测试1：

[http://eureka7001.com:7001/](http://eureka7001.com:7001/)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201575.png)

测试2：

[http://eureka7002.com:7002/](http://eureka7002.com:7002/)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201576.png)

## 将支付服务8001微服务发布到上面2台（7001和7002）Eureka集群配置中

![8001](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201577.png)

8001

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为rue。
    register-with-eureka: true
    #是否从Eurekaserver抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka  #单机版   类比物业公司的地址
      **defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  #集群版**
```

## 将订单服务80微服务发布到上面2台（7001和7002）Eureka集群配置中

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201578.png)

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为rue。
    register-with-eureka: true
    #是否从Eurekaserver抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka  #单机版 类比物业公司的地址
      **defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  #集群版**
```

参考图例：

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201579.png)

### 测试：

- 1、先要启动EurekaServer，7001/7002服务
  
- 2、再要启动服务提供者provider，8001
- 3、再要启动消费者，80

测试地址：

[http://eureka7001.com:7001/](http://eureka7001.com:7001/)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201580.png)

测试地址：

[http://eureka7002.com:7002/](http://eureka7001.com:7001/)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201581.png)

我们通过80服务消费者进行消费测试：

[http://localhost/consumer/payment/get/2](http://localhost/consumer/payment/get/2)

返回数据库中的测试数据，消费成功。

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201582.png)

## 支付服务提供者8001集群环境构建

架构演变：

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201583.png)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201584.png)

### 1、参考 cloud-provider-payment8001新建cloud-provider-payment8002

- 1.1 写pom
  
    ```xml
    <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-server -->
            <dependency> <!--Eureka Client-->
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            
            <dependency>  <!--引入自定义的api通用包，可以使用Payment支付Entities-->
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
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
            </dependency>
            <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.10</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-jdbc -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
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
    ```
    
- 1.2 写YML-8002 参考8001注意把端口改了，这里需要注意的就是，为了保证这两个服务，对外暴露的都是同一个服务提供者，我们的服务名（`name: cloud-payment-service`）需要保持一致
  
    ```yaml
    server:
      port: 8002
    spring:
      application:
        name: cloud-payment-service
      datasource:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: org.gjt.mm.mysql.Driver
        url: jdbc:mysql://localhost:3306/cloud2020?useUnicode=true&characterEncoding=utf-8&useSSL=false
        username: root
        password: root
    
    eureka:
      client:
        #表示是否将自己注册进EurekaServer默认为rue。
        register-with-eureka: true
        #是否从Eurekaserver抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
        fetchRegistry: true
        service-url:
          #defaultZone: http://localhost:7001/eureka  #单机版   类比物业公司的地址
          defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  #集群版
    
    mybatis:
      mapperLocations: classpath:mapper/*.xml
      type-aliases-package: com.youliao.springcloud.entities
    ```
    
- 1.3 主启动
  
    参考哦8001，大致都一样的，可直接拷贝8001中的代码，但是要注意修改包引用，重新导包。
    
- 1.4 业务类：Controller_修改8001/8002的Controller
  
    8001_paymentController
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201585.png)
    
    8002_paymentController
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201586.png)
    
- 1.5 测试：
  
    测试1：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201587.png)
    
    测试2：[http://eureka7002.com:7002/](http://eureka7002.com:7002/)
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201588.png)
    
    测试3：[http://localhost/consumer/payment/get/8](http://localhost/consumer/payment/get/8)
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201589.png)
    
    测试4：[http://localhost:8002/payment/get/1](http://localhost:8002/payment/get/1)
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201590.png)
    
    测试5：[http://localhost/consumer/payment/get/11](http://localhost/consumer/payment/get/11) 我们通过80（服务消费者）端口随机访问获取数据时一直访问的是8001端口（服务提供者8001）
    
    ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201591.png)
    
    - bug:订单服务访问地址不能写死
      
        ```java
        //    public static final String PAYMENT_URL = "http://localhost:8001";
            public static final String PAYMENT_URL = "http://cloud-payment-service";
        ```
        
        现在我们对外暴漏的不再是服务器地址`http://localhost:8001` 我们现在对外暴漏的是`cloud-payment-service` 服务名称。
        
        ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201592.png)
        
    - 使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
      
        cloud-consumer-order80中的config配置中添加@LoadBalanced注解
        
        ```java
        @Configuration
        public classApplicationContextConfig {
            @Bean
            @LoadBalanced//使用@LoadBalanced注解 赋予了RestTemplate负载均衡的能力
        publicRestTemplate getRestTemplate() {
        return newRestTemplate();
            }
        }
        //  ApplicationContext.xml    <bean id="" class="">
        ```
        
        **测试：**[http://localhost/consumer/payment/get/11](http://localhost/consumer/payment/get/11)
        
        ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201594.png)
        
        ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201595.png)
        
        ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201596.png)
        
        ![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2030.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201597.png)
        

# actuator微服务信息完善

要做图形化的展示这块，这两个依赖都需要导入

```xml
			  <!--web启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--监控-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

## 主机名称:服务名称修改

改之前：含有localhost等显得不太合适，实际开发中显得有点low。在故障排错和调试过程中不具有差异性。

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201598.png)

我们需要分别在8001和8002中的：application.yml中添加 `instance:instance-id: payment8001` ，80服务消费者先不配置，测试进行观察。修改后，对外暴露的就是服务名称——payment8001/payment8002

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为rue。
    register-with-eureka: true
    #是否从Eureka Server抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true力能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka  #单机版   类比物业公司的地址
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  #集群版
  **instance:
    instance-id: payment8001**
```

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201599.png)

重启服务：

测试地址：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)

我们发现：CLOUD-PAYMENT-SERVICE中出现了payment8002,payment8001字样，而不是传统的localhost。。。。

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201600.png)

actuator：主要用于IP信息完善

actuator查看健康状态——健康检查

测试：[`http://192.168.0.106:8001/actuator/health`](http://192.168.0.106:8001/actuator/health)   我们的ip地址：192.168.9.106

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201601.png)

## 设置服务的Ip显示：

```yaml
instance:
    instance-id: payment8001
    prefer-ip-address: true     #访问路径显示IP地址
```

测试地址：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)

我们发现左下角开始显示ip地址+端口号

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201602.png)

# 服务发现Discovery

Eureka的新的注解标签 `@EnableDiscoveryClient`

对于注册进Eureka里面的微服务，可以通过服务发现来获得该服务的信息

```java
@Resource
private DiscoveryClient discoveryClient;

/**
     * 得到服务清单列表
     *
     * @return
     */
    @GetMapping(value = "/payment/discovery")
    public Object discovery() {
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("*******element:" + element);
        }
        //一个微服务下面的全部具体实例
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId() + "\t" + instance.getHost() + "\t" + instance.getPort() + "\t" + instance.getUri());
        }
        return this.discoveryClient;
    }
```

<aside>
❓ @EnableEurekaClient与@EnableDiscoveryClient的区别？

</aside>

答：共同点：都是能够让注册中心发现扫描到服务；

不同点：`@EnableEurekaClient` 只适用于Eureka作为注册中心

`@EnableDiscoveryClient` 可以是其他注册中心

针对8001微服务提供者的测试地址：[http://localhost:8001/payment/discovery](http://localhost:8001/payment/discovery)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2036.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201603.png)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201604.png)

# Eureka的自我保护相关知识点

## 1、故障现象：

### 概述：

保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式,
Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据,也就是不会注销任何微服务。

如果在Eureka Server的首页看到以下这段提示，则说明Eureka进入了保护模式:

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2038.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201605.png)

## 2、🎈导致原因：

**一句话: 某时刻某一个微服务不可用了 ，Eureka不会立刻清理,依旧会对该微服务的信息进行保存——[属于CAP里面的AP分支](https://www.notion.so/02_-CAP-Eureka-CAP-AP-e9dc2a8fa95f4359a542bb7d0632a822)**

默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例，默认90秒。但是当网络分区故障发生（延时，卡顿，拥挤）时，微服务与EurekaServer之间无法正常通信，以上行为可能变得非常危险了- 因为微服务本身其实是健康的，此时不应该注销这个微服务，Eureka通过 自我保护模式 来解决这个问题，当EurekaServer节点在短时间丢失过多客户端，那么这个节点就会进入自我保护模式。

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2039.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201606.png)

这是一种高可用的机制

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2040.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201607.png)

在自我保护模式下，Eureka Server会保护服务注册表中的信息，不在注销任何服务实例

它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例

综上，自我保护模式是一种应对网络异常的安全保护措施，它的架构哲学是宁可保留所有微服务（健康的微服务和不健康的微服务都会保留）也不盲目注销任何健康的微服务。使用自我保护模式，可以让Eureka集群更加健壮，稳定。

## 3、怎么禁止Eureka的自我保护：(一般生产环境中不会禁止自我保护)

Eureka默认开启自我保护

```yaml
eureka:
  server:
    enable-self-preservation: true
    peer-node-read-timeout-ms: 3000
    peer-node-connect-timeout-ms: 3000
```

使用`eureka.server. enable-self-preservation: false`可以禁用自我保护模式

```yaml
server:
  enable-self-preservation: false  #关闭Eureka的自我保护机制，保证不可用服务被及时剔除
  eviction-interval-timer-in-ms: 2000  #Eureka默认90秒，我们改成2秒
```

关闭eureka的自我保护机制的效果：

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2041.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201608.png)

同时在客户端进行设置：

```yaml
eureka:
  instance:
    # Eureka客户端向服务端发送心跳的时间间隔，单位为秒，默认30
    lease-renewal-interval-in-seconds: 1 #现在是1秒
    # Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒，默认为90秒，超时将剔除服务
    lease-expiration-duration-in-seconds: 2 #现在是2秒
```

1、7001和8001都配置完成
2、先启动7001再启动8001
3、先关闭8001发现之前注册的payment8001马上被删除了

测试地址：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)

![04_%E9%9B%86%E7%BE%A4Eureka%E6%9E%84%E5%BB%BA%E6%AD%A5%E9%AA%A4%EF%BC%88eureka%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0%EF%BC%89%202c4d43db700341d1bdfe4bb8c62cd7e3/Untitled%2042.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022201609.png)