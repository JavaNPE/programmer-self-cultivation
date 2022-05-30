# 04_分布式组件-SpringCloud Alibaba-Nacos、OpenFeign、Gateway网关学习笔记

# 20、分布式组件-SpringCloud Alibaba简介

[15_SpringCloud Alibaba入门简介及相关学习资料](https://www.notion.so/15_SpringCloud-Alibaba-d6303e69dafd4f0c83ae5fe937e11fc5)

[spring-cloud-alibaba/README-zh.md at master · alibaba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)

## SpringCloud的几大痛点：

> SpringCloud部分组件停止维护和更新，给开发带来不便;
SpringCloud部分环境搭建复杂，没有完善的可视化界面，我们需要大量的二次开发和定制
SpringlCloud配置复杂，难以上手，部分配置差别难以区分和合理应用
> 

## SpringCloud Alibaba的优势:

> 阿里使用过的组件经历了考验，性能强悍，设计合理，现在开源出来大家用
成套的产品搭配完善的可视化界面给开发运维带来极大的便利
搭建简单，学习曲线低。

## 结合SpringCloud Alibaba我们最终的技术搭配方案：

> **SpringCloud Alibaba- Nacos:注册中心(服务发现/注册)
SpringCloud Alibaba . Nacos:配置中心(动态配置管理)**

SpringCloud- Ribbon: 负载均衡
SpringCloud- Feign: 声明式HTTP客户端(调用远程服务)
**SpringCloud Alibaba - Sentinel: 服务容错(限流、降级、熔断)**

SpringCloud - Gateway:  API 网关(webflux 编程模式)
SpringCloud- Sleuth: 调用链监控
**SpringCloud Alibaba- Seata: 原Fescar,即分布式事务解决方案**



## 项目统一Springcloud和Springboot版本

### Springcloud版本：`Greenwich.SR3`

### Springboot版本：`2.1.8.RELEASE`

### SpringCloud Alibaba版本：`2.1.0.RELEASE`

## Spring Cloud版本选型

[Spring Cloud](https://spring.io/projects/spring-cloud#overview)

spring cloud与springboot的版本选择

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

在gulimall-commonModule中的pom.xml中加入依赖：

```xml
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
</dependencyManagement>
```

上面是依赖管理，相当于以后再dependencies里引spring cloud alibaba就不用写版本号， 全用dependencyManagement进行管理。注意他和普通依赖的区别，他只是备注一下，并没有加入依赖。

## SpringCloud Alibaba组件版本说明：

[版本说明 · alibaba/spring-cloud-alibaba Wiki](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

# 21、分布式组件-SpringCloud Alibaba-Nacos注册中心

## Nacos注册中心文档

[spring-cloud-alibaba/spring-cloud-alibaba-examples/nacos-example/nacos-discovery-example at master · alibaba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba/tree/master/spring-cloud-alibaba-examples/nacos-example/nacos-discovery-example)

Nacos注册中心文档

其他文档在该项目上层即可找到，下面读一读官网给的介绍就会用了。

安装启动nacos：下载–解压–双击`bin/startup.cmd`。`http://127.0.0.1:8848/nacos/` 账号密码nacos

## 项目中使用nacos步骤：

- 0、启动nacos
- 1、添加nacos的pom依赖
  
    ```xml
    <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    ```
    
- 2、改YML
  
    ```yaml
    spring:
      datasource:
        username: root
        password: root
        url: jdbc:mysql://192.168.56.10:3306/gulimall_sms
        driver-class-name: com.mysql.jdbc.Driver
      **cloud:
        nacos:
          discovery:
            server-addr: 127.0.0.1:8848
      application:   #注册服务名
        name: gulimall-coupon
        # name: gulimall-member**
    
    # MapperScan
    # sql映射文件位置
    mybatis-plus:
      mapper-locations: classpath:/mapper/**/*.xml
      #主键自增
      global-config:
        db-config:
          id-type: auto
    
    server:
      port: 7000
    ```
    
- 3、主启动添加注解：`@EnableDiscoveryClient`
  
    

![`http://127.0.0.1:8848/nacos/` ](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

`http://127.0.0.1:8848/nacos/` 

### 启动应用，观察nacos服务列表是否已经注册上服务

> 注意：每一个应用都应该有名字，这样才能注册上去。修改application.properties/yml文件
`spring.application.name=serviceprovider
server.port 8000`

然后依次给member、配置上面的yaml，改下name就行。再给每个项目配置类上加上注解@EnableDiscoveryClient

### nacos测试：测试member和coupon的远程调用

想要获取当前会员领取到的所有优惠券。先去注册中心找优惠券服务，注册中心调一台优惠券服务器给会员，会员服务器发送请求给这台优惠券服务器，然后对方响应。

服务请求方发送了2次请求，先问nacos要地址，然后再请求

# 22、分布式组件-SpringCloud-OpenFeign测试远程调用

[08_OpenFeign服务接口调用](https://www.notion.so/08_OpenFeign-65ffd7848ba14c3b8011e456d334da56)

## Feign 声明式远程调用简介

Feign是一个声明式的HTTP客户端，它的目的就是让远程调用更加简单。Feign 提供了HTTP请求的模板，通过编写简单的接口和插入注解，就可以定义好HTTP请求的参数、格式、地址等信息。

**Feign整合了Ribbon (负载均衡)和Hystrix(服务熔断)**，可以让我们不再需要显式地使用这两个组件。

SpringCloudFeigd在NetflixFeign的基础，上扩展了对SpringMVC注解的支持，在其实现下，我们只需创建-一个接口并用注解的方式来配置它，即可完成对服务提供方的接口绑定。简化了SpringCloudRibbon自行封装服务调用客户端的开发量。

会员服务（gulimall-member）想要远程调用优惠券服务（gulimall-coupon），只需要给会员服务（gulimall-member）里引入**openfeign依赖**，他就有了远程调用其他服务的能力。

- 1、gulimall-member中的 POM 文件添加以下依赖
  
    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    ```
    

我们之前在gulimall-member的pom.xml已经引用过了（微服务）。

- 2、在gulimall-coupon Module中修改CouponController类如下的内容
  
    ```java
    @RequestMapping("coupon/coupon")
    public class CouponController {
        @Autowired
        private CouponService couponService;
    
        @RequestMapping("/member/list")
        public R membercoupons(){    //全系统的所有返回都返回R
            // 应该去数据库查用户对于的优惠券，但这个我们简化了，不去数据库查了，构造了一个优惠券给他返回
            CouponEntity couponEntity = new CouponEntity();
            couponEntity.setCouponName("满100-10");//优惠券的名字
            return R.ok().put("coupons",Arrays.asList(couponEntity));
        }
    
    ```
    
    这样我们准备好了优惠券的调用内容
    
- 3、在gulimall-member的`gulimallMemberApplication` 配置类上加注解`@EnableDiscoveryClient，`告诉member是一个远程调用客户端，gulimall-member要调用东西的
  
    ```java
    /*
    * 想要远程调用的步骤：
    * 1 引入openfeign
    * 2 编写一个接口，接口告诉springcloud这个接口需要调用远程服务
    * 	2.1 在接口里声明@FeignClient("gulimall-coupon")他是一个远程调用客户端且要调用coupon服务
    * 	2.2 要调用coupon服务的/coupon/coupon/member/list方法
    * 3 开启远程调用功能 @EnableFeignClients，要指定远程调用功能放的基础包
    * */
    @EnableFeignClients(basePackages="com.atguigu.gulimall.member.feign")//扫描接口方法注解
    @EnableDiscoveryClient
    @SpringBootApplication
    public class gulimallMemberApplication {
    
    	public static void main(String[] args) {
    		SpringApplication.run(gulimallMemberApplication.class, args);
    	}
    }
    
    ```
    
- 4、那么要调用什么东西呢？就是我们刚才写的优惠券的功能，复制函数部分，在`gulimall-member`的`com.atguigu.gulimall.member.feign`包下新建类：
  
    `@FeignClient + @RequestMapping`构成远程调用的坐标
    
    其他类中看似只是调用了`CouponFeignService.membercoupons()`，而实际上该方法跑去nacos里和rpc里调用了才拿到东西返回
    
    ```java
    @FeignClient("gulimall-coupon") //告诉spring cloud这个接口是一个远程客户端，要调用coupon服务(nacos中找到)，具体是调用coupon服务的/coupon/coupon/member/list对应的方法
    public interface CouponFeignService {
        // 远程服务的url
        @RequestMapping("/coupon/coupon/member/list")//注意写全优惠券类上还有映射//注意我们这个地方不是控制层，所以这个请求映射请求的不是我们服务器上的东西，而是nacos注册中心的
        public R membercoupons();//得到一个R对象
    }
    ```
    
- 5、然后我们在gulimall-member的MemberController 控制层写一个测试请求
  
    ```java
    @RestController
    @RequestMapping("member/member")
    public class MemberController {
        @Autowired
        private MemberService memberService;
    
        @Autowired
        CouponFeignService couponFeignService;
    
        @RequestMapping("/coupons")
        public R test(){
            MemberEntity memberEntity = new MemberEntity();
            memberEntity.setNickname("会员昵称张三");
            R membercoupons = couponFeignService.membercoupons();//假设张三去数据库查了后返回了张三的优惠券信息
    
            //打印会员和优惠券信息
            return R.ok().put("member",memberEntity).put("coupons",membercoupons.get("coupons"));
        }
    
    ```
    
- 6、重新启动服务：GulimallCouponApplication :7000和GulimallMemberApplication :8000服务
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)
    
- 7、浏览器访问：[http://localhost:8000/member/member/coupons](http://localhost:8000/member/member/coupons)
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)
    

## Feign的使用步骤：

- 1、想要远程调用别的服务
  
         1)、引入open-feign依赖
    
    2)、编写一个接口，告诉SpringCloud这个接口需要调用远程服务
    
    1、声明接口的每一个方法都是调用哪个远程服务的那个请求
    
    3)、开启远程调用功能
    

> 想要远程调用的步骤：
1 引入openfeign
2 编写一个接口，接口告诉springcloud这个接口需要调用远程服务
2.1 在接口里声明@FeignClient("gulimall-coupon")他是一个远程调用客户端且要调用coupon服务
2.2 要调用coupon服务的/coupon/coupon/member/list方法
3 开启远程调用功能 @EnableFeignClients，要指定远程调用功能放的基础包
> 

# 23、分布式组件-SpringCloud Alibaba-Nacos配置中心-简单示例



## Nacos Config Example：Nacos作为配置中心案例

[spring-cloud-alibaba/readme-zh.md at master · alibaba/spring-cloud-alibaba](https://github.com/alibaba/spring-cloud-alibaba/blob/master/spring-cloud-alibaba-examples/nacos-example/nacos-config-example/readme-zh.md)

nacos官方文档

- 1、首先，修改 pom.xml 文件，引入 Nacos Config Starter。谷粒商城项目由于把gulimall-common作为公共项目，统一管理所有微服务，故只需在gulimall-common中引用此依赖，其他微服务自动将会自动引用。
  
    ```xml
    <dependency>
         <groupId>com.alibaba.cloud</groupId>
         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
     </dependency>
    ```
    
- 2、在应用的 /src/main/resources/bootstrap.properties 配置文件中配置 Nacos Config 元数据，
  
    在`gulimall-coupon`项目中创建`/src/main/resources/**bootstrap.properties**` ，这个文件是springboot里规定的，他优先级别application.properties高
    
    ```yaml
    # 改名字，对应nacos里的配置文件名
    spring.application.name=gulimall-coupon
    # nacos配置中心地址
    spring.cloud.nacos.config.server-addr=127.0.0.1:8848
    ```
    
    还是原来我们使用配置的方式，只不过优先级变了，所以匹配到了nacos的配置
    
- 3、完成上述两步后，应用会从 Nacos Config 中获取相应的配置，并添加在 Spring Environment 的 PropertySources 中。这里我们使用 `@Value` 注解来将对应的配置注入到 SampleController 的 userName 和 age 字段，并添加 `@RefreshScope` 打开动态刷新功能
  
    ```java
    @RefreshScope
    class SampleController {
    
     	@Value("${user.name}")
     	String userName;
    
     	@Value("${user.age}")
     	int age;
    }
    ```
    
    ```java
    @RefreshScope   //@RefreshScope 打开动态刷新功能
    @RestController
    @RequestMapping("coupon/coupon")
    public class CouponController {
        @Autowired
        private CouponService couponService;
    
        @Value("${coupon.user.name}")
        private String name;
    
        @Value("${coupon.user.age}")
        private Integer age;
    
        @RequestMapping("/test")
        public R test() {
            return R.ok().put("name",name).put("age",age);
        }
    ```
    
- 4、先在本地文件**application.properties配置文件**中进行如下信息进行测试，**或者**在**nacos配置中心**进行以下配置。【**如果配置中心和当前应用的配置文件中都配置了相同的项，优先使用配置中心的配置。**】
  
    ```yaml
    coupon.user.name=wangdali.in application.properties
    coupon.user.age=23
    ```
    
    ![方式二：nacos配置中心：配置列表中添加以下配置信息【优先使用】](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)
    
    方式二：nacos配置中心：配置列表中添加以下配置信息【优先使用】
    
- 5、启动GulimallCouponApplication :7000/服务
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)
    
- 6、浏览器中访问：[http://localhost:7000/coupon/coupon/test](http://localhost:7000/coupon/coupon/test)
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)
    

# 24、分布式组件SpringCloud Alibaba Nacos配置中心命名空间与配置分组

## 2、Nacos作为配置中心-分类配置

## 在nacos浏览器中还可以配置：

### 2.1 命名空间：用作配置隔离。（一般每个微服务一个命名空间）

> 默认public。默认新增的配置都在public空间下
> 

> 开发、测试、开发可以用命名空间分割。properties每个空间有一份。
> 

> 在bootstrap.properties里配置（测试完去掉，学习不需要）
> 

```yaml
# 改名字，对应nacos里的配置文件名
spring.application.name=gulimall-coupon
# nacos配置中心地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848

**# 可以选择对应的命名空间 # 写上对应环境的命名空间ID：测试的时nacos配置中心的prop环境
spring.cloud.nacos.config.namespace=06e5773d-6438-471e-9f70-31fd20ed98dc**
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

### 2.2 配置集：一组相关或不相关配置项的集合。

### 2.3 配置集ID：类似于配置文件名，即Data ID

### 2.4 配置分组：默认所有的配置集都属于`DEFAULT_GROUP`。双十一，618的优惠策略改分组即可

```yaml
# 更改配置分组
spring.cloud.nacos.config.group=DEFAULT_GROUP
```

### 谷粒商城项目最终方案：每个微服务创建自己的命名空间，然后使用配置分组区分环境（dev/test/prod）

# 25、分布式组件-SpringCloud Alibaba-Nacos配置中心-加载多配置集



我们要把原来application.yml里的内容都分文件抽离出去。我们在nacos里创建好后，在coupons里指定要导入的配置即可。

- bootstrap.properties：文件路径`D:\develop\workspace\gulimall\gulimallcoupon\src\main\resources\bootstrap.properties`
  
    ```yaml
    # 改名字，对应nacos里的配置文件名
    spring.application.name=gulimall-coupon
    # nacos配置中心地址
    spring.cloud.nacos.config.server-addr=127.0.0.1:8848
    ## 可以选择对应的命名空间 # 写上对应环境的命名空间ID：测试的时nacos配置中心的prop环境
    spring.cloud.nacos.config.namespace=c1563673-9144-490f-b49d-53e59d1c9ed3
    ## 设置不同组 # 更改配置分组
    spring.cloud.nacos.config.group=prod
    spring.cloud.nacos.config.ext-config[0].data-id=datasource.yml
    spring.cloud.nacos.config.ext-config[0].group=dev
    spring.cloud.nacos.config.ext-config[0].refresh=true
    
    spring.cloud.nacos.config.ext-config[1].data-id=mybatis.yml
    spring.cloud.nacos.config.ext-config[1].group=dev
    spring.cloud.nacos.config.ext-config[1].refresh=true
    
    spring.cloud.nacos.config.ext-config[2].data-id=other.yml
    spring.cloud.nacos.config.ext-config[2].group=dev
    spring.cloud.nacos.config.ext-config[2].refresh=true
    
    ----------------------------不同版本略有不同本项目用的上面的---------------------------------
    # 在其中用数组spring.cloud.nacos.config.extension-configs[]写明每个配置集
    
    spring.cloud.nacos.config.extension-configs[0].data-id=datasource.yml
    spring.cloud.nacos.config.extension-configs[0].group=dev
    spring.cloud.nacos.config.extension-configs[0].refresh=true
    
    spring.cloud.nacos.config.extension-configs[1].data-id=mybatis.yml
    spring.cloud.nacos.config.extension-configs[1].group=dev
    spring.cloud.nacos.config.extension-configs[1].refresh=true
    
    spring.cloud.nacos.config.extension-configs[2].data-id=other.yml
    spring.cloud.nacos.config.extension-configs[2].group=dev
    spring.cloud.nacos.config.extension-configs[2].refresh=true
    ```
    
- 启动：GulimallCouponApplication :7000/微服务：idea控制台输出内容
  
    ```yaml
    2021-08-15 16:43:10.115  INFO 30204 --- [           main] c.a.c.n.c.NacosPropertySourceBuilder     : Loading nacos data, dataId: 'datasource.yml', group: 'dev'
    2021-08-15 16:43:10.129  INFO 30204 --- [           main] c.a.c.n.c.NacosPropertySourceBuilder     : Loading nacos data, dataId: 'mybatis.yml', group: 'dev'
    2021-08-15 16:43:10.134  INFO 30204 --- [           main] c.a.c.n.c.NacosPropertySourceBuilder     : Loading nacos data, dataId: 'other.yml', group: 'dev'
    2021-08-15 16:43:10.139  INFO 30204 --- [           main] c.a.c.n.c.NacosPropertySourceBuilder     : Loading nacos data, dataId: 'gulimall-coupon.properties', group: 'prod'
    2021-08-15 16:43:10.140  INFO 30204 --- [           main] b.c.PropertySourceBootstrapConfiguration : Located property source: CompositePropertySource {name='NACOS', propertySources=[NacosPropertySource {name='gulimall-coupon.properties'}, NacosPropertySource {name='other.yml'}, NacosPropertySource {name='mybatis.yml'}, NacosPropertySource {name='datasource.yml'}]}
    2021-08-15 16:43:10.143  INFO 30204 --- [           main] c.a.g.coupon.GulimallCouponApplication   : No active profile set, falling back to default profiles: default
    ```
    
- nacos配置列表中我们进行以下配置
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)
    
- 浏览器访问：[http://localhost:7000/coupon/coupon/list](http://localhost:7000/coupon/coupon/list)
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)
    

# 26、分布式组件-SpringCloud-Gateway网关核心概念&原理

## 微服务-注册中心-配置中心-网关

![image-20220530152903147](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530152903147.png)

## 没有使用网关API网关

![image-20220530152951501](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530152951501.png)



## 使用使用api网关之后

![使用api网关之后](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)



## gateway官网手册：

[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/)

## Gateway网关简介：网关使用的是Netty

**网关作为流量的入口**，常用功能**包括路由转发**、**权限校验限流控制**等。而springcloud gateway
作为SpringCloud 官方推出的第二代网关框架，取代了Zuul网关。

**动态上下线：**发送请求需要知道商品服务的地址，如果商品服务器有123服务器，1号掉线后，还得改，所以需要网关动态地管理，他能从注册中心中实时地感知某个服务上线还是下线。【先通过网关，网关路由到服务提供者】

**拦截：**请求也要加上询问权限，看用户有没有权限访问这个请求，也需要网关。

所以我们使用spring cloud的`gateway`组件做网关功能。

网关是请求流量的入口，常用功能包括路由转发，权限校验，限流控制等。springcloud gateway取代了zuul网关。

[https://spring.io/projects/spring-cloud-gateway](https://spring.io/projects/spring-cloud-gateway)

参考手册：[https://cloud.spring.io/spring-cloud-gateway/2.2.x/reference/html/](https://cloud.spring.io/spring-cloud-gateway/2.2.x/reference/html/)

## gateway三大核心概念：**Route路由、Predicate断言、Filter过滤**

**Route路由:** The basic building block of the gateway. It is defined by an ID, a destination URI, a collection of predicates断言, and a collection of filters. A route is matched if the aggregate predicate is true.发一个请求给网关，网关要将请求路由到指定的服务。路由有id，目的地uri，断言的集合，匹配了断言就能到达指定位置。

**Predicate断言:** This is a Java 8 Function Predicate. The input type is a Spring Framework ServerWebExchange. This lets you match on anything from the HTTP request, such as headers or parameters.就是java里的断言函数，匹配请求里的任何信息，包括请求头等。根据请求头路由哪个服务。

**Filter过滤:** These are instances of Spring Framework GatewayFilter that have been constructed with a specific factory. Here, you can modify requests and responses before or after sending the downstream request.过滤器请求和响应都可以被修改。

客户端发请求给服务端。中间有网关。先交给映射器，如果能处理就交给handler处理，然后交给一系列filer，然后给指定的服务，再返回回来给客户端。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

有很多断言。

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - Cookie=mycookie,mycookievalue

```

`-`代表数组，可以设置Cookie等内容。只有断言成功了，才路由到指定的地址。

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - name: Cookie
          args:
            name: mycookie
            regexp: mycookievalue

```

创建，使用initilizer，Group：com.atguigu.gulimall，Artifact： gulimall-gateway，package：com.atguigu.gulimall.gateway。 搜索gateway选中。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

gulimall-gateway模块中的pom.xml里加上common依赖， 修改springboot和springcloud的版本，

```xml
<dependency>
       <groupId>com.atguigu.gulimall</groupId>
       <artifactId>gulimall-common</artifactId>
       <version>0.0.1-SNAPSHOT</version>
</dependency>
```

在gulimall-gateway服务中主启动开启注册服务发现`@EnableDiscoveryClient`，配置nacos注册中心地址`applicaion.properties`。这样gateway也注册到了nacos中，其他服务就能找到nacos，网关也能通过nacos找到其他服务

```yaml
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
spring.application.name=gulimall-gateway
server.port=88
```

在nacos新建gateway命名空间：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

`bootstrap.properties` 填写nacos配置中心地址

```yaml
spring.application.name=gulimall-gateway
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
spring.cloud.nacos.config.namespace=819b8c98-2dc6-42dc-9c6a-2ea829929ad3
```

本项目在nacos中的服务名

```yaml
spring:
    application:
        name: gulimall-gateway
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

- 在gulimall-gateway模块下新建application.yml文件：根据条件转发到uri等
  
    ```yaml
    spring:
      cloud:
        gateway:
          routes:
            - id: test_route  # - : 表示数组
              uri: https://www.baidu.com
              predicates: #所有的断言规则
                #- Query=rul  #如果我们有‘rul’这个参数，那么我们就去‘https://www.baidu.com’这个地址
                - Query=url,baidu    # 如果我们带 了“baidu”，我们就跳转“https://www.baidu.com”
            - id: qq_route
              uri: https://www.qq.com
              predicates:
                - Query=url,qq
    ```
    
    ```yaml
    spring:
      cloud:
        gateway:
          routes:
            - id: test_route    # - : 表示数组
              uri: https://www.zhihu.com
              predicates: #所有的断言规则
                #- Query=rul  #如果我们有‘rul’这个参数，那么我们就去‘https://www.zhihu.com’这个地址
                - Query=url,zhihu  #如果我们带 了“zhihu”，我们就跳转“https://www.zhihu.com”
    
            - id: jd_route
              uri: https://www.jd.com
              predicates:
                - Query=url,jd
    ```
    
- 测试1：[http://localhost:88/hello?url=jd](http://localhost:88/hello?url=jd)  等于 https://www.jd.com/hello
- 测试2：[http://localhost:88/hello?url=zhihu](http://localhost:88/hello?url=zhihu) 等于www.zhihu.com/hello