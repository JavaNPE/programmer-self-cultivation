# 18_SpringCloud Alibaba Seata处理分布式事务

# 一、分布式事务问题

## 分布式前：

单机单库没这个问题，从1：1 -> 1:N -> N: N

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020238.png)

跨数据库，多数据源的统一调度，就会遇到分布式事务问题。

## 分布式之后：

如下图，单体应用被拆分成微服务应用，原来的三个模板被拆分成三个独立的应用，分别使用三个独立的数据源，业务操作需要调用三个服务来完成。此时每个服务内部的数据一致性由本地事务来保证，但是全局的数据一致性问题没法保证。

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020240.png)

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020241.png)

> 一句话：
> 

**一次业务操作需要跨多个数据源或需要跨多个系统进行远程调用，就会产生分布式事务问题**

# 二、Seata简介

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020242.png)

## **Seata是什么：是一个1+3的套件组成（1：全局唯一的事务ID，3：三大组件“TC,TM,RM”）**

Seata是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能和简单易用的分布式事务服务

官网地址：

[Seata](http://seata.io/zh-cn/)

## Seata能干嘛：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020243.png)

一个典型的分布式事务过程：

分布式事务处理过程的一致性ID + 三组件模型：

- Transaction ID XID：全局唯一的事务ID
- 三组件的概念：
    - Transaction Coordinator（TC）：事务协调器，维护全局事务，驱动全局事务提交或者回滚
    - Transaction Manager（TM）：事务管理器，控制全局事务的范围，开始全局事务提交或回滚全局事务
    - Resource Manager（RM）：资源管理器，控制分支事务，负责分支注册分支事务和报告
    
    ### 处理过程：
    
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020244.png)
    
    - TM向TC申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的XID
    - XID在微服务调用链路的上下文中传播
    - RM向TC注册分支事务，将其纳入XID对应全局事务的管辖
    - TM向TC发起针对XID的全局提交或回滚决议
    - TM调度XID下管辖的全部分支事务完成提交或回滚请求

## Seata下载地址：

[Releases · seata/seata](https://github.com/seata/seata/releases)

## Seata怎么玩：@Transactional，@GlobalTransactional

Spring 本地控制事务注解@Transactional，全局@GlobalTransactional。

SEATA的分布式交易解决方案：

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020245.png)

# 三、Seata-Server安装

## 1.Seata官网地址：Seata下载地址

[Seata](http://seata.io/zh-cn/)

## 2.下载版本：v0.9.0版本

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020246.png)

## 3.seata-server-0.9.0.zip解压到指定目录并修改conf目录下的file.conf配置文件

seata路径：D:\develop\Utils\seata-server-0.9.0

- 先备份原始file.conf文件
  
    ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020247.png)
    
- 主要修改：**自定义事务组名称+事务日志存储模式为db+数据库连接信息**
- file.conf文件下：D:\develop\Utils\seata-server-0.9.0
    - service模块：修改服务模块中的分组
      
        ```json
        vgroup_mapping.my_test_tx_group = "fsp_tx_group"
        ```
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020248.png)
        
        ![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020249.png)
        
    - store模块：修改存储模块
      
        ```sql
        mode = "db"
         
          url = "jdbc:mysql://127.0.0.1:3306/seata"
          user = "root"
          password = "你自己的密码"
        ```
        
        ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020250.png)
        
        ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020251.png)
        

## 4.mysql5.7数据库新建库seata

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020252.png)

## 5.在seata库里建表

- 建表db_store.sql在\seata-server-0.9.0\seata\conf目录里面：**db_store.sql**
  
    [db_store.sql](18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/db_store.sql)
    
    ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020253.png)
    
- SQL
    - 如图：
      
        ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020254.png)
        
    
    ```sql
    -- the table to store GlobalSession data
    drop table if exists `global_table`;
    create table `global_table` (
      `xid` varchar(128)  not null,
      `transaction_id` bigint,
      `status` tinyint not null,
      `application_id` varchar(32),
      `transaction_service_group` varchar(32),
      `transaction_name` varchar(128),
      `timeout` int,
      `begin_time` bigint,
      `application_data` varchar(2000),
      `gmt_create` datetime,
      `gmt_modified` datetime,
      primary key (`xid`),
      key `idx_gmt_modified_status` (`gmt_modified`, `status`),
      key `idx_transaction_id` (`transaction_id`)
    );
    
    -- the table to store BranchSession data
    drop table if exists `branch_table`;
    create table `branch_table` (
      `branch_id` bigint not null,
      `xid` varchar(128) not null,
      `transaction_id` bigint ,
      `resource_group_id` varchar(32),
      `resource_id` varchar(256) ,
      `lock_key` varchar(128) ,
      `branch_type` varchar(8) ,
      `status` tinyint,
      `client_id` varchar(64),
      `application_data` varchar(2000),
      `gmt_create` datetime,
      `gmt_modified` datetime,
      primary key (`branch_id`),
      key `idx_xid` (`xid`)
    );
    
    -- the table to store lock data
    drop table if exists `lock_table`;
    create table `lock_table` (
      `row_key` varchar(128) not null,
      `xid` varchar(96),
      `transaction_id` long ,
      `branch_id` long,
      `resource_id` varchar(256) ,
      `table_name` varchar(32) ,
      `pk` varchar(36) ,
      `gmt_create` datetime ,
      `gmt_modified` datetime,
      primary key(`row_key`)
    );
    ```
    

## 6.修改seata-server-0.9.0\seata\conf目录下的registry.conf配置文件

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020255.png)

```bash
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"
 
  nacos {
    serverAddr = "localhost:8848"
    namespace = ""
    cluster = "default"
  }
目的是：指明注册中心为nacos，及修改nacos连接信息
```

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020256.png)

## 7.先启动Nacos端口号8848

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020257.png)

## 8.再启动seata-server：本机地址D:\develop\Utils\seata-server-0.9.0\seata\bin\**seata-server.bat**

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020258.png)

# 四、订单/库存/账户业务数据库准备

> 前提条件：
> 

以下演示都需要先启动Nacos后启动Seata，保证两个都OK，Seata没启动报错no available server to connect。

## 4.1 分布式事务业务说明：

> 业务说明：
> 

这里我们会创建三个微服务，一个订单服务，一个库存服务，一个账户服务。

