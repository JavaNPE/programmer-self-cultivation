# 17_SpringCloud Alibaba Sentinel实现熔断与限流

# 一、Sentinel概述：

## 1、Sentinel官网：[https://github.com/alibaba/Sentinel](https://github.com/alibaba/Sentinel)

> Sentinel官网
> 

[GitHub - alibaba/Sentinel: A powerful flow control component enabling reliability, resilience and monitoring for microservices. (面向云原生微服务的高可用流控防护组件)](https://github.com/alibaba/Sentinel)

## 2、Sentinel中文官网

[介绍 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E4%BB%8B%E7%BB%8D)

## 3、Sentinel是什么：

一句话解释，之前我们讲解过的Hystrix。

**Hystrix存在的问题：**

- 需要我们程序员自己手工搭建监控平台。
- 没有一套web界面可以给我们进行更加细粒度化的配置，流量控制，速率控制，服务熔断，服务降级。

**这个时候Sentinel运营而生：**

- 单独一个组件，可以独立出来
- 直接界面化的细粒度统一配置

**Sentinel的初衷：**约定 > 配置 >编码，都可以写在代码里，但是尽量使用注解和配置代替编码，让程序员少些代码。

> Sentinel是什么：
> 

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

Sentinel 具有以下特征:

- **丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
- **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
- **完善的 SPI 扩展点**：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014412.png)

## 4、Sentinel去哪下：[https://github.com/alibaba/Sentinel/releases/tag/1.7.0](https://github.com/alibaba/Sentinel/releases/tag/1.7.0)

[Releases · alibaba/Sentinel](https://github.com/alibaba/Sentinel/releases)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014414.png)

# 5、Sentinel能干嘛&主要特性

1. 服务雪崩
2. 服务降级
3. 服务熔断
4. 服务限流

![参考Sentinel中文官网](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014415.png)

参考Sentinel中文官网

## 6、Sentinel 的开源生态：

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014416.png)

## 7、Spring Cloud Alibaba Sentinel怎么玩

> 怎么玩
> 

[Spring Cloud Alibaba Reference Documentation](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_sentinel)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014417.png)

> 服务使用中的各种问题：
> 
1. 服务雪崩
2. 服务降级
3. 服务熔断
4. 服务限流

# 二、安装Sentinel控制台

## 1、sentinel组件由2部分组成

sentinel组件由两部分组成，后台和前台8080

Sentinel 分为两个部分：

- 核心库（Java 客户端）不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
- 控制台（Dashboard）基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。

## 2、Sentinel安装步骤：

### 2.1 Sentinel下载地址 ：下载到本地sentinel-dashboard-1.7.0.jar版本自选

[Releases · alibaba/Sentinel](https://github.com/alibaba/Sentinel/releases)

最新版本Sentinel下载地址。

[Release v1.7.1 · alibaba/Sentinel](https://github.com/alibaba/Sentinel/releases/tag/1.7.1)

指定1.7.1版本Sentinel。

[Release v1.7.0 · alibaba/Sentinel](https://github.com/alibaba/Sentinel/releases/tag/1.7.0)

指定1.7.0版本Sentinel。

- 下载以下文件：本教程以1.7.0版本讲解
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014418.png)
    

### 2.2 启动sentinel命令：

> 前提：确保java8环境🆗，**[8080端口不能被占用](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)**，因为Sentinel默认端口为8080。
> 
- 通过`java -version` 命令查看是否为Java8环境
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014419.png)
    

> **启动sentinel命令**：`**java -jar** sentinel-dashboard-1.7.0.jar`
> 

本地sentinel-dashboard-1.7.0.jar文件地址：D:\develop\Utils\sentinel-dashboard-1.7.0

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014420.png)

启动`sentinel` 

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014421.png)

发现8080端口被占用了，那么我们就需要处理以下被占用的8080端口。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014422.png)

### 2.3 8080端口被占用的解决办法：【结论：使用Sentinel的时候不要用微信，可以给微信改端口本文不做讨论】

- 8080端口被占用的解决办法通过命令查找某一特定端口，在命令窗口中输入命令中输入netstat -ano |findstr "端口号"，然后回车就可以看到这个端口被哪个应用占用。
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014423.png)
    
    输入命令，输入`netstat -ano`然后回车，就可以看到系统当前所有的端口使用情况。
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014424.png)
    
    这种方法比较笨，往下拉可以看到我们的8080端口的一个PID信息（可能不全）。
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014425.png)
    
    另一种方法就是通过命令**查找某一特定端口**，在命令窗口中输入命令中输入`netstat -ano |findstr "端口号"`，然后回车就可以看到这个端口被哪个应用占用。
    
    执行命令： `netstat -ano |findstr "8080"`
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014426.png)
    
    查看到对应的进程id（PID）之后，就可以通过id查找对应的进程名称，使用命令`tasklist |findstr "进程id号"`
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014427.png)
    
    通过命令杀掉进程，或者是直接根据进程的名称杀掉所有的进程，，在命令框中输入如下命令`taskkill /f /t /im "进程id或者进程名称"`
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014428.png)
    
    然后记得重启sentinel服务
    

### 3、访问sentinel管理界面：账号密码均为：**sentinel**

> 访问地址：[http://localhost:8080](http://localhost:8080/)
> 

> 登录账号密码均为：**sentinel**
> 

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014429.png)

安装并启动sentinel成功

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014430.png)

# 三、初始化演示工程

## 1、启动Nacos8848成功

> Windows启动nacos：
> 
- Windows本地启动nacos
  
    本地地址：`D:\develop\Utils\nacos-server-1.1.4\nacos\bin`
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014431.png)
    
    发现启动报错：由于之前配置nacos持久化将nacos自带的derby数据库切换到MySQL了导致的。
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014432.png)
    
    是因为我们之前配置过Nacos持久化，然后持久化必须完成derby到mysql的切换所导致的，那么我们启动我们的本地MySQL数据库。
    
    > 转链：
    > 
    
    ## 要想Nacos持久化必须完成derby到mysql的切换，附配置步骤：
    

