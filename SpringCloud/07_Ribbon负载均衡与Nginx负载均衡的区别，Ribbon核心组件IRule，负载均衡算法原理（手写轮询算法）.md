# 07_Ribbon负载均衡与Nginx负载均衡的区别，Ribbon核心组件IRule，负载均衡算法原理（手写轮询算法）




# [Ribbon相关面试：](https://www.notion.so/07_Ribbon-5272703b75ec4cdaac65770738ac26c8)

1、面试的考官会问你，你除了用过 **轮询** 这样的负载均衡还有没有用过其他的。如果你要用Ribbon的话，我们想换一种路由算法，轮询/负载均衡算法，你有没有自己写过？有没有自己换过？说说你的设计方案和思路。

# 一、Ribbon相关概述：

## Ribbon是什么：

> 一句话就是：RIbbon = 负载均衡 + RestTemplate调用
> 

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套**客户端(80端口服务消费端) 负载均衡的工具**。
简单的说，Ribbon是Netflix发布的开源项目， 主要功能是提供**客户端的软件负载均衡算法和服务调用**。

Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer (简称LB)后面所有的机器, Ribbon会自动的帮助你基于某种规则(如简单轮询,随机连接等)去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。

## Ribbon官网资料：

- 官网资料：[https://github.com/Netflix/ribbon/wiki/Getting-Started](https://github.com/Netflix/ribbon/wiki/Getting-Started)
  
    官网上显示Ribbon目前也进入维护模式了，但是目前主流还是使用Ribbon。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600557.png)
    
- Ribbon未来替代方案：*Spring Cloud想通过LoadBalancer用于替换Ribbon*
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600558.png)
    

## Ribbon的作用：

Ribbon的作用主要是(LB)负载均衡。

### LB负载均衡(Load Balance)是什么

简单的说就是将用户的请求平摊的分配到多个服务上,从而达到系统的HA (高可用)。
常见的负载均衡有软件Nginx, LVS, 硬件F5等。

### Nginx服务端负载均衡的区别（**集中式LB**） VS Ribbon本地负载均衡客户端（**进程内LB**）

**Nginx是服务器负载均衡**，客户端所有请求都会交给nginx,然后由nginx实现转发请求。即负载均衡是由服务端实现的。 

**Ribbon本地负载均衡**，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现RPC远程服务调用技术。

Nginx服务端负载均衡可以简单的理解为，病人去医院看病（暂且口腔问题），先去挂号，然后被（Nginx）分配到了口腔科，但是口腔科不可能只有一个大夫，大夫之间也是轮班（Ribbon本地负载均衡）。

### LB负载均衡(Load Balance)又分为集中式LB和进程内LB

- **集中式LB**
  
    即在服务的消费方和提供方之间使用独立的LB设施(可以是硬件,如F5, 也可以是软件,如nginx), 由该设施负责把访问请求通过某种策略转发至服务的提供方。
    
- **进程内LB**
  
    **将LB逻辑集成到消费方，**消费方从服务注册中心获知有哪些地址可用,然后自己再从这些地址中选择出一个合适的服务器。
    **Ribbon就属于进程内LB，**它只是一个库,集成于消费方进程，消费方通过它来获取到服务提供方的地址。
    

# 二、Ribbon负载均衡演示：

## 1、Ribbon的负载均衡和**RestTemplate**调用

总结: Ribbon其实就是一个软负载均衡的客户端组件，他可以和其他所需请求的客户端（RestTemplate）结合使用，和eureka结合只是其中的一个实例。

- **1、架构说明：**
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600559.png)
    
- **2、pring-cloud-starter-netflix-eureika-client自带了spring-cloud-starter-ribbon引用**
  
    为什么Ribbon不用引用依赖也可以使用：新版的Eureka已经默认引入Ribbon了，不需要额外引入。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600560.png)
    