**当用户下单时，会在订单服务中创建一个订单，然后通过远程调用库存服务来扣减下单商品的库存，在通过远程调用账户服务来扣减用户账户里面的金额，最后在订单服务修改订单状态为已完成。**

该操作跨越了三个数据库，有两次远程调用，很明显会有分布式事务的问题。

一句话：下订单 -> 扣库存 -> 减余额

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020259.png)

- 下订单-->扣库存-->减账户（余额）

## 4.2 创建业务数据库

- seata_order：存储订单的数据库
- seata_storage：存储库存的数据库
- seata_account：存储账户信息的数据库

> 建表语句：
> 

```sql
CREATE DATABASE seata_order;
 
CREATE DATABASE seata_storage;
 
CREATE DATABASE seata_account;
```

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020260.png)

## 4.3 按照上述3库分别建对应业务表

- seata_order库下建t_order表
  
    ```sql
    CREATE TABLE t_order(
        `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
        `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
        `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
        `count` INT(11) DEFAULT NULL COMMENT '数量',
        `money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
        `status` INT(1) DEFAULT NULL COMMENT '订单状态：0：创建中; 1：已完结'
    ) ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
     
    SELECT * FROM t_order;
    ```
    
- seata_storage库下建t_storage表
  
    ```sql
    CREATE TABLE t_storage(
        `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
        `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
        `total` INT(11) DEFAULT NULL COMMENT '总库存',
        `used` INT(11) DEFAULT NULL COMMENT '已用库存',
        `residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
    ) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
     
    INSERT INTO seata_storage.t_storage(`id`,`product_id`,`total`,`used`,`residue`)
    VALUES('1','1','100','0','100');
     
     
    SELECT * FROM t_storage;
    ```
    
- seata_account库下建t_account表
  
    ```sql
    CREATE TABLE t_account(
        `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
        `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
        `total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
        `used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
        `residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
    ) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
     
    INSERT INTO seata_account.t_account(`id`,`user_id`,`total`,`used`,`residue`) VALUES('1','1','1000','0','1000')
     
     
     
    SELECT * FROM t_account;
    ```
    

## 4.4 按照上述3库分别建对应的回滚日志表

订单-库存-账户3个库下都需要建各自的回滚日志表。

我电脑本地地址：D:\develop\Utils\seata-server-0.9.0\seata\conf

\seata-server-0.9.0\seata\conf目录下的db_undo_log.sql。

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020261.png)

> 对应的回滚日志表，建表SQL：在这三个数据库中seata_order，seata_storage，seata_account分别执行以下SQL语句
> 

```sql
-- the table to store seata xid data
-- 0.7.0+ add context
-- you must to init this sql for you business databese. the seata server not need it.
-- 此脚本必须初始化在你当前的业务数据库中，用于AT 模式XID记录。与server端无关（注：业务数据库）
-- 注意此处0.3.0+ 增加唯一索引 ux_undo_log
drop table `undo_log`;  -- 删除表操作
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  `ext` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

> 最终效果：
> 

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020262.png)

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020263.png)

# 五、订单/库存/账户业务微服务准备

> 业务需求：
> 

下订单->减库存->扣余额->改（订单）状态

## 5.1 新建订单Order-Module

- 1.seata-order-service2001
- 2.POM
  
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
    
        <artifactId>seata-order-service2001</artifactId>
    
        <dependencies>
            <!--nacos-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <!--seata-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
                <exclusions>
                    <exclusion>
                        <artifactId>seata-all</artifactId>
                        <groupId>io.seata</groupId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
                <groupId>io.seata</groupId>
                <artifactId>seata-all</artifactId>
                <version>0.9.0</version>
            </dependency>
            <!--feign-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-openfeign</artifactId>
            </dependency>
            <!--web-actuator-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!--mysql-druid-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.37</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.10</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.0.0</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </project>
    ```
    
- 3.YML
  
    ```yaml
    server:
      port: 2001
    
    spring:
      application:
        name: seata-order-service
      cloud:
        alibaba:
          seata:
            #自定义事务组名称需要与seata-server中的对应
            tx-service-group: fsp_tx_group
        nacos:
          discovery:
            server-addr: localhost:8848
      datasource:
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://localhost:3306/seata_order
        username: root
        password: root
    
    feign:
      hystrix:
        enabled: false
    
    logging:
      level:
        io:
          seata: info
    
    mybatis:
      mapperLocations: classpath:mapper/*.xml
    ```
    
- 4.file.conf：D:\develop\Utils\seata-server-0.9.0\seata\conf\file.conf文件内容略有不同
  
    ```bash
    transport {
      # tcp udt unix-domain-socket
      type = "TCP"
      #NIO NATIVE
      server = "NIO"
      #enable heartbeat
      heartbeat = true
      #thread factory for netty
      thread-factory {
        boss-thread-prefix = "NettyBoss"
        worker-thread-prefix = "NettyServerNIOWorker"
        server-executor-thread-prefix = "NettyServerBizHandler"
        share-boss-worker = false
        client-selector-thread-prefix = "NettyClientSelector"
        client-selector-thread-size = 1
        client-worker-thread-prefix = "NettyClientWorkerThread"
        # netty boss thread size,will not be used for UDT
        boss-thread-size = 1
        #auto default pin or 8
        worker-thread-size = 8
      }
      shutdown {
        # when destroy server, wait seconds
        wait = 3
      }
      serialization = "seata"
      compressor = "none"
    }
    
    service {
    
      **vgroup_mapping.fsp_tx_group = "default" #修改自定义事务组名称**
    
      default.grouplist = "127.0.0.1:8091"
      enableDegrade = false
      disable = false
      max.commit.retry.timeout = "-1"
      max.rollback.retry.timeout = "-1"
      disableGlobalTransaction = false
    }
    
    client {
      async.commit.buffer.limit = 10000
      lock {
        retry.internal = 10
        retry.times = 30
      }
      report.retry.count = 5
      tm.commit.retry.count = 1
      tm.rollback.retry.count = 1
    }
    
    ## transaction log store
    store {
      ## store mode: file、db
      mode = "db"
    
      ## file store
      file {
        dir = "sessionStore"
    
        # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
        max-branch-session-size = 16384
        # globe session size , if exceeded throws exceptions
        max-global-session-size = 512
        # file buffer size , if exceeded allocate new buffer
        file-write-buffer-cache-size = 16384
        # when recover batch read size
        session.reload.read_size = 100
        # async, sync
        flush-disk-mode = async
      }
    
      ## database store
      db {
        ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
        datasource = "dbcp"
        ## mysql/oracle/h2/oceanbase etc.
        db-type = "mysql"
        driver-class-name = "com.mysql.jdbc.Driver"
        url = "jdbc:mysql://127.0.0.1:3306/seata"
        user = "root"
        password = "root"
        min-conn = 1
        max-conn = 3
        global.table = "global_table"
        branch.table = "branch_table"
        lock-table = "lock_table"
        query-limit = 100
      }
    }
    lock {
      ## the lock store mode: local、remote
      mode = "remote"
    
      local {
        ## store locks in user's database
      }
    
      remote {
        ## store locks in the seata's server
      }
    }
    recovery {
      #schedule committing retry period in milliseconds
      committing-retry-period = 1000
      #schedule asyn committing retry period in milliseconds
      asyn-committing-retry-period = 1000
      #schedule rollbacking retry period in milliseconds
      rollbacking-retry-period = 1000
      #schedule timeout retry period in milliseconds
      timeout-retry-period = 1000
    }
    
    transaction {
      undo.data.validation = true
      undo.log.serialization = "jackson"
      undo.log.save.days = 7
      #schedule delete expired undo_log in milliseconds
      undo.log.delete.period = 86400000
      undo.log.table = "undo_log"
    }
    
    ## metrics settings
    metrics {
      enabled = false
      registry-type = "compact"
      # multi exporters use comma divided
      exporter-list = "prometheus"
      exporter-prometheus-port = 9898
    }
    
    support {
      ## spring
      spring {
        # auto proxy the DataSource bean
        datasource.autoproxy = false
      }
    }
    ```
    
