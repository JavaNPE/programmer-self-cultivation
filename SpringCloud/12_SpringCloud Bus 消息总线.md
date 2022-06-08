# 12_SpringCloud Bus 消息总线

# 一、消息总线SpringCloud Bus概述

上一讲解的加深和扩充，一言以蔽之。**分布式自动刷新配置功能，Spring Cloud Bus配合Spring Cloud Config使用可以实现配置的动态刷新。**

## SpringCloud Bus是什么？

分布式自动刷新配置功能，SpringCloud Bus配合SpringCloud Config使用可以实现配置的动态刷新。

Bus支持两种消息代理：RabbitMQ和Kafka

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947363.png)

## SpringCloud Bus能干嘛？

SpringCloud Bus是用来将分布式系统的节点与轻量级消息系统链接起来的框架，**它整合了Java的事件处理机制和消息中间件的功能。**

**SpringCloud Bus能管理和传播分布式系统的消息**，就像一个分布式执行器，可用于广播状态更改，事件推送等，也可以当做微服务的通信通道。

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947365.png)

## 为何被称为总线？

### **什么是总线**

在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以被称为消息总线。在总线上的各个实例，都可以方便的广播一些需要让其它连接在该主题上的实例都知道的消息。

### **基本原理**

ConfigClient实例都监听MQ中同一个topic（默认是SpringCloudBus），但一个服务刷新数据的时候，它会被这个消息放到Topic中，这样其它监听同一个Topic的服务就能够得到通知，然后去更新自身的配置。

# 二、RabbitMQ环境配置

## 安装Erlang，下载地址：

尚硅谷课程使用的Erlang版本：[http://erlang.org/download/otp_win64_21.3.exe](http://erlang.org/download/otp_win64_21.3.exe) 注意Erlang与RabbitMQ之间版本匹配的问题

**本人使用的Erlang版本：**[http://erlang.org/download/otp_win64_23.3.4.exe](http://erlang.org/download/otp_win64_23.3.4.exe)

- 安装Erlang步骤
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947366.png)
    
    Erlang安装位置：`d:\Program Files\erl-23.3.4`
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947367.png)
    
    默认配置：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947368.png)
    
    然后在配置环境变量：
    
    ![按照此图安装](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947369.png)
    
    按照此图安装
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947370.png)
    
    然后添加到Path中：`%ERLANG_HOME%\bin`
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947371.png)
    
    打开cmd，输入`erl`，查看是否配置成功：
    
    ![输入命令，出现这样的提示，说明Erlang环境配置成功](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947372.png)
    
    输入命令，出现这样的提示，说明Erlang环境配置成功
    

## 安装RabbitMQ，下载地址:

RabbitMQ官网地址首页：[https://www.rabbitmq.com/install-windows.html](https://www.rabbitmq.com/install-windows.html)

尚硅谷课程使用的RabbitMQ版本：[https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe](https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe)

- 安装RabbitMQ步骤
  
    **截至 July 21, 2021 本人在RabbitMQ官网地址下载的版本首页**：[https://www.rabbitmq.com/install-windows.html](https://www.rabbitmq.com/install-windows.html)
    
    点击下载3.8.19版本RabbitMQ：
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947373.png)
    
    双击安装包
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947374.png)
    
    点击Next，然后选择安装目录。
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947375.png)
    

## 进入RabbitMQ安装目录下的sbin目录：

如例我自己本机`D:\Program Files\RabbitMQ Server\rabbitmq_server-3.8.19\sbin`

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947376.png)

## 在sbin目录下调出cmd窗口输入以下命令启动管理功能

`rabbitmq-plugins enable rabbitmq_management`

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947377.png)

**小插曲——但是出现了报错：**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947379.png)

原因是rabbitmq与erlang版本号不匹配，我安装的rabbitmq-server-3.8.19，安装的erlang版本号为21.3。官方要求erlang版本号最低为23.2。重新安装erlang就可以了。

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947380.png)

