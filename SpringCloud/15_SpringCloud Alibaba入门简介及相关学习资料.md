# 15_SpringCloud Alibaba入门简介及相关学习资料

# 一、SpringCloud Alibaba入门简介

## 为什么会出现SpringCloud alibaba：

SpringCloud Alibaba诞生的主要原因是：因为Spring Cloud Netflix项目进入了维护模式。

地址：

[Spring Cloud Greenwich.RC1 available now](https://spring.io/blog/2018/12/12/spring-cloud-greenwich-rc1-available-now)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081954324.png)

### 什么是维护模式

将模块置为维护模式，意味着SpringCloud团队将不再向模块添加新功能，我们将恢复block级别的bug以及安全问题，我们也会考虑并审查社区的小型pull request。

我们打算继续支持这些模块，知道Greenwich版本被普遍采用至少一年

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081954326.png)

### 进入维护模式意味着什么呢？

Spring Cloud Netflix将不再开发新的组件，我们都知道Spring Cloud项目迭代算是比较快，因此出现了很多重大issue都还来不及Fix，就又推出了另一个Release。进入维护模式意思就是以后一段时间Spring Cloud Netflix提供的服务和功能就这么多了，不在开发新的组件和功能了，以后将以维护和Merge分支Pull Request为主，新组件将以其他替代

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081954327.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081954328.png)

## SpringCloud alibaba带来了什么？

### SpringCloud alibaba是什么？

SpringCloud alibaba官网地址：[https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)

2018.10.31，Spring Cloud Alibaba正式入驻Spring Cloud官方孵化器，并在Maven仓库发布了第一个版本。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206081954329.png)

### SpringCloud alibaba能干嘛？

- **服务限流降级**：默认支持 WebServlet、WebFlux, OpenFeign、RestTemplate、Spring Cloud Gateway, Zuul, Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
- **服务注册与发现**：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
- **分布式配置管理**：支持分布式系统中的外部化配置，配置更改时自动刷新。
- **消息驱动能力**：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
- **分布式事务**：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
- **阿里云对象存储**：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
- **分布式任务调度**：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
- **阿里云短信服务**：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道

### SpringCloud alibaba去哪下？

[https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)

### SpringCloud alibaba怎么玩？

- **[Sentinel](https://github.com/alibaba/Sentinel)**：把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。
- **[Nacos](https://github.com/alibaba/Nacos)**：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。
- **[RocketMQ](https://rocketmq.apache.org/)**：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。
- **[Dubbo](https://github.com/apache/dubbo)**：Apache Dubbo™ 是一款高性能 Java RPC 框架。
- **[Seata](https://github.com/seata/seata)**：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。
- **[Alibaba Cloud OSS](https://www.aliyun.com/product/oss)**: 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。
- **[Alibaba Cloud SchedulerX](https://help.aliyun.com/document_detail/43136.html)**: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。
- **[Alibaba Cloud SMS](https://www.aliyun.com/product/sms)**: 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

引入spring-cloud-alibaba依赖版本控制：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## SpringCloud alibaba学习资料获取：

SpringCloud alibaba英文学习资料：

地址1：[https://github.com/alibaba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba)

地址2：[https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html)

SpringCloud alibaba中文学习资料：

地址：[https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.m](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)