> 登录地址：[http://localhost:8848/nacos/#/login](http://localhost:8848/nacos/#/login)    用户名和密码均为：nacos
> 

## 2、新建Module：cloudalibaba-sentinel-service8401、启动Sentinel、[启动nacos](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)

- 1、新建module：cloudalibaba-sentinel-service8401
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
    
        <artifactId>cloudalibaba-sentinel-service8401</artifactId>
    
        <dependencies>
            <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <dependency>
                <groupId>com.alibaba.csp</groupId>
                <artifactId>sentinel-datasource-nacos</artifactId>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
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
                <groupId>cn.hutool</groupId>
                <artifactId>hutool-all</artifactId>
                <version>4.6.3</version>
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
      port: 8401
    
    spring:
      application:
        name: cloudalibaba-sentinel-service
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            dashboard: localhost:8080
            port: 8719  #默认8719，假如被占用了会自动从8719开始依次+1扫描。直至找到未被占用的端口
    
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
     * @Date 2021/7/28 19:26
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    public class MainApp8401 {
        public static void main(String[] args) {
            SpringApplication.run(MainApp8401.class, args);
        }
    }
    ```
    
- 5、业务类FlowLimitController
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/7/28 19:27
     * @Version 1.0
     * @Description
     */
    
    @RestController
    public class FlowLimitController {
        @GetMapping("/testA")
        public String testA() {
            return "------testA";
        }
    
        @GetMapping("/testB")
        public String testB() {
    
            return "------testB";
        }
    }
    ```
    
- 6、启动微服务8401
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014433.png)
    
- 7、启动Sentinel8080：访问地址：[http://localhost:8080](http://localhost:8080/)
  
    ```bash
    D:\develop\Utils\sentinel-dashboard-1.7.0>**java -jar sentinel-dashboard-1.7.0.jar**
    ```
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014434.png)
    
