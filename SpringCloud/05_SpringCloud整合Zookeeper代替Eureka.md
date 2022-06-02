# 05_SpringCloud整合Zookeeper代替Eureka

# 注册中心：Zookeeper

Zookeeper是一个分布式协调工具，可以实现注册中心功能

安装地址： `/myzookeeper/zookeeper-3.4.9/bin`

查看CentOS 7中的ip: 192.168.187.150

![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212669.png)

查看Windows的Ip地址：192.168.0.106

我们在CentOS中ping Windows的ip 查看能否ping的通。

![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212672.png)

我们在Windows中pingCentOS的ip地址，可以ping通，即正常。

![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212673.png)

服务架构

# 服务架构图：

![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212674.png)

# 服务提供者：cloud-provider-payment8004 注册到zookeeper-附带zk的安装与调试

- 1、新建cloud-provider-payment8004 Module
- 2、改POM文件
  
    ```xml
    <dependencies>
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
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery -->
            <dependency> <!--SpringCloud整合zookeeper客户端-->
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
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
    
- 3、写YML
  
    ```yaml
    #8004表示注册到zookeeper服务器的支付服务提供者端口号
    server:
      port: 8004
    
    #服务别名----注册zookeeper到注册中心名称
    spring:
      application:
        name: cloud-provider-payment
      cloud:
        zookeeper:
          connect-string: 192.168.187.150:2181
    
    ```
    
- 4、主启动
  
    ```java
    @SpringBootApplication
    [@EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务](https://www.notion.so/04_-Eureka-eureka-24fed6d072c54377b7d749dd3f6cc13f)
    public class PaymentMain8004 {
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain8004.class,args);
        }
    }
    ```
    
- 5、启动8004注册进zookeeper
  
    ## 5.1 CentOS 启动zookeeper服务
    
    目录：`/myzookeeper/zookeeper-3.4.9/bin`
    
    bin目录中输入 `./zkServer.sh start` 命令，敲回车尝试启动zookeeper服务。
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212675.png)
    
    提示权限不够，那么我们就需要通过`chmod a+xwr zkServer.sh`  命令，给zkServer.sh赋予其权限，然后再次启动`./zkServer.sh start` 命令，提示：
    
    > ZooKeeper JMX enabled by default
    Using config: /myzookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    grep: /myzookeeper/zookeeper-3.4.9/bin/../conf/**zoo.cfg**: 没有那个文件或目录
    mkdir: 无法创建目录"": 没有那个文件或目录
    > 
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212676.png)
    
    进入 zookeeper-3.4.9 目录，使用命令`mkdir data`创建 data 文件夹。
    
    所在地址：/myzookeeper/zookeeper-3.4.9/data
    
    **进入conf目录**，修改**zoo_sample.cfg**文件中的data 属性：**dataDir=/myzookeeper/zookeeper-3.4.9/data**
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212677.png)
    
    cd conf，然后将**zoo_sample.cfg文件修改成zoo.cfg: 修改文件夹命令：mv zoo_sample.cfg zoo.cfg**
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212678.png)
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212679.png)
    
    再次启动：`./zkServer.sh start` 一切正常。
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212680.png)
    
    链接服务：bin目录下使用命令 `./zkCli.sh` 
    
    同样提示权限不够，使用命令：`chmod a+xwr` `[zkCli.sh](http://zkcli.sh)`   chmod a+xwr [zkCli.sh](http://zkcli.sh/) 添加权限
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212681.png)
    
    添加完之后的权限是这样子的，有权限的是绿色的。我们再次尝试启动一下`./zkCli.sh` 
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212682.png)
    
    zookeeper端口号：2181
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212683.png)
    
    zookeeper节点命令：
    
    启动: `./zkServer.sh start` 
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212684.png)
    
    使用命令: `./zkCli.sh`
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212685.png)
    
    命令`ls /`：`[zk: localhost:2181(CONNECTED) 4] ls /`
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212686.png)
    
    命令：`get /zookeeper`
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212687.png)
    
    查看zookeeper进程命令： `ps -ef|grep zookeeper`
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212688.png)
    
    结束zookeeper进程命令：`kill -9 43641`
    
    ## 5.2 启动8004服务后jar包冲突问题
    
    意思就是我们自己用的zookeeper版本是3.4.9的一个版本，而服务器自带的zookeeper的版本比较新（3.5.3的一个版本），导致版本不一致。
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212689.png)
    
    ### 5.2.1 解决zookeeper版本jar包冲突问题
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212690.png)
    
    ### 5.2.2 排出zookeeper冲突后的新POM [单独使用zookeeper时需要排除自带的版本和自带的日志依赖]
    
    ```xml
    <dependencies>
            <dependency> <!--引入自定义的api通用包，可以使用Payment支付Entities-->
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
                <exclusions> <!--先排除自带的zookeeper3.5.3-->
                    <exclusion>
                        <groupId>org.apache.zookeeper</groupId>
                        <artifactId>zookeeper</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
    
            <!--添加zookeeper3.4.14版本-->
            <dependency>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>3.4.9</version>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
            <dependency> <!--然后单独引入zookeeper时还需排除其自带的日志依赖，否则启动报错，和spring自带的日志冲突了。-->
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>3.4.9</version>
                <exclusions>
                    <exclusion>
                        <artifactId>slf4j-log4j12</artifactId>
                        <groupId>org.slf4j</groupId>
                    </exclusion>
                </exclusions>
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
    
    <!--        &lt;!&ndash; https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery &ndash;&gt;-->
    <!--        <dependency> &lt;!&ndash;SpringCloud整合zookeeper客户端&ndash;&gt;-->
    <!--            <groupId>org.springframework.cloud</groupId>-->
    <!--            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>-->
    <!--        </dependency>-->
    
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
    
    然后单独引入zookeeper时还需排除其自带的日志依赖，否则启动报错，和spring自带的日志冲突了。再次启动Idea中的8004服务，看是否还是报错！
    
    ### **启动**
    
    启动成功后，把服务注册进Zookeeper客户端
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212691.png)
    
    说明我们的支付模块已经成功入住进去我们的zookeeper服务器。
    
    ### 5.2.3 测试：
    
    验证测试1：[http://localhost:8004/payment/zk](http://localhost:8004/payment/zk)
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212692.png)
    
    验证测试2：[https://tool.lu/json](https://tool.lu/json)
    
    ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212693.png)
    

思考？

<aside>
💡 服务已经成功注册到Zookeeper客户端，那么注册上去的节点被称为**临时节点**，还是持久节点？

</aside>

首先Eureka有自我保护机制，也就是某个服务下线后，不会立刻清除该服务，而是将服务保留一段时间。

Zookeeper也一样在服务下线后，会等待一段时间后，也会把该节点删除，这就说明Zookeeper上的节点是**临时节点。**

![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212694.png)

# 服务消费者：cloud-consumerzk-order80注册到zookeeper

- 1、建cloud-consumerzk-order80 Module
- 2、改POM
  
    ```xml
    <artifactId>cloud-consumerzk-order80</artifactId>
    
        <dependencies>
            <dependency> <!--引入自定义的api通用包，可以使用Payment支付Entities-->
                <groupId>com.youliao.springcloud</groupId>
                <artifactId>cloud-api-commons</artifactId>
                <version>${project.version}</version>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
                <exclusions> <!--先排除自带的zookeeper3.5.3-->
                    <exclusion>
                        <groupId>org.apache.zookeeper</groupId>
                        <artifactId>zookeeper</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            <!--添加zookeeper3.4.14版本-->
            <dependency>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>3.4.9</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
            <dependency> <!--然后单独引入zookeeper时还需排除其自带的日志依赖，否则启动报错，和spring自带的日志冲突了。-->
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>3.4.9</version>
                <exclusions>
                    <exclusion>
                        <artifactId>slf4j-log4j12</artifactId>
                        <groupId>org.slf4j</groupId>
                    </exclusion>
                </exclusions>
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
    
            <!--        &lt;!&ndash; https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery &ndash;&gt;-->
            <!--        <dependency> &lt;!&ndash;SpringCloud整合zookeeper客户端&ndash;&gt;-->
            <!--            <groupId>org.springframework.cloud</groupId>-->
            <!--            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>-->
            <!--        </dependency>-->
    
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
    