- 5.registry.conf：D:\develop\Utils\seata-server-0.9.0\seata\conf\registry.conf文件内容
  
    ```bash
    registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"
    
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6379"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      consul {
        cluster = "default"
        serverAddr = "127.0.0.1:8500"
      }
      etcd3 {
        cluster = "default"
        serverAddr = "http://localhost:2379"
      }
      sofa {
        serverAddr = "127.0.0.1:9603"
        application = "default"
        region = "DEFAULT_ZONE"
        datacenter = "DefaultDataCenter"
        cluster = "default"
        group = "SEATA_GROUP"
        addressWaitTime = "3000"
      }
      file {
        name = "file.conf"
      }
    }
    
    config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "file"
    
      nacos {
        serverAddr = "localhost"
        namespace = ""
      }
      consul {
        serverAddr = "127.0.0.1:8500"
      }
      apollo {
        app.id = "seata-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      etcd3 {
        serverAddr = "http://localhost:2379"
      }
      file {
        name = "file.conf"
      }
    }
    ```
    
- 5.registry.conf
  
    ```bash
    registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"
    
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6379"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      consul {
        cluster = "default"
        serverAddr = "127.0.0.1:8500"
      }
      etcd3 {
        cluster = "default"
        serverAddr = "http://localhost:2379"
      }
      sofa {
        serverAddr = "127.0.0.1:9603"
        application = "default"
        region = "DEFAULT_ZONE"
        datacenter = "DefaultDataCenter"
        cluster = "default"
        group = "SEATA_GROUP"
        addressWaitTime = "3000"
      }
      file {
        name = "file.conf"
      }
    }
    
    config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "file"
    
      nacos {
        serverAddr = "localhost"
        namespace = ""
      }
      consul {
        serverAddr = "127.0.0.1:8500"
      }
      apollo {
        app.id = "seata-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      etcd3 {
        serverAddr = "http://localhost:2379"
      }
      file {
        name = "file.conf"
      }
    }
    ```
    
- 5.registry.conf
  
    ```bash
    registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"
    
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6379"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      consul {
        cluster = "default"
        serverAddr = "127.0.0.1:8500"
      }
      etcd3 {
        cluster = "default"
        serverAddr = "http://localhost:2379"
      }
      sofa {
        serverAddr = "127.0.0.1:9603"
        application = "default"
        region = "DEFAULT_ZONE"
        datacenter = "DefaultDataCenter"
        cluster = "default"
        group = "SEATA_GROUP"
        addressWaitTime = "3000"
      }
      file {
        name = "file.conf"
      }
    }
    
    config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "file"
    
      nacos {
        serverAddr = "localhost"
        namespace = ""
      }
      consul {
        serverAddr = "127.0.0.1:8500"
      }
      apollo {
        app.id = "seata-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      etcd3 {
        serverAddr = "http://localhost:2379"
      }
      file {
        name = "file.conf"
      }
    }
    ```
    
- 6.domain类似entity
    - CommonResult
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.AllArgsConstructor;
        import lombok.Data;
        import lombok.NoArgsConstructor;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:19
         * @Version 1.0
         * @Description
         */
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class CommonResult<T> {
            private Integer code;
            private String message;
            private T data;
        
            public CommonResult(Integer code, String message) {
                this(code, message, null);
            }
        }
        ```
        
    - Order
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.AllArgsConstructor;
        import lombok.Data;
        import lombok.NoArgsConstructor;
        
        import java.math.BigDecimal;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:19
         * @Version 1.0
         * @Description
         */
        
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class Order {
            private Long id;
        
            private Long userId;
        
            private Long productId;
        
            private Integer count;
        
            private BigDecimal money;
        
            private Integer status; //订单状态：0：创建中；1：已完结
        }
        ```
    
