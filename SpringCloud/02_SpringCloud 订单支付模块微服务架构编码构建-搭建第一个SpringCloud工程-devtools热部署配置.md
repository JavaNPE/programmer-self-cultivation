# 02_SpringCloud 订单支付模块微服务架构编码构建-搭建第一个SpringCloud工程-devtools热部署配置

# 模块一：工程框架图

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

# 一、微服务cloud整体聚合父工程Project

## 1、父工程步骤

### 1.1 New Project

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

### 1.2 聚合总工程名字

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

### 1.3 Maven选版本

选择我们安装的Maven 

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

### 1.4 工程名字

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

### 1.5 字符编码

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

### 1.6 注解生效激活

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

### 1.7 java编译版本选择

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

![问答地址：[https://ask.csdn.net/questions/7461709](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png) ](02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%208.png)

问答地址：[https://ask.csdn.net/questions/7461709](https://ask.csdn.net/questions/7461709) 

### 1.8 File Type过滤

```java
*.hprof;*.idea;*.pyc;*.pyo;*.rbc;*.yarb;*iml;*~;.DS_Store;.git;.hg;.svn;CVS;__pycache__;_svn;vssver.scc;vssver2.scc;
```

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)

## 2、父工程pom

[Cloud2021父工程pom](https://www.notion.so/Cloud2021-pom-fff21461ab5b42fdb740ef5490984bd0)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

## 补充：Maven中的dependencyManagement和Idependencies的区别？

**dependencyManagement**

Maven使用dependencyManagement元素来提供了-种管理依赖版本号的方式。

**通常会在一个组织或者项目的最顶层的父POM中看到dependencyManagement元素。**

使用pom.xml中的dependencyManagement元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。

Maven会沿着父子层次向上走,直到找到一个拥有dependencyManagement元素的项目,然后它就会使用这个dependencyManagement元素中指定的版本号。



这样做的好处就是:如果有多个子项目都引用同-样依赖,则可以避免在每个使用的子项目里都声明一个版本号,这样当想升级或切换到另一个版本时,只需要在顶层父容器里更新,而不需要一个一 个子项目的修改;另外如果某个子项目需要另外的一个版本，只需要声明version就可。

* dependencyManagement里只是声明依赖，并不实现引入,因此子项目需要显示的声明需要用的依赖。
* 如果不在子项目中声明依赖，是不会从父项目中继承下来的;只有在子项目中写了该依赖项,并且没有指定具体版本，才会从父项目中继承该项，并耳versjon和scope都读取自父pom;
* 如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

[2020年解决：IDEA中已配置阿里镜像，但maven无法下载jar包的问题_傲娇的何先生的博客-CSDN博客](https://blog.csdn.net/HeyWeCome/article/details/104543411)

# 二、Rest微服务工程构建步骤

## 1. cloud-provider-payment8001微服务提供者**支付Module模块**

### 1.1 建cloud-provider-payment8001 Module

创建完成后请回到父工程查看pom文件变化

### 1.2 改pom文件： cloud-provider-payment8001依赖

[ cloud-provider-payment8001依赖](https://www.notion.so/cloud-provider-payment8001-3dfea6ca77174dc98e20254fda3d07e3)

### 1.3 写YML

```yaml
server:
  port: 8001
spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://localhost:3306/cloud2020?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: root

mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.youliao.springcloud.entities
```

### 1.4 主启动

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

### 1.5 业务类-建表SQL、entities、dao、service、controller

![本地Win10环境安装的MySQL版本为：Server version: 5.5.28 MySQL Community Server (GPL)  故须升级MySQL版本为：5.7及以上](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)

本地Win10环境安装的MySQL版本为：Server version: 5.5.28 MySQL Community Server (GPL)  故须升级MySQL版本为：5.7及以上

### 01_建表语句

[01_建表语句](https://www.notion.so/01_-ae824c8b63a146129f977a712a622a56)

### 02_entities

[entities](https://www.notion.so/entities-8e0724d9edf14f2289160494d79e0f4a)

### 03_dao

[接口PaymentDao和mybatis的映射文件](https://www.notion.so/PaymentDao-mybatis-81af4ead9db24e3dac64fe0857422ec4)

### 04_service

[service层](https://www.notion.so/service-b50ccf8b37c342b5a2f808a8036610b2)

### 05_controller

[controller层](https://www.notion.so/controller-4ba95bcf3cf543749d9e6d3a0461af95)

## 2、部署devtools配置步骤：热部署配置

### 1.   Adding devtools to your project 当前model引入pom依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
</dependency>
```

### 2.Adding plugin to your pom.xml

下一段配置黏贴到父工程当中的 pom 里

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>
```

### 3.Enabling automatic build

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

### 4.Update the value of

快捷键：ctrl + alt +shift +/

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

### 5.重启IDEA

## 3、cloud-consumer-order80 微服务消费者订单Module模块

### 整体的业务调用关系：

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)

代码在Idea工程文件的位置：

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)

```java
@RestController
@Slf4j
public class OrderController {
    public static final String PAYMENT_URL = "http://localhost:8001";
    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
    }

    @GetMapping("/consummer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
    }
}
```

我们的客户端消费80通过httpClient/restTemplate调用微服务提供者8001payment.

我们来看下什么是**RestTemplate:**

### RestTemplate官网地址：

[RestTemplate (Spring Framework 5.2.2.RELEASE API)](https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html)

### RestTemplate的作用：使用restTemplate用来访问restful接口

举例：

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)

url：REST请求地址，[http://localhost:8001](http://localhost:8001/)/payment/create

request：请求参数，

ResponseType： HTTP响应转换被转换的对象模型，CommonResult.class  （用来存放公共响应头信息的 报错错误信息，浏览器错误码等公共信息）

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)

# 此阶段的工程架构图：

![02_SpringCloud%20%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%E6%A8%A1%E5%9D%97%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%BC%96%E7%A0%81%E6%9E%84%E5%BB%BA%E2%80%94%E2%80%94%E6%90%AD%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AASpringCloud%E5%B7%A5%E7%A8%8B%205cb47b59862244efb921bec4c66f8f06/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)