- 3、写YML
  
    ```yaml
    #8004表示注册到zookeeper服务器的支付服务提供者端口号
    server:
      port: 80
    
    #服务别名----注册zookeeper到注册中心名称
    spring:
      application:
        name: cloud-consumer-order
      cloud:
        #注册到zookeeper地址
        zookeeper:
          connect-string: 192.168.187.150:2181
    ```
    
- 4、主启动
  
    ```java
    @SpringBootApplication
    @EnableDiscoveryClient
    public class OrderZKMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderZKMain80.class, args);
        }
    }
    ```
    
- 5、业务类
    - 5.1配置Bean
      
        ```java
        @Configuration
        public class ApplicationContextConfig {
            @Bean
            @LoadBalanced
            public RestTemplate getRestTemplate() {
                return new RestTemplate();
            }
        }
        ```
        
    - 5.2 Controller
      
        ```java
        @RestController
        @Slf4j
        public class OrderZKController {
            public static final String INVOKE_URL = "http://cloud-provider-payment";
        
            @Resource
            private RestTemplate restTemplate;
        
            @GetMapping(value = "/consumer/payment/zk")
            public String paymentInfo() {
                String result = restTemplate.getForObject(INVOKE_URL + "/payment/zk", String.class);
                return result;
            }
        }
        ```
    
- 6、验证测试
    - 6.1 CentOS7 中测试：说明order80和payment8004都注册到了我们的zookeeper上面
      
        ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212695.png)
        
    - 6.2 访问测试地址：
      
        地址1：[http://localhost:8004/payment/zk](http://localhost:8004/payment/zk)
        
        ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212696.png)
        
        地址2：[http://localhost/consumer/payment/zk](http://localhost/consumer/payment/zk)
        
        ![05_SpringCloud%E6%95%B4%E5%90%88Zookeeper%E4%BB%A3%E6%9B%BFEureka%20566ee2bfc9f3442b8e64125c88168529/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206022212697.png)