- 7.Dao接口及实现
    - OrderDao：
      
        ```java
        package com.youliao.springcloud.alibaba.dao;
        
        import com.youliao.springcloud.alibaba.domain.Order;
        import org.apache.ibatis.annotations.Mapper;
        import org.apache.ibatis.annotations.Param;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:26
         * @Version 1.0
         * @Description
         */
        @Mapper
        public interface OrderDao {
            //新建订单
            void create(Order order);
        
            //修改订单状态，从零改为1
            void update(@Param("userId") Long userId, @Param("status") Integer status);
        }
        ```
        
    - resources文件夹下新建mapper文件夹后添加：OrderMapper.xml
      
        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
        
        <mapper namespace="com.atguigu.springcloud.alibaba.dao.OrderDao">
        
            <resultMap id="BaseResultMap" type="com.atguigu.springcloud.alibaba.domain.Order">
                <id column="id" property="id" jdbcType="BIGINT"/>
                <result column="user_id" property="userId" jdbcType="BIGINT"/>
                <result column="product_id" property="productId" jdbcType="BIGINT"/>
                <result column="count" property="count" jdbcType="INTEGER"/>
                <result column="money" property="money" jdbcType="DECIMAL"/>
                <result column="status" property="status" jdbcType="INTEGER"/>
            </resultMap>
        
            <insert id="create">
                insert into t_order (id,user_id,product_id,count,money,status)
                values (null,#{userId},#{productId},#{count},#{money},0);
            </insert>
        
            <update id="update">
                update t_order set status = 1
                where user_id=#{userId} and status = #{status};
            </update>
        
        </mapper>
        ```
    
- 8.Service接口及实现
    - OrderService
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        import com.youliao.springcloud.alibaba.domain.Order;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:32
         * @Version 1.0
         * @Description
         */
        public interface OrderService{
            void create(Order order);
        }
        ```
        
    - StorageService
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        import com.youliao.springcloud.alibaba.domain.CommonResult;
        import org.springframework.cloud.openfeign.FeignClient;
        import org.springframework.web.bind.annotation.PostMapping;
        import org.springframework.web.bind.annotation.RequestParam;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:33
         * @Version 1.0
         * @Description
         */
        @FeignClient(value = "seata-storage-service")
        public interface StorageService {
            @PostMapping(value = "/storage/decrease")
            CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
        }
        ```
        
    - AccountService
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        import com.youliao.springcloud.alibaba.domain.CommonResult;
        import org.springframework.cloud.openfeign.FeignClient;
        import org.springframework.web.bind.annotation.PostMapping;
        import org.springframework.web.bind.annotation.RequestParam;
        
        import java.math.BigDecimal;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:34
         * @Version 1.0
         * @Description
         */
        
        @FeignClient(value = "seata-account-service")
        public interface AccountService {
            @PostMapping(value = "/account/decrease")
            CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
        }
        ```
    
- 9.Controller
  
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import com.youliao.springcloud.alibaba.domain.CommonResult;
    import com.youliao.springcloud.alibaba.domain.Order;
    import com.youliao.springcloud.alibaba.service.OrderService;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import javax.annotation.Resource;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 22:36
     * @Version 1.0
     * @Description
     */
    @RestController
    public class OrderController {
        @Resource
        private OrderService orderService;
    
        @GetMapping("/order/create")
        public CommonResult create(Order order) {
            orderService.create(order);
            return new CommonResult(200, "订单创建成功");
        }
    }
    ```
    
- 10.Config配置
    - MyBatisConfig
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import org.mybatis.spring.annotation.MapperScan;
        import org.springframework.context.annotation.Configuration;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:37
         * @Version 1.0
         * @Description
         */
        @Configuration
        @MapperScan({"com.youliao.springcloud.alibaba.dao"})
        public class MyBatisConfig {
        
        }
        ```
        
    - DataSourceProxyConfig：Mybatis DataSourceProxyConfig配置，这里是使用Seata对数据源进行代理
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import com.alibaba.druid.pool.DruidDataSource;
        import io.seata.rm.datasource.DataSourceProxy;
        import org.apache.ibatis.session.SqlSessionFactory;
        import org.mybatis.spring.SqlSessionFactoryBean;
        import org.mybatis.spring.transaction.SpringManagedTransactionFactory;
        import org.springframework.beans.factory.annotation.Value;
        import org.springframework.boot.context.properties.ConfigurationProperties;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
        import javax.sql.DataSource;
        
        /**
         * @Author Dali
         * @Date 2021/8/2 22:39
         * @Version 1.0
         * @Description 使用Seata对数据源进行代理
         */
        @Configuration
        public class DataSourceProxyConfig {
        
            @Value("${mybatis.mapperLocations}")
            private String mapperLocations;
        
            @Bean
            @ConfigurationProperties(prefix = "spring.datasource")
            public DataSource druidDataSource() {
                return new DruidDataSource();
            }
        
            @Bean
            public DataSourceProxy dataSourceProxy(DataSource dataSource) {
                return new DataSourceProxy(dataSource);
            }
        
            @Bean
            public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
                SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
                sqlSessionFactoryBean.setDataSource(dataSourceProxy);
                sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
                sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
                return sqlSessionFactoryBean.getObject();
            }
        
        }
        ```
    
- 11.主启动
  
    ```java
    package com.youliao.springcloud.alibaba;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    /**
     * @Author Dali
     * @Date 2021/8/2 22:18
     * @Version 1.0
     * @Description
     */
    @EnableDiscoveryClient
    @EnableFeignClients
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class)//取消数据源自动创建的配置
    public class SeataOrderMainApp2001 {
    
        public static void main(String[] args) {
            SpringApplication.run(SeataOrderMainApp2001.class, args);
        }
    }
    ```
    

## 5.2 新建库存Storage-Module

- 1.seata-order-service2002
- 2.POM
  
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
    
        <artifactId>seata-order-service2002</artifactId>
    
        <dependencies>
            <!--nacos-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <!--seata-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
                <exclusions>
                    <exclusion>
                        <artifactId>seata-all</artifactId>
                        <groupId>io.seata</groupId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
                <groupId>io.seata</groupId>
                <artifactId>seata-all</artifactId>
                <version>0.9.0</version>
            </dependency>
            <!--feign-->
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
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.0.0</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.37</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.10</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
        </dependencies>
    
    </project>
    ```
    
- 3.YML
  
    ```yaml
    server:
      port: 2002
    
    spring:
      application:
        name: seata-storage-service
      cloud:
        alibaba:
          seata:
            tx-service-group: fsp_tx_group
        nacos:
          discovery:
            server-addr: localhost:8848
      datasource:
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://localhost:3306/seata_storage
        username: root
        password: root
    
    logging:
      level:
        io:
          seata: info
    
    mybatis:
      mapperLocations: classpath:mapper/*.xml
    ```
    