**rabbitmq与erlang的官方匹配度建议：**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947381.png)

**可视化插件:**

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947382.png)

**RabbitMQ的使用：**

启动

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947383.png)

提示以下错误信息：

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947384.png)

我们通过命令的方式启动RabbitMQ

```yaml
进入rabbitmq的sbin目录

# 启动rabbitmq_managemen是管理后台的插件、我们要开启这个插件才能通过浏览器访问登录页面
rabbitmq-plugins enable rabbitmq_management
 
# 启动rabbitmq
rabbitmq-server start
 
#访问登录页面 用户名和密码都是guest
http://localhost:15672
```

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947385.png)

## 访问地址查看是否安装成功:

浏览器中输入：[http://localhost:15672/](http://localhost:15672/)

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947386.png)

输入账号密码并登录: guest guest

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947387.png)

# 三、SpringCloud Bus动态刷新全局广播

必须先具备良好的RabbitMQ环境先，演示广播效果，增加复杂度，再以3355为模板再制作一个3366。

- 1、新建cloud-config-client-3366
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
    
        <artifactId>cloud-config-client-3366</artifactId>
    
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-config</artifactId>
            </dependency>
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
- 4、主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    
    /**
     * @Author Dali
     * @Date 2021/07/21 下午 6:26
     * @Version 1.0
     * @Description
     */
    @EnableEurekaClient
    @SpringBootApplication
    public class ConfigClientMain3366 {
        public static void main(String[] args) {
            SpringApplication.run( ConfigClientMain3366.class,args);
        }
    }
    ```
    
- 5、controller：ConfigClientController
  
    ```java
    package com.youliao.springcloud.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.cloud.context.config.annotation.RefreshScope;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/07/21 下午 6:28
     * @Version 1.0
     * @Description
     */
    @RestController
    @RefreshScope
    public class ConfigClientController {
    
        @Value("${server.port}")
        private String serverPort;
    
        @Value("${config.info}")
        private String configInfo;
        
        @GetMapping("/configInfo")
        public String getConfigInfo(){
            return "serverPort:"+serverPort+"\t\n\n configInfo: "+configInfo;
        }
    }
    ```
    

## SpringCloud Bus设计思想：

- 1、利用消息总线触发一个**客户端/bus/refresh**,而刷新所有客户端的配置
  
    ![图一](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947388.png)
    
    图一
    
- 2、利用消息总线触发一个**服务端ConfigServer的/bus/refresh**端点,而刷新所有客户端的配置**（更加推荐）**
  
    ![图二](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947389.png)
    
    图二
    
    图二的架构显然更加合适，图一不适合的原因如下：
    
    - 打破了微服务的职责单一性，因为微服务本身是业务模块，它本不应该承担配置刷新职责。
    - 破坏了微服务各节点的对等性。
    - 有一定的局限性。例如，微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改。
- 给cloud-config-center-3344配置中心**服务端**添加消息总线支持 yml文件
    - POM
      
        ```xml
        <dependency>   <!--添加消息总线Rabbitmq支持-->
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
        ```
        
    - YML
      
        ```yaml
        server:
          port: 3344
        spring:
          application:
            name: cloud-config-center
          cloud:
            config:
              server:
                git:
                  uri:  https://github.com/hhf19906/springcloud-config.git  #git@github.com:hhf19906/springcloud-config.git
                  search-paths:
                    - springcloud-config
              label: master
        
        **rabbitmq:
            host: localhost
            port: 5672
            username: guest
            password: guest**
        
        eureka:
          client:
            service-url:
              defaultZone:  http://localhost:7001/eureka
        
        **management:
          endpoints:
            web:
              exposure:
                include: 'bus-refresh'**
        
        注意，上面的 bus-refresh 就是actuator的刷新机制，相当于提供了一个 /bus-refresh的post方法，当我们调用的时候，会刷新配置，然后一次发送，处处生效。
        ```
    
- 给cloud-config-center-3355**客户端**添加消息总线支持
    - POM
      
        ```xml
        <!--添加消息总线Rabbitmq支持-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
        ```
        
    - YML：bootstrap.yml
      
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
        
        **#rabbitmq.相关配置15672 是Web管理界面的端口; 5672 是MQ访问的端口
          rabbitmq: #mq相关配置
            host: localhost
            port: 5672
            username: guest
            password: guest**
        
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
        
    
- 给cloud-config-center-3366**客户端**添加消息总线支持
    - POM
      
        ```xml
        <dependency>   <!--添加消息总线Rabbitmq支持-->
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
        ```
        
    - bootstrap.yml
      
        ```yaml
        server:
          port: 3366
        
        spring:
          application:
            name: config-client
          cloud:
            #Config客户端配置
            config:
              label: master #分支名称
              name: config #配置文件名称
              profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
              uri: http://localhost:3344 #配置中心地址
          **#rabbitmq.相关配置15672 是Web管理界面的端口; 5672 是MQ访问的端口
          rabbitmq: #mq相关配置
            host: localhost
            port: 5672
            username: guest
            password: guest**
        
        #服务注册到eureka地址
        eureka:
          client:
            service-url:
              defaultZone: http://eureka7001.com:7001/eureka
        
        **# 暴露监控端点
        management:
          endpoints:
            web:
              exposure:
                include: "*"**
        ```
        

![此时的一个架构图](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947390.png)

此时的一个架构图

**测试：**

- 启动以下微服务：
  
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947391.png)
    
    Eureke：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947392.png)
    
- 运维工程师，修改Github上配置文件增加版本号，发送Post请求：当我们的服务端配置中心 和 客户端都增加完上述配置后，我们需要做的就是手动发送一个POST请求到服务端
- `curl -X POST "[http://localhost:3344/actuator/bus-refresh](http://localhost:3344/actuator/bus-refresh)"` 一次发送，处处生效。
  
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947393.png)
    
- 此时我们的git中的版本是**4**
  
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947394.png)
    
- **配置中心：**[http://config-3344.com:3344/config-dev.yml](http://config-3344.com:3344/config-dev.yml)
  
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2030.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947395.png)
    
- 客户端：
  
    **测试1**：[http://localhost:3355/configInfo](http://localhost:3355/configInfo)
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947396.png)
    
    **测试2**：[http://localhost:3366/configInfo](http://localhost:3366/configInfo)
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947397.png)
    
    然后在git中修改版本号信息5：
    
    ![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947398.png)
    
    在cmd命令窗口键入： `curl -X POST "[http://localhost:3344/actuator/bus-refresh](http://localhost:3344/actuator/bus-refresh)"` 一次发送，处处生效。再次进行**测试1和测试2**获取配置信息，发现都已经刷新了。
    

# 四、SpringCloud Bus动态刷新定点通知

不想全部通知，只想定点通知，比如：只通知3355，不通知3366。

**简单一句话：指定具体某一个实例生效而不是全部。**

**公式：**[`http://localhost](http://localhost/):配置中心的端口号/actuator/bus-refresh/{destination}` 

/bus/refresh请求不再发送到具体的服务实例上，而是发给config server并通过destination参数类指定需要更新配置的服务或实例。

### **案例：**

我们这里以刷新运行在3355端口上的config-client为例：只通知3355，不通知3366。

先启动rabbitmq：

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947399.png)

可以在cmd中使用以下命令：（前提是别忘先启动rabbitmq服务）

`curl -X POST "[http://localhost:3344/actuator/bus-refresh/config-client:3355](http://localhost:3344/actuator/bus-refresh/config-client:3355)"`

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947400.png)

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2036.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947401.png)

### 通知总结All

![12_SpringCloud%20Bus%20%E6%B6%88%E6%81%AF%E6%80%BB%E7%BA%BF%20b6d7eee77c0249119fc1918591b7fa3d/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206080947402.png)