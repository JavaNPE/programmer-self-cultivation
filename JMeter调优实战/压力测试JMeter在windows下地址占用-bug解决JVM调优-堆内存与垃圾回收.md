# 性能压测-压力测试|JMeter在windows下地址占用bug解决|JVM调优-堆内存与垃圾回收

# 性能压测- 压力测试基本介绍

## 一、性能监控

## 二、压力测试

压力测试考察当前软硬件环境下系统所能承受的最大负荷并帮助找出系统瓶颈所在。压测都

是为了系统在线上的处理能力和稳定性维持在-一个标准范围内，做到心中有数。

使用压力测试，我们有希望找到很多种用其他测试方法更难发现的错误。**有两种错误类型是:内存泄漏，并发与同步**。

有效的压力测试系统将应用以下这些**关键条件: 重复，并发，量级，随机变化。**

## 1、性能指标

- 响应时间(Response Time: RT)
  
    响应时间指用户从客户端发起一个请求开始，到客户端接收到从服务器端返回的响应结束，整个过程所耗费的时间。
    
- HPS (Hits Per Second) ：每秒点击次数，单位是次/秒。
- TPS ( Transaction per Second) ：系统每秒处理交易数，单位是笔/秒。
- QPS (Query per Second) ：系统每秒处理查询次数，单位是次/秒。
  
    对于互联网业务中，如果某些业务有且仅有一个请求连接，那么TPS=QPS=HPS, 一般情况下用TPS来衡量整个业务流程，用QPS来衡量接口查询次数，用HPS来表示对服务器单击请求。
    
- 无论TPS、QPS、 HPS,此指标是衡量系统处理能力非常重要的指标，越大越好，根据经验，一般情况下:
  
    金融行业: 1000TPS~50000TPS， 不包括互联网化的活动（秒杀活动、以及各种营销活动）
    
    保险行业: 100TPS~100000TPS， 不包括互联网化的活动（秒杀活动、以及各种营销活动）
    
    制造行业: 10TPS~5000TPS
    
    互联网电子商务: 10000TPS~1 000000TPS
    
    互联网中型网站: 1000TPS~50000TPS
    
    互联网小型网站: 500TPS~1 0000TPS
    
- 最大响应时间(Max Response Time)指用户发出请求或者指令到系统做出反应(响应)的最大时间。
- 最少响应时间(Mininum ResponseTime)指用户发出请求或者指令到系统做出反应(响应)的最少时间。
- 90%响应时间(90% Response Time)是指所有 用户的响应时间进行排序，第90%的响应时间。
- 从外部看，性能测试主要关注如下三个指标
    - 吞吐量:每秒钟系统能够处理的请求数、任务数。
    - 响应时间:服务处理一一个请求或-一个任务的耗时。
    - 错误率：一批请求中结果出错的请求所占比例。

## 2、JMeter

### 1、JMeter 安装：附下载地址

 下载对应的压缩包，解压运行jmeter.bat即可

[Download Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi)

### 2、JMeter压测演实例

1、添加线程组

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

2、添加HTTP请求

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

3、添加查看结果树、汇总报告

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

到90%以上，则可以说明服务器有问题，压力机没有问题。

- 影响性能考虑点包括：
  
    数据库、应用程序、中间件(tomact、 Nginx) 、网络和操作系统等方面
    
- 首先考虑自己的应用属于**CPU密集型**还是**IO密集型**

# P143、性能压测-压力测试JMeter在windows下地址占用bug解决

### 3、JMeter Address Already in use错误解决

windows本身提供的端口访问机制的问题。

Windows提供给TCP/IP 链接的端口为1024-5000， 并且要四分钟来循环回收他们。就导致
我们在短时间内跑大量的请求时将端口占满了。

1.cmd中，用regedit命令打开注册表

2.在HKEY_ LOCAL MACHINE\SYSTEM\CurrentControlSet\Services\Tgpip\Parameters 下，

1. 右击**parameters，**添加一个新的**DWORD**，名字为**MaxUserPort**
2. 然后双击**MaxUserPort**，输入数值数据为**65534**, **基数选择十进制**(如果是分布式运行的话，控制机器和负载机器都需要这样操作哦)

3.修改配置完毕之后记得重启机器才会生效。

**TCPTimedWaitDelay: 30**

