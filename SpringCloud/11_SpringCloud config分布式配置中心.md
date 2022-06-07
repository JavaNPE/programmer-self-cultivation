# 11_SpringCloud config分布式配置中心

# 一、SpringCloud config概述

## 1、分布式系统面临的配置问题

微服务意味着要将单体应用中的业务拆分成一个个子服务, 每个服务的粒度相对较小，因此系统中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、动态的配置管理设施是必不可少的。

SpringCloud提供了ConfigServer来解决这个问题，我们每一个微服务自己带着一个application.yml,上 百个配置文件的管理那岂不要折磨疯了茶哥..../(ToT)/~~

## 2、SpringCloud config是什么

SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为**各个不同微服务应用**提供了一个**中心化的外部配置**。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921522.png)

## 3、SpringCloud config 怎么玩

服务端也称为**分布式配置中心，它是一个独立的微服务应用**，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。

客户端则是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息，配置服务器默认采用git来存储配置信息，这样有助于对环境配置进行版本管理，并且可以通过git客户端工具来方便的管理和访问配置内容。

## 4、SpringCloud config**能做什么**

- 集中管理配置文件
- 不同环境不同配置，动态化的配置更新，分布式部署，比如 dev/test/prod/beta/release
- 运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取自己的信息
- 当配置发生变动时，服务不需要重启即可感知配置的变化并应用新的配置
- 将配置信息以REST接口的形式暴露：post，curl命令刷新

## 5、**与Github整合部署**

由于SpringCloud Config默认使用Git来存储配置文件（也有其他方式，比如支持SVN和本地文件），但最推荐的还是Git，而且使用的是Http/https访问的形式

## 6、SpringCloud config官网

[https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/](https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921523.png)

# 二、Config服务端配置与测试

1、用你自己的账号在Github上新建一个名为sprincloud-config的新Repository。

2、由上一步获得刚新建的git地址（写你自己的仓库地址：[https://gitee.com/SuperVITA/springcloud-config.git](https://gitee.com/SuperVITA/springcloud-config.git)）

3、本地硬盘上新建git仓库并clone：

本地地址：D:\44\SpringCloud2020

git命令：`git clone xxx`

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921524.png)

4、此时在本地D盘符下**D:\44\SpringCloud2020\springcloud-config**

表示多个环境的配置文件；

保存格式必须为UTF-8；

如果需要修改本地git文件然后提交远程仓库，此处模拟运维人员操作git和github：

`git add` 、`git commit -m "init yml"` 、`git push origin master`

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921525.png)

- 1、**新建Module模块cloud-config-center-3344** 它既为Cloud的配置中心模块cloudConfig Center
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
    
        <artifactId>cloud-config-center-3344</artifactId>
    
        <dependencies>
            **<dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-config-server</artifactId>
            </dependency>**
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
    
- 3、YML
  
    ```yaml
    server:
      port: 3344
    spring:
      application:
        name: cloud-config-center #注册进Eureka服务器的微服务名
      cloud:
        config:
          server:
            git:
              uri: https://gitee.com/SuperVITA/springcloud-config.git #填写你自己的github路径
              search-paths: ####搜索目录
                - springcloud-config
          #### 读取分支
          label: master
    
    #服务注册到eureka地址
    eureka:
      client:
        service-url:
          defaultZone:  http://localhost:7001/eureka
    ```
    
- 4、主启动：ConfigCenterMain3344：要使用@EnableConfigServer注解
  
    ```java
    package com.youliao.springcloud;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.config.server.EnableConfigServer;
    
    /**
     * @Author Dali
     * @Date 2021/07/14 下午 12:55
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableConfigServer
    public class ConfigCenterMain3344 {
        public static void main(String[] args) {
            SpringApplication.run(ConfigCenterMain3344.class, args);
        }
    }
    ```
    
- 5、windows下修改hosts文件，增加映射：127.0.0.1 [config-3344.com](http://config-3344.com/)
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921526.png)
    