- **3、二说RestTemplate的使用**：如果公司用的Ribbon做负载均衡还是有必要了解RestTemplate的
    - **3.1 RestTemplate官网：**
      
        [RestTemplate (Spring Framework 5.2.2.RELEASE API)](https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600561.png)
        
    - **3.2** **RestTemplate的**`getForObject`方法和`getForEntity`方法的区别？
      
        `getForObject`：返回对象为响应体中数据转化成的对象，基本可以理解为Json。(推荐使用)
        
        `getForEntity`：返回对象为ResponseEntity对象，包含了响应中的一些重要信息，比如响应头、响应状态码、响应体等。
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600562.png)
        
        ```java
        @RestController
        @Slf4j
        public class OrderController {
            //    public static final String PAYMENT_URL = "http://localhost:8001";
            public static final String PAYMENT_URL = "http://cloud-payment-service";
        
            @Resource
            private RestTemplate restTemplate;
        
            @GetMapping("/consumer/payment/create")
            public CommonResult<Payment> create(Payment payment) {
                return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
        
                //postForObject/ postForEntity的区别：
                //return restTemplate.postForEntity(PAYMENT_URL + "/payment/create", payment, CommonResult.class).getBody();
            }
        
            /**
             * getForObject：返回对象为响应体中数据转化成的对象，基本可以理解为Json。
             *
             * @param id
             * @return
             */
            @GetMapping("/consumer/payment/get/{id}")
            public CommonResult<Payment> getPayment(@PathVariable("id") Long id) {
                return restTemplate.getForObject(PAYMENT_URL + "/payment/get/   " + id, CommonResult.class);
            }
        
            /**
             * getForEntity:返回对象为ResponseEntity对象，包含了响应中的一些重要信息，比如响应头、响应状态码、响应体等
             *
             * @param id
             * @return
             */
            @GetMapping("/consumer/payment/getForEntity/{id}")
            public CommonResult<Payment> getPayment2(@PathVariable("id") Long id) {
                ResponseEntity<CommonResult> entity = restTemplate.getForEntity(PAYMENT_URL + "/payment/get/   " + id, CommonResult.class);
                if (entity.getStatusCode().is2xxSuccessful()) {
                    log.info("***日志信息***" + entity.getStatusCode() + "\t" + entity.getHeaders());  //可以获取到一些信息，比如：响应状态码、响应头
                    return entity.getBody();
                } else {
                    return new CommonResult<>(444, "操作失败");
                }
            }
        }
        ```
        
        启动`OrderMain80` 服务 打开浏览器在地址栏中输入以下URL
        
        测试1：[http://localhost/consumer/payment/get/10](http://localhost/consumer/payment/get/10)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600563.png)
        
        测试2：[http://localhost/consumer/payment/getForEntity/10](http://localhost/consumer/payment/getForEntity/10)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600564.png)
        
        ![07_Ribbon%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A8%20cb7ef229ebc247b1b89cb83bcb76e2f7/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600565.png)
        
    - **[3.3 RestTemplate的**`postForObject` 方法和 `postForEntity` 方法的区别？](https://www.notion.so/07_Ribbon-5272703b75ec4cdaac65770738ac26c8)
      
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600566.png)
        
        ```java
        public static final String PAYMENT_URL = "http://cloud-payment-service";
        
            @Resource
            private RestTemplate restTemplate;
        
            @GetMapping("/consumer/payment/create")
            public CommonResult<Payment> create(Payment payment) {
                return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
                //postForObject/ postForEntity的区别：
                //return restTemplate.postForEntity(PAYMENT_URL + "/payment/create", payment, CommonResult.class).getBody();
            }
        ```
        
    

# 三、Ribbon核心组件lRule：

## Ribbon默认是使用`轮询`作为负载均衡算法

IRule：irule根据特定算法从服务列表中选取一个要访问的服务，IRule是一个接口。

```java
public interface IRule{

    public Server choose(Object key);
    
    public void setLoadBalancer(ILoadBalancer lb);
    
    public ILoadBalancer getLoadBalancer();    
}
```

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600567.png)

## Ribbon的核心组件IRule自带的负载规则有以下七种：轮询，随机，重试.....

- **RoundRobinRule：轮询（Ribbon默认的负载均衡算法）**
- RandomRule：随机
- RetryRule：先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用服务
- WeightedResponseTimeRule：对RoundRobinRule的扩展，响应速度越快的实例选择的权重越大，越容易被选择
- BestAvailableRule：会先过滤掉由于多次访问故障而处于短路跳闸状态的服务，然后选择一个并发量最小的服务
- AvailabilityFilteringRule：先过滤掉故障实例，在选择并发较小的实例
- ZoneAvoidanceRule：默认规则，符合判断server所在区域的性能和server的可用性选择服务器

## 如何替换Ribbon核心组件IRule的负载规则（默认是轮询）

- 1、修改cloud-consumer-order80
  
- 2、注意配置细节：
  
    官方文档明确给出了警告：这个自定义配置类（[MySelfRule](https://www.notion.so/07_Ribbon-5272703b75ec4cdaac65770738ac26c8)）不能放在`@ComponentScan`所扫描的当前包下以及子包下，否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享，达不到特殊化定制的目的了。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600568.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600569.png)
    
    ![07_Ribbon%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A8%20cb7ef229ebc247b1b89cb83bcb76e2f7/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600570.png)
    
- 3、新建package：com.youliao.myrule
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600571.png)
    
- 4、com.youliao.myrule包下新建MySelfRule规则类
  
    ```java
    package com.youliao.myrule;
    
    import com.netflix.loadbalancer.IRule;
    import com.netflix.loadbalancer.RandomRule;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    /**
     * @Author Dali
     * @Date 3/7/2021 下午 6:49
     * @Version 1.0
     * @Description MySelfRule规则类
     */
    @Configuration
    public class MySelfRule {
        @Bean
        public IRule myRule() {
            return new RandomRule();  //定为 “随机” 负载规则算法
        }
    }
    ```
    
- 5、主启动类添加@RibbonClient
  
    ```java
    package com.youliao.springcloud;
    
    import com.youliao.myrule.MySelfRule;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
    import org.springframework.cloud.netflix.ribbon.RibbonClient;
    
    /**
     * @Author Dali
     * @Date 30/6/2021 上午 12:46
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication
    @EnableEurekaClient
    @RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class) //作用：不用默认出厂的轮询算法，改成我们自定义的负载均衡算法。
    public class OrderMain80 {
        public static void main(String[] args) {
            SpringApplication.run(OrderMain80.class, args);
        }
    }
    ```
    
    @RibbonClient(name = "CLOUD-PAYMENT-SERVICE"，。。。）服务名必须与OrderController调用的URL保持一致（包括大小写）都要一致，否则体现不出随机算法的特点。
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600572.png)
    
- 6、测试：[http://localhost/consumer/payment/get/10](http://localhost/consumer/payment/get/10)
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600573.gif)
    
    注意看serverPort端口号8001和8002随机改变
    

# 四、Ribbon负载均衡算法原理：

## 负载均衡算法公式：

**rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标**，每次服务重启动后rest接口计数从1开始。 

- 服务器集群总数量：payment8001和payment8002  `2台机器`
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600574.png)
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600575.png)
    
    Ribbon负载均衡算法原理
    
    ```markdown
    
    假设现在有2台机器，同时 List = 2 instance（也就是服务注册列表中，有两台）
    1 % 2 = 1 -> index = list.get(1)
    
    2 % 2 = 0 -> index = list.get(0)
    
    3 % 2 = 1 -> index = list.get(1)
    
    ....
    ```
    