[错误 WSAENOBUFS (10055) - Windows Client](https://docs.microsoft.com/zh-CN/troubleshoot/windows-client/networking/connect-tcp-greater-than-5000-error-wsaenobufs-10055)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

**TCPTimedWaitDelay: 30**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

最终结果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

# P144、性能压测-性能监控-JVM调优-堆内存与垃圾回收



### 1、JVM内存模型：C语言编写

优化更多的是在**堆**这一块

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

- 程序计数 器Program Counter Register：
    - 记录的是正在执行的虛拟机字节码指令的地址，
    - 此内存区域是唯一一个在 JAVA虚拟机规范中没有规定任何OutOfMemoryError的区域
- 虚拟机：VMStack
    - 描述的是JAVA方法执行的内存模型，每个方法在执行的时候都会创建一个栈帧，用于存储局部变量表，操作数栈，动态链接，方法接口等信息
    - 局部变量表存储了编译期可知的各种基本数据类型、对象引用
    - 线程请求的栈深度不够会报StackOverflowError异常
    - 栈动态扩展的容量不够会报OutOfMemoryError异常
    - 虚拟机栈是线程隔离的，即每个线程都有自己独立的虚拟机栈
- 本地方法: Native Stack
    - 本地方法栈类似于虛拟机栈，只不过本地方法栈使用的是本地方法
- 堆: Heap
    - 几乎所有的对象实例都在堆上分配内存

jvm内存模型图：

![JVM调优更多的是为了调整堆区，元数据区：直接操作物理内存](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

JVM调优更多的是为了调整堆区，元数据区：直接操作物理内存

## 2、堆（JVM调优主要的调优就是为了调堆）

**所有的对象实例以及数组（的创建）都要在堆上分配**。堆是垃圾收集器管理的主要区域，也被称为“GC堆”；也是我们优化最多考虑的地方。

堆可以细分为：

- 新生代
    - Eden空间
    - From Survivor空间
    - To Survivor空间
- 老年代
- 永久代/元空间
    - Java8以前永久代，受jvm管理，java8以后元空间，直接使用物理内存。因此，默认情况下，元空间的大小仅受本地内存限制。

垃圾回收

![Java8内存模型，元空间：直接操作物理内存](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)

Java8内存模型，元空间：直接操作物理内存

**从Java8开始，HotSpot 已经完全将永久代(Permanent Generation)移除，取而代之的是一个新的区域一元空间(MetaSpace)** 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2011.png)

# P145、性能压测-性能监控-jvisualvm使用

## 3、jconsole与jvisualvm(推荐后者)

Jdk的两个小工具jconsole、jvisualvm (升级版的jconsole)； 通过命令行启动，可监控本地和远程应用。远程应用需要配置。

### jconsole使用案例

Win+R调出cmd窗口，输入命令：jconsole，选择本地连接。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

### jvisualvm使用案例

Win+R调出cmd窗口，输入命令：jvisualvm

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2013.png)

> 1、jvisualvm能干什么
> 

监控内存泄露，跟踪垃圾回收，执行时内存、cpu分析，线程分析...

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2014.png)

**运行**：正在运行的

**休眠**：sleep

**等待**：wait

**驻留**：线程池里面的空闲线程

**监视**：阻塞的线程，正在等待锁

> 2、安装插件方便查看gc
> 
1. cmd启动jvisualvm
2. 工具→ 插件
   
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)
    

如果提示无法连接Java VisualVM 插件中心..............

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2018.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

更新插件中心的URL地址

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)

安装Visual GC插件，安装完成之后重启jvisualvm

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)

监视+Visual GC

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)

# P146、性能压测-优化-中间件(nginx和geateway)对性能的影响

- SQL耗时越小越好，-般情祝下微秒级别。
- 命中率越高越好，一般情 况下不能低于95%。
- 锁等待次数越低越好，等待时间越短越好。

## 1、压测Nginx

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)

## 监控docker容器CPU使用率命令：`docker stats`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)

nginx比较消耗CPU资源

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)

## 2、压测网关gateway

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2027.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2028.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2029.png)

## 3、压测简单服务