- 6、测试通过Config微服务是否可以从Github上获取配置内容
  
    6.1 启动微服务3344
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921527.png)
    
    6.2测试地址： [http://config-3344.com:3344/master/config-dev.yml](http://config-3344.com:3344/master/config-dev.yml)
    
    [http://config-3344.com:3344/master/config-test.yml](http://config-3344.com:3344/master/config-test.yml)
    
    访问结果：浏览器中出现git仓库中的代码内容
    
    浏览器中出现gitee中的文件内容，说明我们的Config Server3344→Local Git repository → Remote Git repository这条链路是正常的。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921528.png)
    
    成功实现了用SpringCloud Config 通过GitHub获取配置信息
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921529.png)
    
    这是Config服务端的配置-Config Server
    

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921530.png)

### **配置读取规则**

- /{label}/{application}-{profile}.yml
    - [http://config-3344.com:3344/master/config-dev.yml](http://config-3344.com:3344/master/config-dev.yml)
- /{application}-{profile}.yml
    - [http://config-3344.com:3344/config-dev.yml：如果不写的话，默认就是从master分支上找](http://config-3344.com:3344/config-dev.yml%EF%BC%9A%E5%A6%82%E6%9E%9C%E4%B8%8D%E5%86%99%E7%9A%84%E8%AF%9D%EF%BC%8C%E9%BB%98%E8%AE%A4%E5%B0%B1%E6%98%AF%E4%BB%8Emaster%E5%88%86%E6%94%AF%E4%B8%8A%E6%89%BE)

官网推荐：/{label}/{application}-{profile}.yml（最推荐使用这种方式）

### **参数总结**

label：分支，branch

name：服务名

profiles：环境(dev/test/prod)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921531.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921532.png)

# 三、Config客户端配置与测试

框架图：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921533.png)

### bootstrap.yml

application.yml：是用户级的资源配置项

**bootstrap.yml：**是系统级别的，优先级更加高。

Spring Cloud会创建一个**Bootstrap Context**，作为Spring应用的Application Context的父上下文。初始化的时候，Bootstrap Context负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的Environment。

Bootstrap属性有高优先级，默认情况下，他们不会被本地配置覆盖，Bootstrap context 和 Application Context有着不同的约定，所以新增了一个bootstrap.yml文件，保证Bootstrap Context 和 Application Context配置的分离。

要将客户端Client模块下的Application.yml文件改成bootstrap.yml这是很关键的，因为bootstrap.yml是比application.yml先加载的，bootstrap.yml优先级高于application.yml

- 1、新建cloud-config-client-3355
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
    
        <artifactId>cloud-config-client-3355</artifactId>
    
        <dependencies>
    
           ** <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-config</artifactId>
            </dependency>**
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <dependency>  <!--引入自定义的api通用包，可以使用Payment支付Entities-->
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
    
- 3、bootstrap.yml
  
    ```yaml
    server:
      port: 3355
    
    spring:
      application:
        name: config-client
      cloud:
        #Config客户端配置
        config:
          label: master #分支名称
          name: config #配置文件名称
          profile: dev #读取后缀名称  上述3个综合：master分支上config-dev.yml的配置文件被读取
          uri: http://localhost:3344 #配置中心地址，去3344服务端拿到git中的配置信息
    
    # 服务注册到eureka地址
    eureka:
      client:
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka
    ```
    
    - 关于bootstap.yml相关说明
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921534.png)
    