- 4.file.conf
  
    ```yaml
    transport {
      # tcp udt unix-domain-socket
      type = "TCP"
      #NIO NATIVE
      server = "NIO"
      #enable heartbeat
      heartbeat = true
      #thread factory for netty
      thread-factory {
        boss-thread-prefix = "NettyBoss"
        worker-thread-prefix = "NettyServerNIOWorker"
        server-executor-thread-prefix = "NettyServerBizHandler"
        share-boss-worker = false
        client-selector-thread-prefix = "NettyClientSelector"
        client-selector-thread-size = 1
        client-worker-thread-prefix = "NettyClientWorkerThread"
        # netty boss thread size,will not be used for UDT
        boss-thread-size = 1
        #auto default pin or 8
        worker-thread-size = 8
      }
      shutdown {
        # when destroy server, wait seconds
        wait = 3
      }
      serialization = "seata"
      compressor = "none"
    }
    
    service {
      #vgroup->rgroup
      vgroup_mapping.fsp_tx_group = "default"
      #only support single node
      default.grouplist = "127.0.0.1:8091"
      #degrade current not support
      enableDegrade = false
      #disable
      disable = false
      #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
      max.commit.retry.timeout = "-1"
      max.rollback.retry.timeout = "-1"
      disableGlobalTransaction = false
    }
    
    client {
      async.commit.buffer.limit = 10000
      lock {
        retry.internal = 10
        retry.times = 30
      }
      report.retry.count = 5
      tm.commit.retry.count = 1
      tm.rollback.retry.count = 1
    }
    
    transaction {
      undo.data.validation = true
      undo.log.serialization = "jackson"
      undo.log.save.days = 7
      #schedule delete expired undo_log in milliseconds
      undo.log.delete.period = 86400000
      undo.log.table = "undo_log"
    }
    
    support {
      ## spring
      spring {
        # auto proxy the DataSource bean
        datasource.autoproxy = false
      }
    }
    ```
    
- 5.registry.conf
  
    ```yaml
    registry {
      # file 、nacos 、eureka、redis、zk
      type = "nacos"
    
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6381"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      file {
        name = "file.conf"
      }
    }
    
    config {
      # file、nacos 、apollo、zk
      type = "file"
    
      nacos {
        serverAddr = "localhost"
        namespace = ""
        cluster = "default"
      }
      apollo {
        app.id = "fescar-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      file {
        name = "file.conf"
      }
    }
    ```
    
- 6.domain
    - CommonResult
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.AllArgsConstructor;
        import lombok.Data;
        import lombok.NoArgsConstructor;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:35
         * @Version 1.0
         * @Description
         */
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class CommonResult<T> {
            private Integer code;
            private String message;
            private T data;
        
            public CommonResult(Integer code, String message) {
                this(code, message, null);
            }
        }
        ```
        
    - Storage
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.Data;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:37
         * @Version 1.0
         * @Description
         */
        @Data
        public class Storage {
        
            private Long id;
        
            // 产品id
            private Long productId;
        
            //总库存
            private Integer total;
        
            //已用库存
            private Integer used;
        
            //剩余库存
            private Integer residue;
        }
        ```
        
    
- 7.Dao接口及实现
    - StorageDao
      
        ```java
        package com.youliao.springcloud.alibaba.dao;
        
        import org.apache.ibatis.annotations.Mapper;
        import org.apache.ibatis.annotations.Param;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:38
         * @Version 1.0
         * @Description
         */
        @Mapper
        public interface StorageDao {
            //扣减库存信息
            void decrease(@Param("productId") Long productId, @Param("count") Integer count);
        }
        ```
        
    - StorageMapper.xml：resources文件夹下新建mapper文件夹后添加
      
        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
        
        <mapper namespace="com.youliao.springcloud.alibaba.dao.StorageDao">
        
            <resultMap id="BaseResultMap" type="com.youliao.springcloud.alibaba.domain.Storage">
                <id column="id" property="id" jdbcType="BIGINT"/>
                <result column="product_id" property="productId" jdbcType="BIGINT"/>
                <result column="total" property="total" jdbcType="INTEGER"/>
                <result column="used" property="used" jdbcType="INTEGER"/>
                <result column="residue" property="residue" jdbcType="INTEGER"/>
            </resultMap>
        
            <update id="decrease">
                UPDATE
                    t_storage
                SET
                    used = used + #{count},residue = residue - #{count}
                WHERE
                    product_id = #{productId}
            </update>
        
        </mapper>
        ```
    
- 8.Service接口及实现
    - StorageService
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:42
         * @Version 1.0
         * @Description
         */
        public interface StorageService {
            // 扣减库存
            void decrease(Long productId, Integer count);
        }
        ```
        
    - StorageServiceImpl
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        import com.youliao.springcloud.alibaba.dao.StorageDao;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.stereotype.Service;
        
        import javax.annotation.Resource;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:43
         * @Version 1.0
         * @Description
         */
        
        @Service
        public class StorageServiceImpl implements StorageService {
        
            private static final Logger LOGGER = LoggerFactory.getLogger(StorageServiceImpl.class);
        
            @Resource
            private StorageDao storageDao;
        
            // 扣减库存
            @Override
            public void decrease(Long productId, Integer count) {
                LOGGER.info("------->storage-service中扣减库存开始");
                storageDao.decrease(productId, count);
                LOGGER.info("------->storage-service中扣减库存结束");
            }
        }
        ```
    
- 9.Controller：StorageController
  
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import com.youliao.springcloud.alibaba.domain.CommonResult;
    import com.youliao.springcloud.alibaba.service.StorageService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * @Author Dali
     * @Date 2021/8/3 10:45
     * @Version 1.0
     * @Description
     */
    
    @RestController
    public class StorageController {
    
        @Autowired
        private StorageService storageService;
    
        //扣减库存
        @RequestMapping("/storage/decrease")
        public CommonResult decrease(Long productId, Integer count) {
            storageService.decrease(productId, count);
            return new CommonResult(200, "扣减库存成功！");
        }
    }
    ```
    