[http://localhost:10000/hello](http://localhost:10000/hello)

```java
package com.atguigu.gulimall.product.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * @Author Dali
 * @Date 2021/12/19 11:48
 * @Version 1.0
 * @Description: P146、性能压测-优化-中间件(nginx和geateway)对性能的影响
 * 压测简单服务
 */
@Controller
public class IndexController {
	@ResponseBody
	@GetMapping("/hello")
	public String hello() {
		return "hello";
	}
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2030.png)

## 4 gateway+简单服务：通过网关访问简单服务/hello，并对其进行压力测试

[localhost:88/hello](http://localhost:88/hello)

gulimall-gateway服务：application.yml

```yaml
##将精确的路由规则放置到模糊的路由规则的前面--- Path=/api/product/**是精确路由,压测/hello简单服务
- id: product_route
  uri: lb://gulimall-product # 注册中心的服务
  predicates:
    - Path=/api/product/**,/hello
  filters:
    - RewritePath=/api/(?<segment>/?.*),/$\{segment}
```

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2031.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2032.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2033.png)

## 5、压测全链路：nginx+gateway+product商品微服务

[gulimall.com/hello](http://gulimall.com/hello)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2034.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2035.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2036.png)

| 压测内容 | 压测线程数 | 吞吐量/s | 90%响应时间 | 99%响应时间 |
| --- | --- | --- | --- | --- |
| Nginx | 50 | 642.991 | 72毫秒 | 1,897 |
| Gateway | 50 | 3,052.233 | 41 | 125 |
| 简单服务 | 50 | 2,849.157 | 45 | 117 |
| 首页一级菜单渲染 | 50 | 1,853.605（DB，thymeleaf） | 61 | 148 |
| 三级分类数据获取 | 50 | 2.564（DB拖慢的） | 24,902 | 26,548 |
| 首页全量数据获取 | 50 | 7.922（静态资源拖慢） | 9,001 | 10,003 |
| Nginx+Gateway |  |  |  |  |
| Gateway+简单服务 | 50 | 1,239.014 | 86 | 242 |
| 全链路 | 50 | 214.045 | 361 | 549 |

中间件越多，性能损失越大，大多都损失在网络交互了。

针对业务优化：

- Db（MySQL优化业务查询数据库表）
- 模板的渲染速度（thymeleaf开发的时候关闭缓存，上线的时候开启缓存）
- 静态资源（css，png，js文件等）

## 6、压力测试：首页一级菜单渲染

[](http://localhost:10000/)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2037.png)

JMeterHTTP请求内容：

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2038.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2039.png)

## 7、三级分类数据获取

[localhost:10000/index/catalog.json](http://localhost:10000/index/catalog.json)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2040.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2041.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2042.png)

## 8、首页全量数据获取

HTTP请求基本设置：

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2043.png)

高级设置：

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2044.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2045.png)

# P147、性能压测-优化简单优化吞吐量测试

## 5、JVM分析&调优

jvm调优，调的是稳定，并不能带给你性能的大幅提升。服务稳定的重要性就不用多说了，

# P148、性能压测-优化- nginx动静分离

[Java项目《谷粒商城》Java架构师 | 微服务 | 大型电商项目_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1np4y1C7Yf?p=148&spm_id_from=pageDriver)

1、以后将所有项目的静态资源都应该放在nginx里面。

2、规则: 指定/static/**所有请求都由nginx直接返回。

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2046.png)

前后端分离之后，前端资源都不会到后台了，后台只处理前端发送的业务请求。

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2047.png)

将原来在IDEA中的静态资源放在nginx中刚才新创建的static文件夹中，实现动静分离。

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2048.png)

idea中的index.html中的url地址之前需要添加`/static/`

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2049.png)

修改nginx配置信息

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2050.png)

```yaml
server {
    listen       80;
    server_name  gulimall.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;
    **location /static/ {
        root    /usr/share/nginx/html;
    }**
    location / {
        proxy_set_header Host  $host;
        proxy_pass  http://gulimall;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
............................
    #    deny  all;
    #}
}
```

修改配置信息记得重启nginx

```yaml
[root@10 static]# docker restart nginx
```

需要模板引擎解析html页面，放nginx没用；没有前后端分离，只是动静分离。

- **index.html**_templates/index.html 修改之后的代码，改错！！！
  
    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>Document</title>
      <link rel="stylesheet" href="/static/index/css/swiper-3.4.2.min.css">
      <link rel="stylesheet" href="/static/index/css/GL.css">
      <script src="/static/index/js/jquery-3.1.1.min.js" type="text/javascript" charset="utf-8"></script>
    
      <script src="/static/index/js/swiper-3.4.2.jquery.min.js" type="text/javascript" charset="utf-8"></script>
    
      <script src="/static/index/js/swiper-3.4.2.min.js"></script>
    
    </head>
    
    <body>
    <div class="top_find">
      <div class="top_find_son">
        <img src="/static/index/img/top_find_logo.png" alt="">
        <div class="input_find">
          <input type="text" placeholder="卸妆水" />
          <span style="background: url('/static/index/img/img_12.png') 0 -1px;"></span>
          <a href="/static/#"><img src="/static/index/img/img_09.png" /></a>
        </div>
      </div>
    </div>
    <ul class="left_floor">
      <li class="left_floor_xiang">享品质</li>
      <li class="left_floor_fu">服饰美妆</li>
      <li class="left_floor_jia">家电手机</li>
      <li class="left_floor_dian">电脑数码</li>
      <li class="left_floor_3C">3C运动</li>
      <li class="left_floor_ai">爱吃</li>
      <li class="left_floor_mu">母婴家居</li>
      <li class="left_floor_tu">图书汽车</li>
      <li class="left_floor_you">游戏金融</li>
      <li class="left_floor_lv">旅行健康</li>
      <li class="left_floor_hai">还没逛够</li>
      <li class="left_floor_ding">顶部</li>
    </ul>
    <header>
      <div class="head">
        <a href="/static/#"><img src="/static/index/img/img_01.png" /></a>
        <p>X</p>
      </div>
      <!--头部-->
      <div class="header_head">
        <div class="header_head_box">
          <a href="/static/#" class="img"><img src="/static/index/img/logo.jpg" /></a>
          <b class="header_head_p">
            <a href="/static/#">
              <img src="/static/index/img/img_05.png" style="border-radius: 50%;"/>
              <!--<span class="glyphicon glyphicon-map-marker"></span>-->
              北京</a>
            <div class="header_head_p_cs">
              <a href="/static/#" style="background: #C81623;color: #fff;">北京</a>
              <a href="/static/#">上海</a>
              <a href="/static/#">天津</a>
              <a href="/static/#">重庆</a>
              <a href="/static/#">河北</a>
              <a href="/static/#">山西</a>
              <a href="/static/#">河南</a>
              <a href="/static/#">辽宁</a>
              <a href="/static/#">吉林</a>
              <a href="/static/#">黑龙江</a>
              <a href="/static/#">内蒙古</a>
              <a href="/static/#">江苏</a>
              <a href="/static/#">山东</a>
              <a href="/static/#">安徽</a>
              <a href="/static/#">浙江</a>
              <a href="/static/#">福建</a>
              <a href="/static/#">湖北</a>
              <a href="/static/#">湖南</a>
              <a href="/static/#">广东</a>
              <a href="/static/#">广西</a>
              <a href="/static/#">江西</a>
              <a href="/static/#">四川</a>
              <a href="/static/#">海南</a>
              <a href="/static/#">贵州</a>
              <a href="/static/#">云南</a>
              <a href="/static/#">西藏</a>
              <a href="/static/#">陕西</a>
              <a href="/static/#">甘肃</a>
              <a href="/static/#">青海</a>
              <a href="/static/#">宁夏</a>
              <a href="/static/#">新疆</a>
              <a href="/static/#">港澳</a>
              <a href="/static/#">台湾</a>
              <a href="/static/#">钓鱼岛</a>
              <a href="/static/#">海外</a>
            </div>
          </b>
          <ul>
            <li>
              <a href="/static//登录页面\index.html">你好，请登录</a>
            </li>
            <li>
              <a href="/static//注册页面\index.html" class="li_2">免费注册</a>
            </li>
            <span>|</span>
            <li>
              <a href="/static/#">我的订单</a>
            </li>
    
          </ul>
        </div>
      </div>
      <!--搜索导航-->
      <div class="header_sous">
        <div class="header_form">
          <input id="searchText" type="text" placeholder="" />
          <span style="background: url('/static/index/img/img_12.png') 0 -1px;"></span>
          <!--<button><i class="glyphicon"></i></button>-->
          <a href="/static/#" ><img src="/static/index/img/img_09.png" onclick="search()" /></a>
        </div>
        <div class="header_ico">
          <div class="header_gw">
            <img src="/static/index/img/img_15.png" />
            <span><a href="/static//购物车\One_JDshop.html">我的购物车</a></span>
            <span>0</span>
          </div>
          <div class="header_ko">
            <p>购物车中还没有商品，赶紧选购吧！</p>
          </div>
        </div>
        <div class="header_form_nav">
          <ul>
            <li>
              <a href="/static/#" class="aaaaa">满999减300</a>
            </li>
            <li>
              <a href="/static/#">金立S11</a>
            </li>
            <li>
              <a href="/static/#">农用物资</a>
            </li>
            <li>
              <a href="/static/#">保暖特惠</a>
            </li>
            <li>
              <a href="/static/#">洗衣机节</a>
            </li>
            <li>
              <a href="/static/#">七度空间卫生巾</a>
            </li>
            <li>
              <a href="/static/#">自动波箱油</a>
            </li>
            <li>
              <a href="/static/#">超市</a>
            </li>
          </ul>
        </div>
        <nav>
          <ul>
            <li>
              <a href="/static/#">秒杀</a>
            </li>
            <li>
              <a href="/static/#">优惠券</a>
            </li>
            <li>
              <a href="/static/#">闪购</a>
            </li>
            <li>
              <a href="/static/#">拍卖</a>
            </li>
          </ul>
          <div class="spacer">|</div>
          <ul>
            <li>
              <a href="/static/#">服饰</a>
            </li>
            <li>
              <a href="/static/#">超市</a>
            </li>
            <li>
              <a href="/static/#">生鲜</a>
            </li>
            <li>
              <a href="/static/#">全球购</a>
            </li>
          </ul>
          <div class="spacer">|</div>
          <ul>
            <li>
              <a href="/static/#">金融</a>
            </li>
          </ul>
        </nav>
        <div class="right">
          <a href="/static/#"><img src="/static/index/img/img_21.png" /></a>
        </div>
      </div>
      <!--轮播主体内容-->
      <div class="header_main">
        <div class="header_banner">
          <div class="header_main_left">
            <ul>
              <li th:each="category : ${categorys}">
                <a href="/static/#" class="header_main_left_a" th:attr="ctg-data=${category.catId}"><b th:text="${category.name}">家用电器1111</b></a>
              </li>
            </ul>
    
          </div>
          <div class="header_main_center">
            <div class="swiper-container swiper1">
              <div class="swiper-wrapper">
                <div class="swiper-slide">
                  <a href="/static/#"><img src="/static/index/img/lunbo.png" /></a>
                </div>
    
                <div class="swiper-slide">
                  <a href="/static/#"><img src="/static/index/img/lunbo3.png" /></a>
                </div>
    
                <div class="swiper-slide">
                  <a href="/static/#"><img src="/static/index/img/lunbo6.png" /></a>
                </div>
                <div class="swiper-slide">
                  <a href="/static/#"><img src="/static/index/img/lunbo7.png" /></a>
                </div>
              </div>
              <div class="swiper-pagination"></div>
              <div class="swiper-button-next swiper-button-white"></div>
              <div class="swiper-button-prev swiper-button-white"></div>
            </div>
            <div class="header_main_center_b">
              <a href="/static/#"><img src="/static/index/img/5a13bf0bNe1606e58.jpg" /></a>
              <a href="/static/#"><img src="/static/index/img/5a154759N5385d5d6.jpg" /></a>
            </div>
          </div>
          <div class="header_main_right">
            <div class="header_main_right_user">
              <div class="user_info">
                <div class="user_info_tou">
                  <a href="/static/#"><img class="" src="/static/index/img/touxiang.png"></a>
                </div>
                <div class="user_info_show">
                  <p class="">Hi, 欢迎来到!</p>
                  <p>
                    <a href="/static/#" class="">登录</a>
                    <a href="/static/#" class="">注册</a>
                  </p>
                </div>
              </div>
              <div class="user_info_hide">
                <a href="/static/#">新人福利</a>
                <a href="/static/#">PLUS会员</a>
              </div>
            </div>
            <div class="header_main_right_new">
              <div class="header_new">
                <div class="header_new_t">
                  <p class="active">
                    <a href="/static/#">促销</a>
                  </p>
                  <p>
                    <a href="/static/#">公告</a>
                  </p>
                  <a href="/static/#">更多</a>
                </div>
                <div class="header_new_connter">
                  <div class="header_new_connter_1">
                    <ul>
                      <li>
                        <a href="/static/#">全民纸巾大作战</a>
                      </li>
                      <li>
                        <a href="/static/#">家具建材满999减300元</a>
                      </li>
                      <li>
                        <a href="/static/#">黑科技冰箱，下单立减千元</a>
                      </li>
                      <li>
                        <a href="/static/#">抢102减101神券！</a>
                      </li>
                    </ul>
                  </div>
                  <div class="header_new_connter_1" style="display: none;">
                    <ul>
                      <li>
                        <a href="/static/#">关于召回普利司通（天津）轮胎有限公司2个规格乘用车轮胎的公告</a>
                      </li>
                      <li>
                        <a href="/static/#">物流推出配送员统一外呼电话"95056”</a>
                      </li>
                      <li>
                        <a href="/static/#">天府大件运营中心开仓公告</a>
                      </li>
                      <li>
                        <a href="/static/#">大件物流“送装一体”服务全面升级！</a>
                      </li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
            <div class="header_main_right_ser">
              <div class="ser_box">
                <ul>
                  <li class="ser_box_item">
                    <a href="/static/#">
                      <img src="/static/index/img/huafei.png" />
                      <span>话费</span>
                    </a>
                  </li>
                  <li class="ser_box_item">
                    <a href="/static/#">
                      <img src="/static/index/img/jipiao.png" />
                      <span>机票</span>
                    </a>
                  </li>
                  <li class="ser_box_item">
                    <a href="/static/#">
                      <img src="/static/index/img/jiudian.png" />
                      <span>酒店</span>
                    </a>
                  </li>
                  <li class="ser_box_item">
                    <a href="/static/#">
                      <img src="/static/index/img/youxi.png" />
                      <span>游戏</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/qiyegou.png" />
                      <span>企业购</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/jiayouka.png" />
                      <span>加油卡</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/dianyingpiao.png" />
                      <span>电影票</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/huochepiao.png" style="height:20px;" />
                      <span>火车票</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/zhongchou.png" />
                      <span>众筹</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/licai.png" style="height:22px;" />
                      <span>理财</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/lipinka.png" style="height:14px;" />
                      <span>礼品卡</span>
                    </a>
                  </li>
                  <li class="ser_box_item1">
                    <a href="/static/#">
                      <img src="/static/index/img/baitiao.png" style="height:20px;" />
                      <span>白条</span>
                    </a>
                  </li>
                </ul>
                <div class="ser_box_aaa">
                  <div class="ser_box_aaa_one">
                    <div class="ser_box_aaa_nav">
                      <ol>
                        <li class="active">
                          <a href="/static/#">话费</a>
                        </li>
                        <li>
                          <a href="/static/#">机票</a>
                        </li>
                        <li>
                          <a href="/static/#">酒店</a>
                        </li>
                        <li>
                          <a href="/static/#">游戏</a>
                        </li>
                      </ol>
                      <div class="ser_ol">
                        <div class="ser_ol_li">
                          <ul>
                            <div class="guanbi">X</div>
                            <a class="active">话费充值</a>
                            <a>流量充值</a>
                            <a>套餐变更</a>
                            <div class="ser_ol_div">
                              <p>号码<input type="text" /></p>
                              <p style="margin: 10px 0;">面值
                                <select name="">
                                  <option value="">100元</option>
                                  <option value="">20元</option>
                                  <option value="">50元</option>
                                  <option value="">10元</option>
                                  <option value="">2元</option>
                                </select>
                                <span>￥98.0-￥100.0</span></p>
                            </div>
                            <button>快速充值</button>
                            <p class="p">抢99减50元话费</p>
                          </ul>
    
                        </div>
                        <div class="ser_ol_li">
                          <ul>
                            <div class="guanbi">X</div>
                            <a class="active">国际机票</a>
                            <a>国际/港澳</a>
                            <a>特惠活动</a>
                            <div class="ser_ol_div1">
                              <p>
                                <input type="radio" name="a" style="vertical-align:middle;" />单程
                                <input type="radio" name="a" style="vertical-align:middle;" />往返
                              </p>
                              <input type="text" placeholder="出发城市" />
                              <input type="text" placeholder="到达城市" />
                              <input type="text" placeholder="日期" />
                            </div>
                            <button>机票查询</button>
                            <span class="p">当季热门特惠机票</span>
                          </ul>
                        </div>
                        <div class="ser_ol_li">
                          <ul>
                            <div class="guanbi">X</div>
                            <a class="active" style="width: 50%;">国内港澳台</a>
                            <a style="width: 50%;">促销活动</a>
                            <div class="ser_ol_div1">
                              <input type="text" placeholder="出发城市" style="margin-top: 10px;" />
                              <input type="text" placeholder="到达城市" />
                              <input type="text" placeholder="日期" />
                              <input type="text" placeholder="酒店 商圈 地标" />
                            </div>
                            <button>酒店查询</button>
                            <span class="p">订酒店到</span>
                          </ul>
                        </div>
                        <div class="ser_ol_li">
                          <ul>
                            <div class="guanbi">X</div>
                            <a class="active">点卡</a>
                            <a>QQ</a>
                            <a>页游</a>
                            <div class="ser_ol_div1">
                              <input type="text" placeholder="游戏" style="margin-top: 15px;" />
                              <br />面值
                              <select name="" style="margin: 8px 0;">
                                <option value="">面值</option>
                                <option value="">面值</option>
                                <option value="">面值</option>
                              </select><span style="color: #C81623;">￥0.00</span>
                              <p>
                                <input type="radio" name="a" style="width: 15px;vertical-align:middle;" />直充
                                <input type="radio" name="a" style="width: 15px;vertical-align:middle;" />卡密
                              </p>
                            </div>
                            <button>快速充值</button>
                            <span class="p">吃鸡就要快人一步</span>
                          </ul>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="header_banner1">
          <a href="/static/#" class="a">
            <img src="/static/index/img/5a1e5ce2N034ce344.png" class="aa" />
          </a>
          <div class="header_banner1_div">
            <p>X</p>
          </div>
        </div>
      </div>
    </header>
    
    <div class="section_second">
      <!-- 第一层 -->
      <div class="section_second_header">
        <p class="section_second_header_img"></p>
        <div class="section_second_header_left">
          <p></p>
          <span class="">秒杀</span>
          <span>总有你想不到的低价</span>
          <span>
            </span>
        </div>
        <div class="section_second_header_right">
          <p>当前场次</p>
          <span class="section_second_header_right_hours">00</span>
          <span class="section_second_header_right_mao">：</span>
          <span class="section_second_header_right_minutes">00</span>
          <span class="section_second_header_right_mao">：</span>
          <span class="section_second_header_right_second">00</span>
          <p>后结束</p>
        </div>
      </div>
      <div class="section_second_list">
        <div class="swiper-container swiper_section_second_list_left">
          <div class="swiper-wrapper">
            <div class="swiper-slide">
              <ul>
                <li>
                  <img src="/static/index/img/section_second_list_img1.jpg" alt="">
                  <p>花王 (Merries) 妙而舒 纸尿裤 大号 L54片 尿不湿（9-14千克） （日本官方直采） 花王 (Merries) 妙而舒 纸尿裤 大号 L54片 尿不湿（9-14千</p>
                  <span>¥83.9</span><s>¥99.9</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img2.jpg" alt="">
                  <p>华为mate9 4GB+32GB版 月光银 移动联通电信4G手机 双卡</p>
                  <span>¥17.90</span><s>¥29.90</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img3.jpg" alt="">
                  <p>超能 植翠低泡洗衣液（鲜艳亮丽）2kg/袋装（新老包装随机</p>
                  <span>¥20.70</span><s>¥44.90</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img4.jpg" alt="">
                  <p>长城（GreatWall）红酒 特选5年橡木桶解百纳干红葡萄酒 整</p>
                  <span>¥399.00</span><s>¥599.00</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img5.jpg" alt="">
                  <p>惠普(HP)暗影精灵2代Pro 15.6英寸游戏笔记本电脑(i5-7300H</p>
                  <span>¥5999.00</span><s>¥6499.00</s>
                </li>
              </ul>
            </div>
            <div class="swiper-slide">
              <ul>
                <li>
                  <img src="/static/index/img/section_second_list_img6.jpg" alt="">
                  <p>Apple iMac 21.5英寸一体机（2017新款四核Core i5 处理器/8GB内存/1TB/RP555显卡/4K屏 MNDY2CH/A） Apple iMac 21.5英寸一体机（2017新款四核Core i5 处理</p>
                  <span>¥9588.00</span><s>¥10288.00</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img7.jpg" alt="">
                  <p>中柏（Jumper）EZpad 4S Pro 10.6英寸二合一平板电脑（X5 z</p>
                  <span>¥848.00</span><s>¥899.00</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img8.jpg" alt="">
                  <p>飞利浦（PHILIPS）电动牙刷HX6761/03亮白型成人充电式声波震动牙刷粉色 飞利浦（PHILIPS）电动牙刷HX6761/03亮白型成人充电式声波
                  </p>
                  <span>¥379.00</span><s>¥698.00</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img9.jpg" alt="">
                  <p>美的(Midea) 258升 变频智能三门冰箱 一级能效 风冷无霜 中门</p>
                  <span>¥3088.00</span><s>¥3299.00</s>
                </li>
                <li>
                  <img src="/static/index/img/section_second_list_img10.jpg" alt="">
                  <p>【第二件减50元】蒙羊 内蒙古羔羊羊肋排 2.4斤</p>
                  <span>¥99.90</span><s>¥199.00</s>
                </li>
              </ul>
            </div>
          </div>
          <div class="swiper-button-prev second_list">
            <p></p>
          </div>
          <div class="swiper-button-next second_list">
            <p></p>
          </div>
        </div>
        <ul class="section_second_list_right">
          <li>
            <img src="/static/index/img/section_second_list_right_img.jpg" alt="">
          </li>
          <li>
            <img src="/static/index/img/section_second_list_right_img.png" alt="">
          </li>
          <div class="section_second_list_right_button">
            <p class="section_second_list_right_button_active"></p>
            <p></p>
          </div>
        </ul>
      </div>
    
    </div>
    
    </body>
    <script type="text/javascript">
      function search() {
        var keyword=$("#searchText").val()
        window.location.href="/static/http://search.gulimall.com/search.html?keyword="+keyword;
      }
    
    </script>
    <script type="text/javascript" src="/static/index/js/text.js"></script>
    <script type="text/javascript" src="/static/index/js/header.js"></script>
    <script type="text/javascript" src="/static/index/js/secend.js"></script>
    <script type="text/javascript" src="/static/index/js/zz.js"></script>
    <script type="text/javascript" src="/static/index/js/index.js"></script>
    <script type="text/javascript" src="/static/index/js/left,top.js"></script>
    <script type="text/javascript" src="/static/index/js/catalogLoader.js"></script>
    </html>
    ```
    

# P149、性能压测-优化模拟线上应用内存崩溃宕机情况

## 【坑】关闭电脑防火墙

如果出现以下这种情况，务必检查Windows防火墙是否关闭：

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2051.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2052.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2053.png)

1、idea中设置：-Xmx100m

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2054.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2055.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2056.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2057.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2058.png)

2、我们调节idea中的 `-Xmx1024m -Xms1024m -Xmn512m`

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2059.png)

## 模拟服务崩溃场景：

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2060.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2061.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2062.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2063.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2064.png)

![Untitled](15_%E6%80%A7%E8%83%BD%E5%8E%8B%E6%B5%8B-%20%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%20JMeter%E5%9C%A8windows%E4%B8%8B%E5%9C%B0%E5%9D%80%E5%8D%A0%E7%94%A8bug%E8%A7%A3%E5%86%B3%20JVM%E8%B0%83%E4%BC%98-%E5%A0%86%E5%86%85%E5%AD%98%E4%B8%8E%E5%9E%83%202459c0ebbe12409ba8b1139e0d0b1f27/Untitled%2065.png)

# P150、性能压测优化优化三级分类数据获取

针对业务逻辑进行优化，之前的写法多次查询数据库，频繁访问数据库会造成性能的损耗，先查询所有数据，将查询到的数据封装在一个List<CategoryEntity>集合中，然后后面查二级或三级分类的时候就去List<CategoryEntity>集合中去比对，然后在封装查询返回的数据，避免频繁访问数据库。

```java
/**
	 * 查出所有的分类
	 *
	 * @return
	 */
	@Override
	public Map<String, List<Catelog2Vo>> getCatalogJson() {

		/**
		 * 1、将数据库的多次查询变成一次（150、性能压测优化优化三级分类数据获取）
		 */
		// 查询所有
		List<CategoryEntity> selectList = baseMapper.selectList(null);

		//1、查出所有1级分类
		List<CategoryEntity> level1Categorys = getParent_cid(selectList, 0L);

		//2、封装数据【重要】
		Map<String, List<Catelog2Vo>> parent_cid = level1Categorys.stream().collect(Collectors.toMap(k -> k.getCatId().toString(), v -> {
			//1、每一个的一级分类，查到这个一级分类的二级分类
			List<CategoryEntity> categoryEntities = getParent_cid(selectList, v.getCatId());

			//2、封装上面的结果
			List<Catelog2Vo> catelog2Vos = null;
			if (categoryEntities != null) {
				catelog2Vos = categoryEntities.stream().map(l2 -> {
					Catelog2Vo catelog2Vo = new Catelog2Vo(v.getCatId().toString(), null, l2.getCatId().toString(), l2.getName());
					//1、找当前二级分类的三级分类，封装成vo
					List<CategoryEntity> level3Catelog = getParent_cid(selectList, l2.getCatId());
					if (level3Catelog != null) {
						List<Catelog2Vo.Catelog3Vo> collect = level3Catelog.stream().map(l3 -> {
							//2、封装成指定格式
							Catelog2Vo.Catelog3Vo catelog3Vo = new Catelog2Vo.Catelog3Vo(l2.getCatId().toString(), l3.getCatId().toString(), l3.getName());

							return catelog3Vo;
						}).collect(Collectors.toList());

						catelog2Vo.setCatalog3List(collect);
					}

					return catelog2Vo;
				}).collect(Collectors.toList());
			}
			return catelog2Vos;
		}));
		return parent_cid;
	}

	private List<CategoryEntity> getParent_cid(List<CategoryEntity> selectList, Long parent_cid) {
		List<CategoryEntity> collect = selectList.stream()
				.filter(item -> item.getParentCid().equals(parent_cid))
				.collect(Collectors.toList());
		return collect;
		//return baseMapper.selectList(new QueryWrapper<CategoryEntity>().eq("parent_cid", v.getCatId()));
	}
```