- 负载均衡源码：（简单描述：下标，用了一个**CAS+自旋锁**）
  
    我们查看RandomRule的源码发现，其实内部就是利用的取余的技术，同时为了保证同步机制，还是使用了AtomicInteger原子整型类 
    
    ```java
    public class RoundRobinRule extends AbstractLoadBalancerRule {
    
        private AtomicInteger nextServerCyclicCounter;
        private static final boolean AVAILABLE_ONLY_SERVERS = true;
        private static final boolean ALL_SERVERS = false;
    
        private static Logger log = LoggerFactory.getLogger(RoundRobinRule.class);
    
        public RoundRobinRule() {
            nextServerCyclicCounter = new AtomicInteger(0);
        }
    
        public RoundRobinRule(ILoadBalancer lb) {
            this();
            setLoadBalancer(lb);
        }
    
        public Server choose(ILoadBalancer lb, Object key) {
            if (lb == null) {
                log.warn("no load balancer");
                return null;
            }
    
            Server server = null;
            int count = 0;
            while (server == null && count++ < 10) {
                List<Server> reachableServers = lb.getReachableServers();
                List<Server> allServers = lb.getAllServers();
                int upCount = reachableServers.size();
                int serverCount = allServers.size();
    
                if ((upCount == 0) || (serverCount == 0)) {
                    log.warn("No up servers available from load balancer: " + lb);
                    return null;
                }
    
                int nextServerIndex = incrementAndGetModulo(serverCount);
                server = allServers.get(nextServerIndex);
    
                if (server == null) {
                    /* Transient. */
                    Thread.yield();
                    continue;
                }
    
                if (server.isAlive() && (server.isReadyToServe())) {
                    return (server);
                }
    
                // Next.
                server = null;
            }
    
            if (count >= 10) {
                log.warn("No available alive servers after 10 tries from load balancer: "
                        + lb);
            }
            return server;
        }
    
        /**
         * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
         *
         * @param modulo The modulo to bound the value of the counter.
         * @return The next value.
         */
        private int incrementAndGetModulo(int modulo) {
            for (;;) {
                int current = nextServerCyclicCounter.get();
                int next = (current + 1) % modulo;
                if (nextServerCyclicCounter.compareAndSet(current, next))
                    return next;
            }
        }
    
        @Override
        public Server choose(Object key) {
            return choose(getLoadBalancer(), key);
        }
    
        @Override
        public void initWithNiwsConfig(IClientConfig clientConfig) {
        }
    }
    ```
    

## Ribbon之手写轮询算法：