- 10.Config配置
    - MyBatisConfig
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import org.mybatis.spring.annotation.MapperScan;
        import org.springframework.context.annotation.Configuration;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:46
         * @Version 1.0
         * @Description
         */
        @Configuration
        @MapperScan({"com.youliao.springcloud.alibaba.dao"})
        public class MyBatisConfig {
        }
        ```
        
    - DataSourceProxyConfig
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import com.alibaba.druid.pool.DruidDataSource;
        import io.seata.rm.datasource.DataSourceProxy;
        import org.apache.ibatis.session.SqlSessionFactory;
        import org.mybatis.spring.SqlSessionFactoryBean;
        import org.mybatis.spring.transaction.SpringManagedTransactionFactory;
        import org.springframework.beans.factory.annotation.Value;
        import org.springframework.boot.context.properties.ConfigurationProperties;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
        
        import javax.sql.DataSource;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 10:47
         * @Version 1.0
         * @Description
         */
        
        @Configuration
        public class DataSourceProxyConfig {
        
            @Value("${mybatis.mapperLocations}")
            private String mapperLocations;
        
            @Bean
            @ConfigurationProperties(prefix = "spring.datasource")
            public DataSource druidDataSource() {
                return new DruidDataSource();
            }
        
            @Bean
            public DataSourceProxy dataSourceProxy(DataSource dataSource) {
                return new DataSourceProxy(dataSource);
            }
        
            @Bean
            public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
                SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
                sqlSessionFactoryBean.setDataSource(dataSourceProxy);
                sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
                sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
                return sqlSessionFactoryBean.getObject();
            }
        
        }
        ```
    
- 11.主启动
  
    ```java
    package com.youliao.springcloud.alibaba;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    /**
     * @Author Dali
     * @Date 2021/8/3 10:34
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
    @EnableDiscoveryClient
    @EnableFeignClients
    public class SeataStorageServiceApplication2002 {
        public static void main(String[] args) {
            SpringApplication.run(SeataStorageServiceApplication2002.class, args);
        }
    }
    ```
    

## 5.3 新建账户Account-Module

- 1.seata-order-service2003
- 2.POM
  
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
    
        <artifactId>seata-order-service2003</artifactId>
    
        <dependencies>
            <!--nacos-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            </dependency>
            <!--seata-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
                <exclusions>
                    <exclusion>
                        <artifactId>seata-all</artifactId>
                        <groupId>io.seata</groupId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
                <groupId>io.seata</groupId>
                <artifactId>seata-all</artifactId>
                <version>0.9.0</version>
            </dependency>
            <!--feign-->
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
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.0.0</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.37</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.10</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </project>
    ```
    
- 3.YML
  
    ```yaml
    server:
      port: 2003
    
    spring:
      application:
        name: seata-account-service
      cloud:
        alibaba:
          seata:
            tx-service-group: fsp_tx_group
        nacos:
          discovery:
            server-addr: localhost:8848
      datasource:
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://localhost:3306/seata_account
        username: root
        password: root
    
    feign:
      hystrix:
        enabled: false
    
    logging:
      level:
        io:
          seata: info
    
    mybatis:
      mapperLocations: classpath:mapper/*.xml
    ```
    
- 4.file.conf
  
    ```bash
    transport {
      # tcp udt unix-domain-socket
      type = "TCP"
      #NIO NATIVE
      server = "NIO"
      #enable heartbeat
      heartbeat = true
      #thread factory for netty
      thread-factory {
        boss-thread-prefix = "NettyBoss"
        worker-thread-prefix = "NettyServerNIOWorker"
        server-executor-thread-prefix = "NettyServerBizHandler"
        share-boss-worker = false
        client-selector-thread-prefix = "NettyClientSelector"
        client-selector-thread-size = 1
        client-worker-thread-prefix = "NettyClientWorkerThread"
        # netty boss thread size,will not be used for UDT
        boss-thread-size = 1
        #auto default pin or 8
        worker-thread-size = 8
      }
      shutdown {
        # when destroy server, wait seconds
        wait = 3
      }
      serialization = "seata"
      compressor = "none"
    }
     
    service {
     
      vgroup_mapping.fsp_tx_group = "default" #修改自定义事务组名称
     
      default.grouplist = "127.0.0.1:8091"
      enableDegrade = false
      disable = false
      max.commit.retry.timeout = "-1"
      max.rollback.retry.timeout = "-1"
      disableGlobalTransaction = false
    }
     
     
    client {
      async.commit.buffer.limit = 10000
      lock {
        retry.internal = 10
        retry.times = 30
      }
      report.retry.count = 5
      tm.commit.retry.count = 1
      tm.rollback.retry.count = 1
    }
     
    ## transaction log store
    store {
      ## store mode: file、db
      mode = "db"
     
      ## file store
      file {
        dir = "sessionStore"
     
        # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
        max-branch-session-size = 16384
        # globe session size , if exceeded throws exceptions
        max-global-session-size = 512
        # file buffer size , if exceeded allocate new buffer
        file-write-buffer-cache-size = 16384
        # when recover batch read size
        session.reload.read_size = 100
        # async, sync
        flush-disk-mode = async
      }
     
      ## database store
      db {
        ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
        datasource = "dbcp"
        ## mysql/oracle/h2/oceanbase etc.
        db-type = "mysql"
        driver-class-name = "com.mysql.jdbc.Driver"
        url = "jdbc:mysql://127.0.0.1:3306/seata"
        user = "root"
        password = "123456"
        min-conn = 1
        max-conn = 3
        global.table = "global_table"
        branch.table = "branch_table"
        lock-table = "lock_table"
        query-limit = 100
      }
    }
    lock {
      ## the lock store mode: local、remote
      mode = "remote"
     
      local {
        ## store locks in user's database
      }
     
      remote {
        ## store locks in the seata's server
      }
    }
    recovery {
      #schedule committing retry period in milliseconds
      committing-retry-period = 1000
      #schedule asyn committing retry period in milliseconds
      asyn-committing-retry-period = 1000
      #schedule rollbacking retry period in milliseconds
      rollbacking-retry-period = 1000
      #schedule timeout retry period in milliseconds
      timeout-retry-period = 1000
    }
     
    transaction {
      undo.data.validation = true
      undo.log.serialization = "jackson"
      undo.log.save.days = 7
      #schedule delete expired undo_log in milliseconds
      undo.log.delete.period = 86400000
      undo.log.table = "undo_log"
    }
     
    ## metrics settings
    metrics {
      enabled = false
      registry-type = "compact"
      # multi exporters use comma divided
      exporter-list = "prometheus"
      exporter-prometheus-port = 9898
    }
     
    support {
      ## spring
      spring {
        # auto proxy the DataSource bean
        datasource.autoproxy = false
      }
    }
    ```
    
- 5.registry.conf
  
    ```bash
    registry {
      # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
      type = "nacos"
     
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        cluster = "default"
      }
      eureka {
        serviceUrl = "http://localhost:8761/eureka"
        application = "default"
        weight = "1"
      }
      redis {
        serverAddr = "localhost:6379"
        db = "0"
      }
      zk {
        cluster = "default"
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      consul {
        cluster = "default"
        serverAddr = "127.0.0.1:8500"
      }
      etcd3 {
        cluster = "default"
        serverAddr = "http://localhost:2379"
      }
      sofa {
        serverAddr = "127.0.0.1:9603"
        application = "default"
        region = "DEFAULT_ZONE"
        datacenter = "DefaultDataCenter"
        cluster = "default"
        group = "SEATA_GROUP"
        addressWaitTime = "3000"
      }
      file {
        name = "file.conf"
      }
    }
     
    config {
      # file、nacos 、apollo、zk、consul、etcd3
      type = "file"
     
      nacos {
        serverAddr = "localhost"
        namespace = ""
      }
      consul {
        serverAddr = "127.0.0.1:8500"
      }
      apollo {
        app.id = "seata-server"
        apollo.meta = "http://192.168.1.204:8801"
      }
      zk {
        serverAddr = "127.0.0.1:2181"
        session.timeout = 6000
        connect.timeout = 2000
      }
      etcd3 {
        serverAddr = "http://localhost:2379"
      }
      file {
        name = "file.conf"
      }
    }
    ```
    
- 6.domain
    - CommonResult
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.AllArgsConstructor;
        import lombok.Data;
        import lombok.NoArgsConstructor;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:09
         * @Version 1.0
         * @Description
         */
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class CommonResult<T> {
            private Integer code;
            private String message;
            private T data;
        
            public CommonResult(Integer code, String message) {
                this(code, message, null);
            }
        }
        ```
        
    - Account
      
        ```java
        package com.youliao.springcloud.alibaba.domain;
        
        import lombok.AllArgsConstructor;
        import lombok.Data;
        import lombok.NoArgsConstructor;
        
        import java.math.BigDecimal;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:10
         * @Version 1.0
         * @Description
         */
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class Account {
        
            private Long id;
        
            /**
             * 用户id
             */
            private Long userId;
        
            /**
             * 总额度
             */
            private BigDecimal total;
        
            /**
             * 已用额度
             */
            private BigDecimal used;
        
            /**
             * 剩余额度
             */
            private BigDecimal residue;
        }
        ```
    
- 7.Dao接口及实现
    - AccountDao
      
        ```java
        package com.youliao.springcloud.alibaba.dao;
        
        import org.apache.ibatis.annotations.Mapper;
        import org.apache.ibatis.annotations.Param;
        
        import java.math.BigDecimal;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:13
         * @Version 1.0
         * @Description
         */
        
        @Mapper
        public interface AccountDao {
        
            /**
             * 扣减账户余额
             */
            void decrease(@Param("userId") Long userId, @Param("money") BigDecimal money);
        }
        ```
        
    - AccountMapper.xml：resources文件夹下新建mapper文件夹后添加
      
        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
        
        <mapper namespace="com.youliao.springcloud.alibaba.dao.AccountDao">
        
            <resultMap id="BaseResultMap" type="com.youliao.springcloud.alibaba.domain.Account">
                <id column="id" property="id" jdbcType="BIGINT"/>
                <result column="user_id" property="userId" jdbcType="BIGINT"/>
                <result column="total" property="total" jdbcType="DECIMAL"/>
                <result column="used" property="used" jdbcType="DECIMAL"/>
                <result column="residue" property="residue" jdbcType="DECIMAL"/>
            </resultMap>
        
            <update id="decrease">
                UPDATE t_account
                SET
                  residue = residue - #{money},used = used + #{money}
                WHERE
                  user_id = #{userId};
            </update>
        
        </mapper>
        ```
    
