# 01_微服务概述-SpringCloud组件对比-停更解决方案-官方文档汇总

# 1、微服务架构概述

## 1.1什么是微服务

微服务架构是种架构模式，它提倡将单一应用程序划分成组小的服务，服务之间互相协调、互相配合， 为用户提供最终价值。每个服务运行在其独立的进程中，服务与服务间采用轻量级的通信机制互相协作(通常是基于HTTP协议的RESTfulAPI)。每个服务都围绕着具本业务进行构建，并且能够被独立的部署到生产环境、类生产环境等。另外，应当尽量避免统一的、集中式的服务管理机制，对具体的七个服务而言负应根据业务上下文，选择合适的语言、工具对其进行构建。

### 1.1.1**主题词01: 95后数字化生活落地维度**

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151537.png)

### 1.1.2**主题词02:分布式微服务架构-落地维度**

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151539.png)

### 1.1.3**主题词03:基于分布式的微服务架构**

要求说得出来 是什么：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151540.png)

# 2、SpringCloud简介

Spring Cloud 是分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体，俗称微服务全家桶。 类比：肯德基全家桶，有可乐有鸡翅有汉堡等。

## SpringCloud这个大集合里有多少种技术：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151541.png)

### 下面一张图是京东的促销架构

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151542.png)

### 阿里的架构图：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151543.png)

### 京东物流的架构图：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151544.png)

## 基础服务：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151545.png)

# 3、SpringCloud技术栈

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151546.png)

## Spring Cloud版本选型

### SpringBoot2.X版 和 SpringCloud H版

SpringBoot官方已经强烈推荐 2.X版

SpringCloud采用英国伦敦地铁站的名称来命名，并由地铁站名称字母A-Z一次类推的形式发布迭代版本

SpringCloud是由许多子项目组成的综合项目，各子项目有不同的发布节奏，为了管理SpringCloud与各子项目的版本依赖关系，发布了一个清单，其中包括了某个SpringCloud版对应的子项目版本，为了避免SpringCloud版本号与子项目版本号混淆，SpringCloud版采用了名称而非版本号命名。例如Angel，Brixton。当SpringCloud的发布内容积累到临界点或者一个重大BUG被解决后，会发布一个Service releases版本，俗称SRX版本，比如 Greenwich.SR2就是SpringCloud发布的Greenwich版本的第二个SRX版本。

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151547.png)

2021/06/28日查看：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151548.png)

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151549.png)

### SpringBoot和SpringCloud版本约束

SpringBoot和SpringCloud的版本选择也不是任意的，而是应该参考官网的约束配置。

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151550.png)

**地址**：`https://spring.io/projects/spring-cloud`

**SpringCloud和Springboot之间版本对应：**`https://start.spring.io/actuator/info`

![浏览器需要安装对应的JSON插件](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151551.png)

浏览器需要安装对应的JSON插件

![网址二维码](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151552.png)

网址二维码

### 本次教程开发各技术组件的版本号选择

cloud：Hoxton.SR1
boot：2.2.2.RELEASE
cloud alibaba：2.1.0.RELEASE
Java：Java8
Maven：3.5及以上
Mysq|：5.7及以上
上述全部版本必须保持一致

# 4、关于Cloud各种组件的停更/升级/替换

## 停更不停用：什么是停更？

- 被动修复Bugs
- 不再接受合并请求
- 不再发布新版本

## 2020年之前SpringCloud技术选型：

1. 服务注册与发现：Eureka
2. 服务负载与调用：Ribbon
3. 服务负载与调用：Feing
4. 服务熔断与降级：Hystrix
5. 服务网关：Zuul
6. 服务分布式配置：SpringCloud Config
7. 服务开发：SpringBoot

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151553.png)

但是随着Eureka等组件的闭源，后续的一些解决方案也有了新的替换产品

## 2020年之后SpringCloud技术选型：

![01_%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%A6%82%E8%BF%B0%20%E5%85%B3%E4%BA%8ESpringCloud%E5%90%84%E7%BB%84%E4%BB%B6%E7%9A%84%E5%81%9C%E6%9B%B4%20%E5%8D%87%E7%BA%A7%20%E6%9B%BF%E6%8D%A2%2071e487dab86b4011b318349cf9d1fb20/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022151554.png)

## SpringCloud升级之后的替代产品的区别和推荐

### [1、服务注册中心](https://www.notion.so/06_Consul-48e2052fcfae4f9d8508b0f5a4c24a98)

- [Eureka](https://www.notion.so/01_Eureka-c1b35e2878a841aa8241ca6639510d2e)：重症患者，进ICU用也救不活那种（凑合能用）[@EnableEurekaServer](https://www.notion.so/03_-Eureka-eureka-1c13b1b0602a4c79a0219b6e23e23174):实现服务治理/服务注册与发现。
- Zookeeper：老系统 [Dubbo](https://www.notion.so/01_Eureka-c1b35e2878a841aa8241ca6639510d2e)（做服务调用）  + Zookeeper（做服务注册中心）
- Consul：Go语言写的
- **Nacos（重点推荐）阿里巴巴的Nacos**

### 2、服务调用

- Feign
- **OpenFeign（强烈推荐）**
- Ribbon：轻度患者，还能用，服务端负载均衡，主要提供软件负载均衡算法。
- [LoadBalancer](https://www.notion.so/04_-Eureka-eureka-24fed6d072c54377b7d749dd3f6cc13f)

### 3、服务降级

- Hystrix：@EnableCircuitBreaker:熔断器，保护系统控制故障范围。
- resilience4j：国外用的比较多
- **sentienl（国内推荐）**SpringCloud Alibaba Sentine实现熔断与限流

### 4、服务网关

- Zuul：@EnableZuulProxy,网关管理由Zuul，网关转发请求给对应的服务。
- Zuul2
- **Gateway（推荐）**

### 5、服务配置

- Config
- **Nacos（推荐）**

### 6、服务总线

- Bus
- **Nacos（推荐）**

# SpringCloud和SpringBoot相关技术参考资料

## SpringCloud英文参考文档：

[Spring Cloud](https://cloud.spring.io/spring-cloud-static/Hoxton.SR1/reference/htmlsingle/)

## SpringCloud中文文档：

[首页](https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md)

## SpringBoot参考文档：

[Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/)