- 4、修改config-dev.yml配置并提交到GitHub中，比如加个变量age或者版本号version
- 5、主启动：ConfigClientMain3355类
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/20 下午 11:13
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableEurekaClient
    public class ConfigClientMain3355 {
        public static void main(String[] args) {
            SpringApplication.run( ConfigClientMain3355.class,args);
        }
    }
    ```
    
- 6、业务类：ConfigClientController
  
    ```java
    package com.youliao.springcloud.Controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/07/20 下午 11:16
     * @Version 1.0
     * @Description
     */
    @RestController
    public class ConfigClientController {
    
        @Value("${config.info}")
        private String configInfo;
    
        @GetMapping("/configInfo")
        public String getConfigInfo(){
            return configInfo;
        }
    }
    ```
    
- 7、测试：
    - 启动eureka7001：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921535.png)
        
    - 启动Config配置中心3344微服务并自测
      
        测试：[http://config-3344.com:3344/master/config-dev.yml](http://config-3344.com:3344/master/config-dev.yml)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921536.png)
        
        测试：[http://config-3344.com:3344/master/config-test.yml](http://config-3344.com:3344/master/config-test.yml)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921537.png)
        
    - 启动3355作为Client准备访问，测试：[http://localhost:3355/configInfo](http://localhost:3355/configInfo)
      
        ![测试成功页面：成功实现了客户端3355访问SpringCloud Config3344通过GitHub获取配置信息](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921538.png)
        
        测试成功页面：成功实现了客户端3355访问SpringCloud Config3344通过GitHub获取配置信息
        
        启动3355报错：Caused by: java.lang.IllegalArgumentException: Could not resolve placeholder '[config.info](http://config.info/)' in value "${[config.info](http://config.info/)}"
        
        ![bootstrap.yml名字别写错了](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921539.png)
        
        bootstrap.yml名字别写错了
        

# 四、Config客户端之动态刷新

## 分布式配置存在的问题：

分布式配置的动态刷新问题？

- Linux运维修改Github上的配置文件内容做调整
- 刷新3344，发现ConfigServer配置中心立刻响应
- 刷新3355，发现ConfigClient客户端没有任何响应
- 3355没有变化除非自己重启或者重启加载
- 难道每次运维修改后，都需要重启？

相当于直接修改Github上的配置文件，配置不会改变，这个时候就存在了分布式配置的动态刷新问题。

## Config客户端之动态刷新

为了避免每次更新配置都要重启客户端微服务3355，休要修改3355模块。

- POM引入actuator监控
- 修改YML，暴露监控端口
  
    ```yaml
    #暴露监控端点
    management:
      endpoints:
        web:
          exposure:
            include: "*"
    ```
    
    ```yaml
    server:
      port: 3355
    
    spring:
      application:
        name: config-client
      cloud:
        #Config客户端配置
        config:
          label: master #分支名称
          name: config #配置文件名称
          profile: dev #读取后缀名称  上w述3个综合：master分支上config-dev.yml的配置文件被读取
          uri: http://localhost:3344 #配置中心地址，去3344服务端拿到git中的配置信息
    
    # 服务注册到eureka地址
    eureka:
      client:
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka
    
    **#暴露监控端点
    management:
      endpoints:
        web:
          exposure:
            include: "*"**
    ```
    
- 在业务类ConfigClientController中，添加 @RefreshScope标签
  
    ```java
    @RestController
    //实现刷新功能
    @RefreshScope
    public class ConfigClientController {
    
        @Value("${config.info}")
        private String configInfo;
    
        @GetMapping("/configInfo")
        public String getConfigInfo(){
            return configInfo;
        }
    
    }
    ```
    
- 此时修改github---> 3344 ---> 3355
  
    [http://localhost:3355/configInfo](http://localhost:3355/configInfo)
    
    3355改变了没有改变
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921540.png)
    
- 需要运维人员发送**Post请求**刷新3355
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921541.png)
    
    ```java
    curl -X POST "http://localhost:3355/actuator/refresh"
    ```
    
- 再次尝试[http://localhost:3355/configInfo](http://localhost:3355/configInfo)请求 成功实现了客户端3355刷新到最新配置内容，避免了服务的重启
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071921542.png)
    

然后就能够生效了，成功刷新了配置，避免了服务重启

这个方案存在问题：

- 假设有多个微服务客户端 3355/ 3366 / 3377
- 每个微服务都要执行一次post请求，手动刷新？
- 可否广播，一次通知，处处生效？
- 我们想大范围的自动刷新，求方法....

目前来说，暂时做不到这个，所以才用了下面的内容，即Spring Cloud Bus 消息总线