- 8.Service接口及实现
    - AccountService
      
        ```java
        package com.youliao.springcloud.alibaba.service;
        
        import org.springframework.web.bind.annotation.RequestParam;
        
        import java.math.BigDecimal;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:16
         * @Version 1.0
         * @Description
         */
        
        public interface AccountService {
        
            /**
             * 扣减账户余额
             */
            void decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
        }
        ```
        
    - impl：AccountServiceImpl
      
        ```java
        package com.youliao.springcloud.alibaba.service.impl;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:16
         * @Version 1.0
         * @Description
         */
        
        import com.youliao.springcloud.alibaba.dao.AccountDao;
        import com.youliao.springcloud.alibaba.service.AccountService;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.stereotype.Service;
        
        import javax.annotation.Resource;
        import java.math.BigDecimal;
        import java.util.concurrent.TimeUnit;
        
        /**
         * 账户业务实现类
         */
        @Service
        public class AccountServiceImpl implements AccountService {
        
            private static final Logger LOGGER = LoggerFactory.getLogger(AccountServiceImpl.class);
        
            @Resource
            AccountDao accountDao;
        
            /**
             * 扣减账户余额
             */
            @Override
            public void decrease(Long userId, BigDecimal money) {
        
                LOGGER.info("------->account-service中扣减账户余额开始");
               /* try {
                    TimeUnit.SECONDS.sleep(20);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }*/
                accountDao.decrease(userId, money);
                LOGGER.info("------->account-service中扣减账户余额结束");
            }
        }
        ```
    
- 9.Controller
  
    ```java
    package com.youliao.springcloud.alibaba.controller;
    
    import com.youliao.springcloud.alibaba.domain.CommonResult;
    import com.youliao.springcloud.alibaba.service.AccountService;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;
    
    import javax.annotation.Resource;
    import java.math.BigDecimal;
    
    /**
     * @Author Dali
     * @Date 2021/8/3 11:19
     * @Version 1.0
     * @Description
     */
    @RestController
    public class AccountController {
    
        @Resource
        AccountService accountService;
    
        /**
         * 扣减账户余额
         */
        @RequestMapping("/account/decrease")
        public CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money){
            accountService.decrease(userId,money);
            return new CommonResult(200,"扣减账户余额成功！");
        }
    }
    ```
    