- [8、启动nacos](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)
- 9、启动8401微服务后查看sentienl控制台：空空如也，啥都没有。。
  
    我们会发现Sentinel里面空空如也，什么也没有，这是因为Sentinel采用的懒加载，执行一次访问即可：
    
    链接1：[http://localhost:8401/testA](http://localhost:8401/testA)
    
    链接2：[http://localhost:8401/testB](http://localhost:8401/testB)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014435.png)
    
    访问Sentinel地址：[http://localhost:8080](http://localhost:8080/)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014436.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014437.png)
    
    结论：sentinel8080正在监控微服务8401。
    

# 四、流控规则

## 1、流控规则的基本介绍：

[流量控制 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014438.png)

**字段说明**

- 资源名：唯一名称，默认请求路径
- 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
- 阈值类型 / 单机阈值
    - QPS：（每秒钟的请求数量）：但调用该API的QPS达到阈值的时候，进行限流
    - 线程数：当调用该API的线程数达到阈值的时候，进行限流
- 是否集群：不需要集群
- 流控模式
    - 直接：api都达到限流条件时，直接限流
    - 关联：当关联的资源达到阈值，就限流自己
    - 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）【API级别的针对来源】
- 流控效果
    - [快速失败：直接失败，抛异常](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)
    - [Warm UP：](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)根据codeFactory（冷加载因子，默认3），从阈值/CodeFactor，经过预热时长，才达到设置的QPS阈值
    - [排队等待：](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)匀速排队，让请求以匀速的速度通过，阈值类型必须设置QPS，否则无效

## 2、流控模式：1.直接、2.关联、3.链路

### [2.1 流控模式直接（系统默认）：包含QPS和线程数](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)

> **阈值类型QPS：**
> 

我们给testA增加流控

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014439.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014440.png)

[](http://localhost:8080/)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014441.png)

然后我们请求 [http://localhost:8401/testA](http://localhost:8401/testA%EF%BC%8C%E5%B0%B1%E4%BC%9A%E5%87%BA%E7%8E%B0%E5%A4%B1%E8%B4%A5%EF%BC%8C%E8%A2%AB%E9%99%90%E6%B5%81%EF%BC%8C%E5%BF%AB%E9%80%9F%E5%A4%B1%E8%B4%A5)  如果1秒中刷新多次，就会出现直接马上快速失败，被限流，快速失败等情况。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014442.png)

> 思考：
> 

直接调用的是默认报错信息，能否有我们的后续处理，比如更加友好的提示，类似有hystrix的fallback方法。

> **阈值类型线程数：**
> 

这里的线程数表示一次只有一个线程进行业务请求，当前出现请求无法响应的时候，会直接报错，例如，在方法的内部增加一个睡眠，那么后面来的就会失败

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2030.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014443.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014445.png)

在我 们修改阈值类型为“线程数”之后，我们在浏览器中访问[http://localhost:8401/testA](http://localhost:8401/testA) 时一秒钟刷新多次并没有出现QPS那种直接报错的情况（Blocked by Sentinel (flow limiting)）。

[https://www.notion.so](https://www.notion.so)

[https://www.notion.so](https://www.notion.so)

- 业务类中添加睡眠代码
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.concurrent.TimeUnit;
    
    /**
     * @Author Dali
     * @Date 2021/7/28 19:27
     * @Version 1.0
     * @Description
     */
    
    @RestController
    public class FlowLimitController {
        @GetMapping("/testA")
        public String testA() {
            **try { //暂停0.8毫秒
                TimeUnit.MILLISECONDS.sleep(800);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }**
            return "------testA";
        }
    
        @GetMapping("/testB")
        public String testB() {
            return "------testB";
        }
    }
    ```
    

[](http://localhost:8401/testA)

测试：间隔0.8秒刷新一次正常显示-----testA内容，0.8秒多次刷新。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_07_29_11_02_36_526.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014446.gif)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_07_29_10_59_39_612.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014447.gif)

### [2.2 流控模式：关联](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)

> **流控模式之关联是什么意思：**
> 
- 当关联的资源达到阈值时，就限流自己
- 当与A关联的资源B达到阈值后，就限流自己
- B惹事，A挂了

**场景：**支付接口达到阈值后，就限流下订单的接口

> **设置：**
> 

当关联资源 /testB的QPS达到阈值超过1时，就限流/testA的Rest访问地址，当关联资源达到阈值后，限制配置好的资源名

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014448.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014449.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014450.png)

> 用**postman模拟并发密集访问testB**
> 
- **postman模拟并发密集访问testB**
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014451.png)
    
    1、访问testB成功
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2036.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014452.png)
    
    2、postman里新建多线程集合组
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014453.png)
    
    3、将访问地址添加进新线程组
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2038.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014454.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2039.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014455.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2040.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014456.png)
    
    4、Run，大批量线程高并发访问B，导致A失效了
    
    [https://www.notion.so](https://www.notion.so)
    
    5、运行后发现testA挂了，点击访问http://localhost:8401/testA
    
    结果：Blocked by Sentinel (flow limiting)
    

### 2.3 流控模式：链路，家庭作业

多个请求调用了同一个微服务。

## 3、流控效果

### [3.1 流控效果：直接->快速失败（默认的流控处理）](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)

快速失败，默认的流控处理

- 直接失败，抛出异常：Blocked by Sentinel（Flow limiting）

> 源码：
> 

com.alibaba.csp.sentinel.slots.block.flow.controller.**DefaultController**

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2041.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014457.png)

### [3.2 流控效果：预热](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d) **Warm Up**

系统最怕的就是出现，平时访问是0，然后突然一瞬间来了10W的QPS

> **公式：**阈值 除以 clodFactor（默认值为3），经过预热时长后，才会达到阈值
> 

**Warm Up方式，即预热/冷启动方式**，当系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能会瞬间把系统压垮。通过冷启动，让通过的流量缓慢增加，在一定时间内逐渐增加到阈值，给冷系统一个预热的时间，避免冷系统被压垮。通常冷启动的过程系统允许的QPS曲线如下图所示

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2042.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014458.png)

**默认clodFactor为3**，即请求QPS从threshold / 3开始，经预热时长逐渐提升至设定的QPS阈值。

> 限流 冷启动：
> 

[限流 冷启动 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E9%99%90%E6%B5%81---%E5%86%B7%E5%90%AF%E5%8A%A8)

> 源码：
> 

com.alibaba.csp.sentinel.slots.block.flow.controller.**WarmUpController**

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2043.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014459.png)

> Warmup配置：
> 

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2044.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014460.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2045.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014461.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2046.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014462.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2047.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014463.png)

假设这个系统的QPS是10，那么最开始系统能够接受的 QPS = 10 / 3 = 3，然后从3逐渐在5秒内提升到10。

**启动**IDEA本地服务：MainApp8401，启动nacos，启动Sentinel服务。

> **测试：**多次点击http://localhost:8401/testB，刚开始不行，后续慢慢OK
> 

**测试结果：**正常一秒一次可，若是1秒多次（超3次），则不可以，但是我们通过Warm Up配置之后逐渐在5秒内就又可以正常访问了。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_07_29_14_35_24_23.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014464.gif)

> 应用场景：秒杀
> 

秒杀系统在开启的瞬间，会有很多流量上来，很可能把系统打死，预热的方式就是为了保护系统，可能慢慢的把流量放进来，慢慢的把阈值增长到设置的阈值。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2048.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014465.png)

### [3.3 流控效果：排队等待](https://www.notion.so/17_SpringCloud-Alibaba-Sentinel-84f871664193468395df13d84ba6668d)：匀速排队，阈值必须设置为QPS

大家均速排队，让请求以均匀的速度通过，阈值类型必须设置成QPS，否则无效

**均速排队方式必须严格控制请求通过的间隔时间**，也即让请求以匀速的速度通过，**对应的是漏桶算法。**

> 官网：
> 

[流量控制 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6#%E5%8C%80%E9%80%9F%E6%8E%92%E9%98%9F)

> 源码：com.alibaba.csp.sentinel.slots.block.flow.controller.RateLimiterController
> 

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2049.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014466.png)

**注意：匀速排队模式暂时不支持 QPS > 1000 的场景。**

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2050.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014467.png)

这种方式主要用于处理间隔性突发的流量，例如消息队列，想象一下这样的场景，在某一秒有大量的请求到来，而接下来的几秒处于空闲状态，我们系统系统能够接下来的空闲期间逐渐处理这些请求，而不是在第一秒直接拒绝多余的请求。

**设置含义：/testA 每秒1次请求，超过的话，就排队等待，等待时间超过20000毫秒**

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2051.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014468.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2052.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014469.png)

- 在postman中进行测试：[http://localhost:8401/testB](http://localhost:8401/testB)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2053.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014470.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2054.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014471.png)
    
    查看idea控制台输出的信息。
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2055.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014472.png)
    

# 五、降级规则

> 官网：[https://github.com/alibaba/Sentinel/wiki/熔断降级](https://github.com/alibaba/Sentinel/wiki/%E7%86%94%E6%96%AD%E9%99%8D%E7%BA%A7)
> 

## 5.1 降级规则名词基本介绍

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2056.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014473.png)

- RT（平均响应时间，秒级）
    - 平均响应时间，超过阈值 且 时间窗口内通过的请求 >= 5，两个条件同时满足后出发降级
    - 窗口期过后，关闭断路器
    - RT最大4900（更大的需要通过 -Dcsp.sentinel.staticstic.max.rt=XXXXX才能生效）
- 异常比例（秒级）
    - QPA >= 5 且异常比例（秒级）超过阈值时，触发降级；时间窗口结束后，关闭降级
- 异常数（分钟级）
    - 异常数（分钟统计）超过阈值时，触发降级，时间窗口结束后，关闭降级

> 进一步说明：
> 

Sentinel熔断降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常异常比例升高），对这个资源的调用进行限制，让请求快速失败，避免影响到其它的资源而导致级联错误。

当资源被降级后，在接下来的降级时间窗口之内，对该资源的调用都进行自动熔断（默认行为是抛出DegradeException）

> Sentinel的断路器是没有半开状态的。
> 

半开的状态，系统自动去检测是否请求有异常，没有异常就关闭断路器恢复使用，有异常则继续打开断路器不可用，具体可以参考hystrix：

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2057.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014474.png)

## 5.2降级策略实战：

### 5.2.1 RT

> **RT是什么：**
> 

平均响应时间 (DEGRADE_GRADE_RT)：当 1s 内持续进入 N 个请求，对应时刻的平均响应时间（秒级）均超过阈值（count，以 ms 为单位），那么在接下的时间窗口（DegradeRule 中的timeWindow，以 s 为单位）之内，对这个方法的调用都会自动地熔断（抛出 DegradeException）。注意 Sentinel 默认统计的 RT 上限是 4900 ms，超出此阈值的都会算作 4900 ms，若需要变更此上限可以通过启动配置项 -Dcsp.sentinel.statistic.max.rt=xxx 来配置。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2058.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014475.png)

> **RT测试：**
> 

### 2.1 Jmeter压测测试（附：Jmeter的使用教程）

- RT测试：
    - FlowLimitController类中添加以下代码：
      
        ```java
        
        /**
             * 降级策略实战:RT
             *
             * @return
             */
            @GetMapping("/testD")
            public String testD() {
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.info("testD 测试RT");
        
                return "------testD";
            }
        ```
        
    - Sentinel配置：[http://localhost:8080/#/login](http://localhost:8080/#/login)
      
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2059.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014476.png)
        
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2060.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014477.png)
        
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2061.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014478.png)
        
    - jmeter压测：
      
        ![1秒10个线程](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014479.png)
        
        1秒10个线程
        
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2063.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014480.png)
        
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2064.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014481.png)
        
        按照上述操作，永远1秒种打进来10个线程，大于5个了，调用tesetD，我们希望200毫秒内处理完本次任务，如果200毫秒没有处理完，在未来的1秒的时间窗口内，断路器打开（保险丝跳闸）微服务不可用，保险丝跳闸断电。
        
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2065.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014482.png)
        
        后续我们**停止使用jmeter**，没有那么大的访问量了，断路器关闭（保险丝恢复），微服务恢复OK
        

### 5.2.2 异常比例

异常比例 (DEGRADE_GRADE_EXCEPTION_RATIO)：当资源的每秒请求量 >= 5（可配置），并且每秒异常总数占通过量的比值超过阈值（DegradeRule 中的 count）之后，资源进入降级状态，即在接下的时间窗口（DegradeRule 中的 timeWindow，以 s 为单位）之内，对这个方法的调用都会自动地返回。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2066.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014483.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2067.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014484.png)

单独访问一次，必然来一次报错一次，开启jmeter后，直接高并发发送请求，多次调用达到我们的配置条件了，断路器开启（保险丝跳闸），微服务不可用，不在报错，而是服务降级了

> 异常比例测试：
> 
- 异常比例测试：
    - FlowLimitController类中修改以下代码：
      
        ```java
        /**
             * 降级策略实战：异常比例
             *
             * @return
             */
            @GetMapping("/testD")
            public String testD() {
                log.info("testD 测试异常比例");
                int age = 10 / 0;
                return "------testD";
            }
        ```
        
    - Sentinel配置：[http://localhost:8080/#/login](http://localhost:8080/#/login)
      
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2068.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014485.png)
        
        设置3秒内，如果请求百分50%出错，那么就会熔断
        
    - jmeter压测：
      
        ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2069.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014486.png)
        
        - [http://localhost:8401/testD](http://localhost:8401/testD)
          
            我们用jmeter每秒发送10次请求，3秒后，再次调用 localhost:8401/testD 出现服务降级.
            
            ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2070.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014487.png)
            
            我们把jmeter停止再次刷新浏览器，浏览器直接弹出报错信息。
            
            ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2071.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014488.png)
            
        - 异常比例测试结论：
          
            ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2072.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014489.png)
            

### 5.2.3 异常数

异常数 (`DEGRADE_GRADE_EXCEPTION_COUNT`)：当资源近 1 分钟的异常数目超过阈值之后会进行熔断。注意由于统计时间窗口是分钟级别的，若 `timeWindow` 小于 60s，则结束熔断状态后仍可能再进入熔断状态

时间窗口一定要大于等于60秒。

异常数是按分钟来统计的

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2073.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014490.png)

> 异常数测试
> 
- FlowLimitController类中添加以下代码：
  
    ```java
    @GetMapping("/testE")
        public String testE() {
            log.info("testE 测试异常数");
            int age = 10 / 0;
            return "------testE 测试异常数";
        }
    ```
    
- Sentinel配置：[http://localhost:8080/#/login](http://localhost:8080/#/login)
  
    启动MainApp8401微服务：浏览器测试：[http://localhost:8401/testE](http://localhost:8401/testE)
    
    下面设置是，一分钟内出现5次，则熔断。
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2074.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014491.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2075.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014492.png)
    
    [http://localhost:8401/testE](http://localhost:8401/testE)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_07_29_17_42_20_45.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014493.gif)
    
- jmeter：参考异常比例
  
    

# 六、热点key限流

## 6.1 热点key限流基本介绍：

sentinel地址[http://localhost:8080/](http://localhost:8080/)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2076.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014494.png)

> 热点key限流官网：
> 

[热点参数限流 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E7%83%AD%E7%82%B9%E5%8F%82%E6%95%B0%E9%99%90%E6%B5%81)

## 6.2**什么是热点数据**

[Github文档传送门](https://github.com/alibaba/Sentinel/wiki/%E7%83%AD%E7%82%B9%E5%8F%82%E6%95%B0%E9%99%90%E6%B5%81)

何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制。比如：

- 商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
- 用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2077.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014495.png)

Sentinel 利用 LRU 策略统计最近最常访问的热点参数，结合令牌桶算法来进行参数级别的流控。热点参数限流支持集群模式。

## 6.3**兜底的方法**

分为系统默认的和客户自定义的，两种，之前的case中，限流出现问题了，都用sentinel系统默认的提示：Blocked By Sentinel，我们能不能自定义，类似于hystrix，某个方法出现问题了，就找到对应的兜底降级方法。

从 `@HystrixCommand` 到 `@SentinelResource`

> 源码：com.alibaba.csp.sentinel.slots.block.BlockException
> 

> 代码：启动MainApp8401，启动sentinel服务，
> 
- 代码
  
    ```java
    @GetMapping("/testHotKey")
        @SentinelResource(value = "testHotKey", blockHandler = "deal_testHotKey")
        public String testHotKey(@RequestParam(value = "p1", required = false) String p1,
                                 @RequestParam(value = "p2", required = false) String p2) {
            //int age = 10/0;
            return "------testHotKey";
        }
    
        //兜底方法：
        public String deal_testHotKey(String p1, String p2, BlockException exception) {
            return "------deal_testHotKey,o(╥﹏╥)o"; //sentinel.系统默认的提示: Blocked by Sentinel (fLow limiting)
    
        }
    ```
    
- 测试1：[http://localhost:8401/testHotKey](http://localhost:8401/testHotKey)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2078.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014496.png)
    
- 测试2：[http://localhost:8401/testHotKey?p1=a&p2=b](http://localhost:8401/testHotKey?p1=a&p2=b)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2079.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014498.png)
    

## 6.4 热点key限流配置：

我们对参数0，设置热点key进行限流

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2080.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014499.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2081.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014500.png)

配置完成后

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2082.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014501.png)

当我们不断的请求时候，也就是以第一个参数为目标，请求接口，我们会发现多次请求后

[http://localhost:8401/testHotKey?p1=a](http://localhost:8401/testHotKey?p1=a)，就会出现以下的兜底错误：-----deal_testHotKey,o(╥﹏╥)o

这是因为我们针对第一个参数进行了限制，当我们QPS超过1的时候，就会触发兜底的错误。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_08_01_15_46_53_796.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014502.gif)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2083.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014503.png)

假设我们请求的接口是：[http://localhost:8401/testHotKey?p2=a](http://localhost:8401/testHotKey?p2=a) ，我们会发现他就没有进行限流.

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2084.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014504.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2085.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014505.png)

## 6.5**参数例外项**

上述案例演示了第一个参数p1，当QPS超过1秒1次点击狗，马上被限流

- 普通：超过一秒1个后，达到阈值1后马上被限流
- 我们期望p1参数当它达到某个特殊值时，它的限流值和平时不一样
- 特例：假设当p1的值等于5时，它的阈值可以达到200
- 一句话说：当key为特殊值的时候，不被限制

平时的时候，参数1的QPS是1，超过的时候被限流，但是有特殊值，比如5，那么它的阈值就是200。

我们通过 `http://localhost:8401/testHotKey?p1=5` 一直刷新，发现不会触发兜底的方法，这就是参数例外项。

热点参数的注意点，参数必须是基本类型或者String

![添加按钮不能忘](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014506.png)

添加按钮不能忘

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2087.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014507.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2088.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014508.png)

## 6.6 总结

`@SentinelResource` 处理的是Sentinel控制台配置的违规情况，有blockHandler方法配置的兜底处理

RuntimeException，如 int a = 10/0 ; 这个是java运行时抛出的异常，RuntimeException，@RentinelResource不管

也就是说：`@SentinelResource` 主管配置出错，运行出错不管。

如果想要有配置出错，和运行出错的话，那么可以设置 fallback

```java
    @GetMapping("/testHotKey")
    @SentinelResource(value = "testHotKey",blockHandler = "deal_testHotKey", fallback = "fallBack")
    public String testHotKey(@RequestParam(value = "p1",required = false) String p1,
                             @RequestParam(value = "p2",required = false) String p2)
    {
        //int age = 10/0;
        return "------testHotKey";
    }
```

# 七、Sentinel系统规则

> 官网：
> 

[系统自适应限流 · alibaba/Sentinel Wiki](https://github.com/alibaba/Sentinel/wiki/%E7%B3%BB%E7%BB%9F%E8%87%AA%E9%80%82%E5%BA%94%E9%99%90%E6%B5%81)

Sentinel 系统自适应限流**从整体维度**对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是从应用级别的入口流量进行控制，从单台机器的 load、CPU 使用率、平均 RT、入口 QPS 和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是应用整体维度的，而不是资源维度的，并且**仅对入口流量生效**。入口流量指的是进入应用的流量（`EntryType.IN`），比如 Web 服务或 Dubbo 服务端接收的请求，都属于入口流量。

系统规则支持以下的模式：

- **Load 自适应**（仅对 Linux/Unix-like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 `maxQps * minRt` 估算得出。设定参考值一般是 `CPU cores * 2.5`。
- **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。
- **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
- **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
- **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

![这样相当于设置了全局的QPS过滤](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014509.png)

这样相当于设置了全局的QPS过滤

# 八、@SentinelResource注解

> 前提：启动Nacos成功（因为换过Nacos的数据库，所以需要启动MySQL），启动Sentinel成功
> 

启动Nacos：[http://localhost:8848/nacos/#/login](http://localhost:8848/nacos/#/login)

启动Sentinel：[http://localhost:8080/](http://localhost:8080/)

- 按资源名称限流 + 后续处理
- 按URL地址限流 + 后续处理

## 8.1 按资源名称限流+后续处理

- 修改cloudalibaba-sentinel-service8401
- POM
  
    ```xml
    <dependency>
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
    ```
    
- YML没变
  
    ```yaml
    server:
      port: 8401
    
    spring:
      application:
        name: cloudalibaba-sentinel-service
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            dashboard: localhost:8080
            port: 8719  #默认8719，假如被占用了会自动从8719开始依次+1扫描。直至找到未被占用的端口
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```
    
- 业务类RateLimitController：新值
  
    ```java
    package com.youliao.springcloud.controller;
    
    import com.alibaba.csp.sentinel.annotation.SentinelResource;
    import com.alibaba.csp.sentinel.slots.block.BlockException;
    import com.youliao.springcloud.entities.CommonResult;
    import com.youliao.springcloud.entities.Payment;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/8/1 23:05
     * @Version 1.0
     * @Description
     */
    @RestController
    public class RateLimitController {
        @GetMapping("/byResource")
        @SentinelResource(value = "byResource", blockHandler = "handleException")
        public CommonResult byResource() {
            return new CommonResult(200, "按资源名称限流测试OK", new Payment(2020L, "serial001"));
        }
    
        public CommonResult handleException(BlockException exception) {
            return new CommonResult(444, exception.getClass().getCanonicalName() + "\t 服务不可用");
        }
    }
    ```
    
- 主启动
- 测试：IDEA启动MainApp8401服务，正常访问：[http://localhost:8401/byResource](http://localhost:8401/byResource)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2090.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014510.png)
    

### 8.1.1 配置流控规则

> 配置流控规则：
> 

配置步骤

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2091.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014511.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2092.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014512.png)

浏览器测试结果：[http://localhost:8401/byResource](http://localhost:8401/byResource)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_08_02_11_09_15_928.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014513.gif)

1秒钟点击1下，OK,超过上述问题，疯狂点击，返回了自己定义的限流处理信息，限流发送。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2093.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014514.png)

图形配置和代码关系，表示1秒钟内查询次数大于1，就跑到我们自定义的处流，限流。

## **问题**

- 系统默认的，没有体现我们自己的业务要求
- 依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观
- 每个业务方法都添加一个兜底方法，那代码膨胀加剧
- 全局统一的处理方法没有体现
- 关闭8401，发现流控规则已经消失，说明这个是没有持久化

### **客户自定义限流处理逻辑**

创建CustomerBlockHandler类用于自定义限流处理逻辑

## 8.2 按照Url地址限流+后续处理

通过访问的URL来限流，会返回Sentinel自带默认的限流处理信息。

- 业务类RateLimitController
  
    ```java
    @GetMapping("/rateLimit/byUrl")
        @SentinelResource(value = "byUrl")
        public CommonResult byUrl() {
            return new CommonResult(200, "按url限流测试OK", new Payment(2020L, "serial002"));
        }
    ```
    
- 访问一次：[http://localhost:8401/rateLimit/byUrl](http://localhost:8401/rateLimit/byUrl)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2094.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014516.png)
    
- Sentinel控制台配置
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2095.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014517.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2096.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014518.png)
    
- 再次访问：[http://localhost:8401/rateLimit/byUrl](http://localhost:8401/rateLimit/byUrl)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_08_02_11_21_35_49.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014519.gif)
    
    疯狂点击http://localhost:8401/rateLimit/byUrl，会返回Sentinel自带的限流处理结果。
    

### 上面兜底方法面临的问题：

- 系统默认的，没有体现我们自己的业务要求
- 依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观
- 每个业务方法都添加一个兜底方法，那代码膨胀加剧
- 全局统一的处理方法没有体现
- 关闭8401，发现流控规则已经消失，说明这个是没有持久化

## 8.3 客户自定义限流处理逻辑

- 创建customerBlockHandler类用于自定义限流处理逻辑
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2097.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014520.png)
    
- 自定义限流处理，CustomerBlockHandler
  
    ```java
    package com.youliao.springcloud.myhandler;
    
    import com.alibaba.csp.sentinel.slots.block.BlockException;
    import com.youliao.springcloud.entities.CommonResult;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 11:38
     * @Version 1.0
     * @Description
     */
    
    public class CustomerBlockHandler {
        public static CommonResult handlerException(BlockException exception) {
            return new CommonResult(4444, "按客戶自定义,global handlerException----1");
        }
    
        public static CommonResult handlerException2(BlockException exception) {
            return new CommonResult(4444, "按客戶自定义,global handlerException----2");
        }
    }
    ```
    
- RateLimitController：package com.youliao.springcloud.controller;
  
    ```java
    @GetMapping("/rateLimit/customerBlockHandler")
    @SentinelResource(value ="customerBlockHandler",
            blockHandlerClass = CustomerBlockHandler.class,
            blockHandler ="handlerException2")
    publicCommonResult customerBlockHandler() {
    return newCommonResult(200,"按客戶自定义",newPayment(2020L,"serial003"));
    }
    ```
    
- 启动微服务后先调用一次：[http://localhost:8401/rateLimit/customerBlockHandler](http://localhost:8401/rateLimit/customerBlockHandler)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2098.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014521.png)
    
- Sentinel控制台配置
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%2099.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014522.png)
    
- 测试后我们自定义的出来了：[http://localhost:8401/rateLimit/customerBlockHandler](http://localhost:8401/rateLimit/customerBlockHandler)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_08_02_11_51_09_2.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014523.gif)
    
- 进一步说明
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20100.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014524.png)
    

### 更多注解属性说明

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20101.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014525.png)

所有的代码都要用try - catch - finally 进行处理

### sentinel主要有三个核心API

- Sphu定义资源
- Tracer定义统计
- ContextUtil定义了上下文

# 九、服务熔断功能

## 9.1 sentinel整合ribbon+openFeign+fallback

## 9.2 Ribbon系列

前提：启动nacos和sentinel，搭建提供者9003/9004。

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20102.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014526.png)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20103.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014527.png)

### 搭建服务提供者9003/9004

- 新建cloudalibaba-provider-payment9003/9004
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
    
        <artifactId>cloudalibaba-provider-payment9003</artifactId>
    
        **<dependencies>
            <!--SpringCloud ailibaba nacos -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
            <!-- SpringBoot整合Web组件 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!--日常通用jar包配置-->
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
        </dependencies>**
    </project>
    ```
    
- YML
  
    ```yaml
    server:
      port: 9003
    
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
    
- 主启动
  
    ```java
    package com.youliao.springcloud.alibaba;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 12:14
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableDiscoveryClient
    public class PaymentMain9003 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain9003.class, args);
        }
    }
    ```
    
- 业务类
  
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import com.youliao.springcloud.entities.CommonResult;
    import com.youliao.springcloud.entities.Payment;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.HashMap;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 12:15
     * @Version 1.0
     * @Description
     */
    @RestController
    public class PaymentController {
        @Value("${server.port}")
        private String serverPort;
    
        public static HashMap<Long, Payment> hashMap = new HashMap<>();
    
        static {
            hashMap.put(1L, new Payment(1L, "28a8c1e3bc2742d8848569891fb42181"));
            hashMap.put(2L, new Payment(2L, "bba8c1e3bc2742d8848569891ac32182"));
            hashMap.put(3L, new Payment(3L, "6ua8c1e3bc2742d8848569891xt92183"));
        }
    
        @GetMapping(value = "/paymentSQL/{id}")
        public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id) {
            Payment payment = hashMap.get(id);
            CommonResult<Payment> result = new CommonResult(200, "from mysql,serverPort:  " + serverPort, payment);
            return result;
        }
    
    }
    ```
    
- 测试地址：[http://localhost:9003/paymentSQL/1](http://localhost:9003/paymentSQL/1)

### 搭建服务消费者84

- 新建cloudalibaba-consumer-nacos-order84
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
    
        <artifactId>cloudalibaba-consumer-nacos-order84</artifactId>
    
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
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
    
- YML：记得修改不同的端口号
  
    ```yaml
    server:
      port: 84
    
    spring:
      application:
        name: nacos-order-consumer
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            #配置Sentinel dashboard地址
            dashboard: localhost:8080
            #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
            port: 8719
    
    #消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
    service-url:
      nacos-user-service: http://nacos-payment-provider
    ```
    
- 主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 12:29
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    @EnableFeignClients
    public class OrderNacosMain84 {
        public static void main(String[] args) {
            SpringApplication.run(OrderNacosMain84.class, args);
        }
    }
    ```
    
- 业务类
    - ApplicationContextConfig
      
        ```java
        package com.youliao.springcloud.config;
        
        import org.springframework.cloud.client.loadbalancer.LoadBalanced;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.web.client.RestTemplate;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 12:30
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
        
    - CircleBreakerController的全部源码：解决服务熔断
      
        ```java
        package com.youliao.springcloud.controller;
        
        import com.alibaba.csp.sentinel.annotation.SentinelResource;
        import com.alibaba.csp.sentinel.slots.block.BlockException;
        import com.youliao.springcloud.entities.CommonResult;
        import com.youliao.springcloud.entities.Payment;
        import lombok.extern.slf4j.Slf4j;
        import org.springframework.web.bind.annotation.PathVariable;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;
        import org.springframework.web.client.RestTemplate;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 12:30
         * @Version 1.0
         * @Description
         */
        @RestController
        @Slf4j
        public class CircleBreakerController {
        
            public static final String SERVICE_URL = "http://nacos-payment-provider";
        
            @Resource
            private RestTemplate restTemplate;
        
            @RequestMapping("/consumer/fallback/{id}")
            //@SentinelResource(value = "fallback") //没有配置
            //@SentinelResource(value = "fallback",fallback = "handlerFallback") //fallback只负责业务异常
            //@SentinelResource(value = "fallback",blockHandler = "blockHandler") //blockHandler只负责sentinel控制台配置违规
            @SentinelResource(value = "fallback", fallback = "handlerFallback", blockHandler = "blockHandler",
                    exceptionsToIgnore = {IllegalArgumentException.class})
            public CommonResult<Payment> fallback(@PathVariable Long id) {
                CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/" + id, CommonResult.class, id);
        
                if (id == 4) {
                    throw new IllegalArgumentException("IllegalArgumentException,非法参数异常....");
                } else if (result.getData() == null) {
                    throw new NullPointerException("NullPointerException,该ID没有对应记录,空指针异常");
                }
        
                return result;
            }
        
            //fallback
            public CommonResult handlerFallback(@PathVariable Long id, Throwable e) {
                Payment payment = new Payment(id, "null");
                return new CommonResult<>(444, "兜底异常handlerFallback,exception内容  " + e.getMessage(), payment);
            }
        
            //blockHandler
            public CommonResult blockHandler(@PathVariable Long id, BlockException blockException) {
                Payment payment = new Payment(id, "null");
                return new CommonResult<>(445, "blockHandler-sentinel限流,无此流水: blockException  " + blockException.getMessage(), payment);
            }
        }
        ```
    
- 启动以下服务
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20104.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014528.png)
    
- 浏览器访问以下地址
  
    [http://localhost:9003/paymentSQL/1](http://localhost:9003/paymentSQL/1)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20105.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014529.png)
    
    [http://localhost:9004/paymentSQL/1](http://localhost:9004/paymentSQL/1)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20106.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014530.png)
    

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20107.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014531.png)

fallback管运行异常，blockHandler管配置违规，测试地址：[http://localhost:84/consumer/fallback/1](http://localhost:84/consumer/fallback/1)

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20108.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014532.png)

## 9.3 Feign系列

- 修改84模块
  
- POM
  
    ```xml
    <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
            </dependency>
    ```
    
- YML
  
    ```yaml
    #对Feign的支持
    feign:
      sentinel:
        enabled: true
    ```
    
- 业务类
    - 带@FeignClient注解的业务接口
      
        ```java
        package com.youliao.springcloud.service;
        
        import com.youliao.springcloud.entities.CommonResult;
        import com.youliao.springcloud.entities.Payment;
        import org.springframework.cloud.openfeign.FeignClient;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.PathVariable;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 15:03
         * @Version 1.0
         * @Description
         */
        
        @FeignClient(value = "nacos-payment-provider", fallback = PaymentFallbackService.class)
        public interface PaymentService {
            @GetMapping(value = "/paymentSQL/{id}")
            public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id);
        }
        ```
        
    - fallback = PaymentFallbackService.class
      
    - PaymentFallbackService实现类
      
        ```java
        package com.youliao.springcloud.service;
        
        import com.youliao.springcloud.entities.CommonResult;
        import com.youliao.springcloud.entities.Payment;
        import org.springframework.stereotype.Component;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 15:04
         * @Version 1.0
         * @Description
         */
        @Component
        public class PaymentFallbackService implements PaymentService {
            @Override
            public CommonResult<Payment> paymentSQL(Long id) {
                return new CommonResult<>(44444, "服务降级返回,---PaymentFallbackService", new Payment(id, "errorSerial"));
            }
        }
        ```
        
    - Controller
      
        ```java
        // OpenFeign
            @Resource
            private PaymentService paymentService;
        
            @GetMapping(value = "/consumer/paymentSQL/{id}")
            public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id) {
                return paymentService.paymentSQL(id);
            }
        ```
    
- 主启动：添加@EnableFeignClients启动Feign的功能
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 12:29
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @SpringBootApplication
    @EnableFeignClients
    public class OrderNacosMain84 {
        public static void main(String[] args) {
            SpringApplication.run(OrderNacosMain84.class, args);
        }
    }
    ```
    
- [http://lcoalhost:84/consumer/paymentSQL/1](http://lcoalhost:84/consumer/paymentSQL/1)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20109.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014533.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20110.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014534.png)
    
- 测试84调用9003，此时故意关闭9003微服务提供者，看84消费侧自动降级，不会被耗死
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20111.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014536.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20112.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014537.png)
    

## 9.4 熔断框架比较（注意面试）

![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20113.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014538.png)

# 10、规则持久化

## 规则持久化是什么：

一旦我们重启应用，Sentinel规则将消失，生产环境需要将配置规则进行持久化

## 规则持久化怎么玩：是通过Nacos配合持久化来完成的

将限流配置规则持久化进Nacos保存，只要刷新8401某个rest地址，sentinel控制台的流控规则就能看到，只要Nacos里面的配置不删除，针对8401上Sentinel上的流控规则持续有效

## 配置规则持久化步骤：启动Nacos和Sentinel

- 修改cloudalibaba-sentinel-service8401
- POM
  
    ```xml
    <dependency>
                <groupId>com.alibaba.csp</groupId>
                <artifactId>sentinel-datasource-nacos</artifactId>
            </dependency>
    ```
    
- YML：添加Nacos数据源配置
  
    ```yaml
    server:
      port: 8401
    
    spring:
      application:
        name: cloudalibaba-sentinel-service
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            dashboard: localhost:8080
            port: 8719  #默认8719，假如被占用了会自动从8719开始依次+1扫描。直至找到未被占用的端口
          datasource: #添加Nacos数据源配置
            ds1:
              nacos:
                server-addr: localhost:8848
                dataId: cloudalibaba-sentinel-service
                groupId: DEFAULT_GROUP
                data-type: json
                rule-type: flow
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    
    feign:
      sentine l:
        enabled: true # 激活Sentinel对Feign的支持
    ```
    
- 添加Nacos业务规则配置：[http://localhost:8848/nacos/#/login](http://localhost:8848/nacos/#/login)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20114.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014539.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20115.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014540.png)
    
    ```json
    [
        {
             "resource": "/rateLimit/byUrl",
             "limitApp": "default",
             "grade":   1,
             "count":   1,
             "strategy": 0,
             "controlBehavior": 0,
             "clusterMode": false
        }
    ]
    ```
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20116.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014541.png)
    
    - resource：资源名称
    - limitApp：来源应用
    - grade：阈值类型，0表示线程数，1表示QPS
    - count：单机阈值
    - strategy：流控模式，0表示直接，1表示关联，2表示链路
    - controlBehavior：流控效果，0表示快速失败，1表示Warm，2表示排队等待
    - clusterMode：是否集群
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20117.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014542.png)
    
- 启动8401后刷新sentinel发现业务规则有了
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20118.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014543.png)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20119.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014544.png)
    
    [http://localhost:8401/rateLimit/byUrl](http://localhost:8401/rateLimit/byUrl)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20120.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014545.png)
    
    [](http://localhost:8080/)
    
- 快速访问测试接口：[http://localhost:8401/rateLimit/byUrl](http://localhost:8401/rateLimit/byUrl)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/%E5%BD%95%E5%88%B6_2021_08_02_18_23_17_889.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014546.gif)
    
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20121.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014547.png)
    
- 停止8401再看sentinel：[http://localhost:8080](http://localhost:8080/)
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20122.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014548.png)
    
- 重新启动8401再看sentinel
  
    ![17_SpringCloud%20Alibaba%20Sentinel%E5%AE%9E%E7%8E%B0%E7%86%94%E6%96%AD%E4%B8%8E%E9%99%90%E6%B5%81%20d05b7cf7717345d09a290dfff51cf4d7/Untitled%20123.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082014549.png)
    
    扎一看还是没有，稍等一会儿，多次调用：[http://localhost:8401/rateLimit/byUrl](http://localhost:8401/rateLimit/byUrl)，重新配置出现了，持久化验证通过。