- 手写负载均衡算法：**前提要懂得负载均衡算法原理 + JUC（CAS+自旋锁）**
    - 1、启动7001和7002集群
      
        链接2：[http://eureka7001.com:7001/](http://eureka7001.com:7001/)
        
        链接2：[http://eureka7002.com:7002/](http://eureka7002.com:7002/)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600576.png)
        
    - 2、对8001和8002微服务进行改造，分别在8001和8002的PaymentController类中添加以下代码:
      
        ```java
            @GetMapping(value = "/payment/lb")
            public String getPaymentLB() {
                return serverPort;
            }
        ```
        
    - 3、80订单微服务改造
        - 3.1 ApplicationContextBean (**ApplicationContextConfig** )去掉@LoadBalanced
          
            首先需要在RestTemplate的配置上将 @LoadBalanced注解删除
            
            ```java
            package com.youliao.springcloud.config;
            
            import org.springframework.cloud.client.loadbalancer.LoadBalanced;
            import org.springframework.context.ApplicationContext;
            import org.springframework.context.annotation.Bean;
            import org.springframework.context.annotation.Configuration;
            import org.springframework.web.client.RestTemplate;
            
            /**
             * @Author Dali
             * @Date 30/6/2021 下午 12:11
             * @Version 1.0
             * @Description
             */
            @Configuration
            public class ApplicationContextConfig {
                @Bean
                //@LoadBalanced //使用@LoadBalanced注解 赋予了RestTemplate负载均衡的能力
                public RestTemplate getRestTemplate() {
                    return new RestTemplate();
                }
            }
            //  ApplicationContext.xml    <bean id="" class="">
            ```
            
        - 3.2 LoadBalancer接口
          
            ```java
            package com.youliao.springcloud.lb;
            
            import org.springframework.cloud.client.ServiceInstance;
            
            import java.util.List;
            
            /**
             * @Author Dali
             * @Date 2021/07/04 下午 10:36
             * @Version 1.0
             * @Description: 收集Eureka上面所有活着的机器总数 UP (2) - payment8002 , payment8001
             */
            public interface LoadBalancer {
                //收集服务器总共有多少台能够提供服务的机器，并放到list里面
                ServiceInstance instance(List<ServiceInstance> serviceInstances);
            }
            ```
            
        - 3.3 MyLB实现类（实现LoadBalancer接口）
          
            ```java
            package com.youliao.springcloud.lb;
            
            import org.springframework.cloud.client.ServiceInstance;
            import org.springframework.cloud.client.loadbalancer.LoadBalanced;
            import org.springframework.stereotype.Component;
            
            import java.lang.annotation.Annotation;
            import java.util.List;
            import java.util.concurrent.atomic.AtomicInteger;
            
            /**
             * @Author Dali
             * @Date 2021/07/04 下午 10:40
             * @Version 1.0
             * @Description: Ribbon之手写轮询算法
             */
            @Component
            public class MyLB implements LoadBalancer {
                private AtomicInteger atomicInteger = new AtomicInteger(0);
            
                public final int getAndIncrement() {
                    int current;
                    int next;
                    // 自旋锁
                    do {
                        // 获取当前值
                        current = this.atomicInteger.get();
            
                        /*2147483647:整型最大值*/
                        // 发生越界，从0开始计数
                        next = current >= 2147483647 ? 0 : current + 1;
                        // 比较并交换
                    } while (!this.atomicInteger.compareAndSet(current, next));
                    System.out.println("*****第几次访问，次数next: " + next);
                    return next;
                }
            
                //负载均衡算法：第几次请求%服务器总数量=实际访问。服务每次启动从1开始
                @Override
                public ServiceInstance instance(List<ServiceInstance> serviceInstances) {
                    // 获取当前计数 模  实例总数
                    int index = getAndIncrement() % serviceInstances.size();
                    // 返回选择的实例
                    return serviceInstances.get(index);
                }
            }
            ```
            
        - 3.4 OrderController
          
            ```java
                @Resource
                private LoadBalancer loadBalancer;
            
                @Resource
                private DiscoveryClient discoveryClient;
            
               /**
                 * 自己写的负载均衡 轮询算法
                 *
                 * @return
                 */
                @GetMapping(value = "/consumer/payment/lb")
                public String getPaymentLB() {
                    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
                    if (instances == null || instances.size() <= 0) {
                        return null;
                    }
                    ServiceInstance serviceInstance = loadBalancer.instance(instances);
                    URI uri = serviceInstance.getUri();
                    return restTemplate.getForObject(uri + "/payment/lb", String.class);
                }
            }
            ```
            
        - 3.5 测试：[http://localhost/consumer/payment/lb](http://localhost/consumer/payment/lb)
          
            启动以下服务
            
            ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600577.png)
            
            测试结果：
            
            ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206071600578.gif)