- 10.Config配置
    - MyBatisConfig
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import org.mybatis.spring.annotation.MapperScan;
        import org.springframework.context.annotation.Configuration;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:20
         * @Version 1.0
         * @Description
         */
        @Configuration
        @MapperScan({"com.youliao.springcloud.alibaba.dao"})
        public class MyBatisConfig {
        
        }
        ```
        
    - DataSourceProxyConfig
      
        ```java
        package com.youliao.springcloud.alibaba.config;
        
        import com.alibaba.druid.pool.DruidDataSource;
        import io.seata.rm.datasource.DataSourceProxy;
        import org.apache.ibatis.session.SqlSessionFactory;
        import org.mybatis.spring.SqlSessionFactoryBean;
        import org.mybatis.spring.transaction.SpringManagedTransactionFactory;
        import org.springframework.beans.factory.annotation.Value;
        import org.springframework.boot.context.properties.ConfigurationProperties;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
        
        import javax.sql.DataSource;
        
        /**
         * @Author Dali
         * @Date 2021/8/3 11:20
         * @Version 1.0
         * @Description
         */
        
        @Configuration
        public class DataSourceProxyConfig {
        
            @Value("${mybatis.mapperLocations}")
            private String mapperLocations;
        
            @Bean
            @ConfigurationProperties(prefix = "spring.datasource")
            public DataSource druidDataSource() {
                return new DruidDataSource();
            }
        
            @Bean
            public DataSourceProxy dataSourceProxy(DataSource dataSource) {
                return new DataSourceProxy(dataSource);
            }
        
            @Bean
            public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
                SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
                sqlSessionFactoryBean.setDataSource(dataSourceProxy);
                sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
                sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
                return sqlSessionFactoryBean.getObject();
            }
        
        }
        ```
    
- 11.主启动
  
    ```java
    package com.youliao.springcloud;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    /**
     * @Author Dali
     * @Date 2021/8/3 11:07
     * @Version 1.0
     * @Description
     */
    @SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
    @EnableDiscoveryClient
    @EnableFeignClients
    public class SeataAccountMainApp2003 {
        public static void main(String[] args) {
            SpringApplication.run(SeataAccountMainApp2003.class, args);
        }
    }
    ```
    

# 六、Test

下订单->减库存->扣余额->改（订单）状态

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020264.png)

> 数据库初始情况：
> 

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020265.png)

- 正常下单
  
    [](http://localhost:2001/order/create?userId=1&productId=1&count=10&money=100)
    
    ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020266.png)
    
    > 数据库情况
    > 
    
    ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020267.png)
    
    ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020268.png)
    
- 超时异常，没加@GlobalTransactional
  
    我们在account-module模块，添加睡眠时间20秒，因为openFeign默认时间是1秒,
    
    ![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2030.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020269.png)
    
    出现了数据不一致的问题
    
    故障情况
    
    - 当库存和账户金额扣减后，订单状态并没有设置成已经完成，没有从零改成1
    - 而且由于Feign的重试机制，账户余额还有可能被多次扣除
- 超时异常，添加@GlobalTransactional
  
    ```java
    @GlobalTransactional(name = "fsp-create-order",rollbackFor = Exception.class)
    ```
    
    rollbackFor表示，什么什么错误就会回滚
    
    添加这个后，发现下单后的数据库并没有改变，记录都添加不进来
    

# 七、Seata之原理简介

## 7.1 Seata

2019年1月份蚂蚁金服和阿里巴巴共同开源的分布式事务解决方案

2019年1月份，蚂蚁金服和阿里巴巴共同开源的分布式事务解决方案

Seata：Simple Extensible Autonomous Transaction Architecture，简单可扩展自治事务框架

2020起始，参加工作以后用1.0以后的版本。

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020270.png)

## 7.2 再看TC/TM/RM三大组件

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2032.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020271.png)

什么是TC，TM，RM

TC：seata服务器

TM：带有@GlobalTransaction注解的方法

RM：数据库，也就是事务参与方

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2033.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020272.png)

## 7.3 分布式事务的执行流程

- TM开启分布式事务（TM向TC注册全局事务记录），相当于注解 `@GlobelTransaction`注解
- 按业务场景，编排数据库，服务等事务内部资源（RM向TC汇报资源准备状态）
- TM结束分布式事务，事务一阶段结束（TM通知TC提交、回滚分布式事务）
- TC汇总事务信息，决定分布式事务是提交还是回滚
- TC通知所有RM提交、回滚资源，事务二阶段结束

## 7.4 AT模式如何做到对业务的无侵入（面试）

默认AT模式，阿里云GTS

### AT模式

### 前提

- 基于支持本地ACID事务的关系型数据库
- Java应用，通过JDBC访问数据库

### 整体机制

两阶段提交协议的演变

- 一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源
- 二阶段
    - 提交异步化，非常快速的完成
    - 回滚通过一阶段的回滚日志进行反向补偿

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2034.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020273.png)

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2035.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020274.png)

### AT模式：一阶段加载

在一阶段，Seata会拦截 业务SQL

- 解析SQL语义，找到业务SQL，要更新的业务数据，在业务数据被更新前，将其保存成 `before image（前置镜像）`
- 执行业务SQL更新业务数据，在业务数据更新之后
- 将其保存成 after image，最后生成行锁

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2036.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020275.png)

以上操作全部在一个数据库事务内完成，这样保证了一阶段操作的原子性

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2037.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020276.png)

### AT模式：二阶段提交

二阶段如果顺利提交的话，因为业务SQL在一阶段已经提交至数据库，所以Seata框架只需将一阶段保存的快照和行锁删除掉，完成数据清理即可

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2038.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020277.png)

### AT模式：二阶段回滚

二阶段如果回滚的话，Seata就需要回滚到一阶段已经执行的 业务SQL，还原业务数据

回滚方式便是用 before image 还原业务数据，但是在还原前要首先校验脏写，对比数据库当前业务数据 和after image，如果两份数据完全一致，没有脏写，可以还原业务数据，如果不一致说明有脏读，出现脏读就需要转人工处理

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2039.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020278.png)

## 总结

![18_SpringCloud%20Alibaba%20Seata%E5%A4%84%E7%90%86%E5%88%86%E5%B8%83%E5%BC%8F%E4%BA%8B%E5%8A%A1%20e5a360da1d7b4d3992a7e53132bb9cbd/Untitled%2040.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206082020279.png)