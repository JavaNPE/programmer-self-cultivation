# 06_商品服务-API-三级分类查询-配置网关路由与路径重写-解决跨域-同源策略-拖拽效果-三级分类删除批量删除-小结

**前言准备：**

vue项目开放端口太麻烦了，其他主机访问不了。防火墙输入策略和host什么的修改过，nacos等其他服务能正常访问，vue项目别的主机访问不了，不知道哪里有限制可以在`config/index.js`配置vue项目的`ip`和`端口`

# 45、商品服务-API-三级分类查询-递归树形结构数据获取

此处三级分类最起码得启动以下服务：

1. `renren-fast`
2. `nacos`
3. `gulimall-gateway`
4. `gulimall-product`

## 45.2 三级分类：对应的数据库表：`pms_category` 表结构说明

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

![本人用的是网上提供的数据库SQL脚本](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

本人用的是网上提供的数据库SQL脚本

三级分类`pms_category` 表结构说明

```sql
cat_id：分类id，cat代表分类，bigint(20)
name：分类名称
parent_cid：在哪个父目录下
cat_level：分类层级
show_status：是否显示，用于逻辑删除
sort：同层级同父目录下显示顺序
ico图标，product_unit商品计量单位，
InnoDB表，自增大小1437，utf编码，动态行格式
```

## 45.2 SPU与SKU

## 45.3 基本属性与销售属性

# 46、商品服务-API-三级分类配置网关路由与路径重写

## 46.1 VSCode中启动**renren-fast-vue**前端项目命令：`npm run dev` | 账号&密码均为：`admin`

p47 如果注释了renren-fast里的跨域代码还是报错，显示配置了两个跨域，可以去删了renren-fast下的target目录，然后把renren-fast这个项目maven install一下应该可以解决，顶上去，让兄弟们看见！

## **验证码不显示问题**

F12打开看了一下，验证码图片的端口还是8080，也就是说，你的renren-fast项目的端口更改了，而前端项目不知道你改过了。后面的笔记中有个【网关88】小节，去那里看吧

接着操作后台

localhost:8001 ， 点击系统管理，菜单管理，新增

- 目录
- `商品系统`
- 一级菜单

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

刷新，看到左侧多了商品系统，添加的这个菜单其实是添加到了guli-admin.sys_menu表里

(新增了memu_id=31 parent_id=0 name=商品系统 icon=editor )

继续新增：

菜单
分类维护
商品系统
product/category
…
menu

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

guli-admin.sys_menu表又多了一行，父id是刚才的商品系统id

### 菜单路由

在左侧点击【商品系统-分类维护】，希望在此展示3级分类。可以看到

url是`http://localhost:8001/#/product-category`
填写的菜单路由是`product/category`对应的视图是`src/view/modules/product/category.vue`
再如`sys-role`具体的视图在`renren-fast-vue/views/modules/sys/role.vue`

所以要自定义我们的product/category视图的话，就是创建`mudules/product/category.vue`

输入**vue快捷生成模板**，然后去`https://element.eleme.cn/#/zh-CN/component/tree`

看如何使用多级目录

el-tree中的data是要展示的树形数据
props属性设置
@node-click单击函数

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/tree)

以下代码参考王站地址 ： Tree 树形控件

- 三级菜单：Tree 树形控件
  
    ```html
    <el-tree :data="data" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
    
    <script>
      export default {
        data() {
          return {
            data: [{
              label: '一级 1',
              children: [{
                label: '二级 1-1',
                children: [{
                  label: '三级 1-1-1'
                }]
              }]
            }, {
              label: '一级 2',
              children: [{
                label: '二级 2-1',
                children: [{
                  label: '三级 2-1-1'
                }]
              }, {
                label: '二级 2-2',
                children: [{
                  label: '三级 2-2-1'
                }]
              }]
            }, {
              label: '一级 3',
              children: [{
                label: '二级 3-1',
                children: [{
                  label: '三级 3-1-1'
                }]
              }, {
                label: '二级 3-2',
                children: [{
                  label: '三级 3-2-1'
                }]
              }]
            }],
            defaultProps: {
              children: 'children',
              label: 'label'
            }
          };
        },
        methods: {
          handleNodeClick(data) {
            console.log(data);
          }
        }
      };
    </script>
    ```
    
- 参照Tree树形组件：创建我们自己的`modules/product/category.vue`
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <el-tree
        :data="data"
        :props="defaultProps"
        @node-click="handleNodeClick"
      ></el-tree>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          data: [],
          defaultProps: {
            children: "children",
            label: "label",
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        handleNodeClick(data) {
          console.log(data);
        },
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category//list/tree"),   //给后台发送请求
            method: "get",
            // params: this.$http.adornParams({
            //   page: this.pageIndex,
            //   limit: this.pageSize,
            //   roleName: this.dataForm.roleName,
            // }),
          }).then(({ data }) => {
            console.log("成功获取到菜单数据。。。", data);
            // if (data && data.code === 0) {
            //   this.dataList = data.page.list;
            //   this.totalPage = data.page.totalCount;
            // } else {
            //   this.dataList = [];
            //   this.totalPage = 0;
            // }
            // this.dataListLoading = false;
          });
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
          this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

## gulimall-gateway**网关88端口**

在登录管理后台的时候，我们会发现，他要请求`localhost:8080/renren-fast/product/category/list/tree`这个url

但是报错404找不到，此处就解决登录页验证码不显示的问题。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

他要给8080发请求读取数据，但是数据是在10000端口上，如果找到了这个请求改端口那改起来很麻烦。方法1是改vue项目里的全局配置，方法2是搭建个网关，让网关路由到10000（即将vue项目里的请求都给网关，网关经过url处理后，去nacos里找到管理后台的微服务，就可以找到对应的端口了，这样我们就无需管理端口，统一交给网关管理端口接口）

> Ctrl+Shift+F全局搜索：VSCode中
> 

在`static/config/index.js`里

```bash
window.SITE_CONFIG['baseUrl'] = 'http://localhost:88/api';
// 意思是说本vue项目中要请求的资源url都发给88/api，那么我们就让网关端口为88，然后匹配到/api请求即可，
// 网关可以通过过滤器处理url后指定给某个微服务
// renren-fast服务已经注册到了nacos中

-----------------------------------------下面是全部内容----------------------------------------

/**
 * 开发环境
 */
;(function () {
  window.SITE_CONFIG = {};

  // api接口请求地址
  **window.SITE_CONFIG['baseUrl'] = 'http://localhost:88/api';**

  // cdn地址 = 域名 + 版本号
  window.SITE_CONFIG['domain']  = './'; // 域名
  window.SITE_CONFIG['version'] = '';   // 版本号(年月日时分)
  window.SITE_CONFIG['cdnUrl']  = window.SITE_CONFIG.domain + window.SITE_CONFIG.version;
})();
```

接着让重新登录`http://localhost:8001/#/login`，验证码是请求88的，所以不显示。而验证码是来源于fast后台的

- 现在的验证码请求路径为，[http://localhost:88/api/captcha.jpg?uuid=69c79f02-d15b-478a-8465-a07fd09001e6](http://localhost:88/api/captcha.jpg?uuid=69c79f02-d15b-478a-8465-a07fd09001e6)
- 原始的验证码请求路径：[http://localhost:8001/renren-fast/captcha.jpg?uuid=69c79f02-d15b-478a-8465-a07fd09001e6](http://localhost:8001/renren-fast/captcha.jpg?uuid=69c79f02-d15b-478a-8465-a07fd09001e6)

88是gateway的端口：application.properties

```bash
# gateway微服务的配置
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
spring.application.name=gulimall-gateway
server.port=88
```

问题：他要去nacos中查找api服务，但是nacos里有的是fast服务，就通过网关过滤器把api改成fast服务

所以让fast注册到服务注册中心，这样请求88网关转发到8080fast

让fast里加入注册中心的依赖，而common中有nacos依赖，所以引入common

```xml
<dependency>
    <!-- 里面有nacos注册中心 -->
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

在`renren-fast` 项目application.yml中添加

```yaml
spring:
  application:
    name: renren-fast  # 意思是把renren-fast项目也注册到nacos中(后面不再强调了)，这样网关才能转发给
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 # nacos

        
server: 
  port: 88  # 网关端口为88
```

然后在`renren-fast`启动类上加上注解`@EnableDiscoveryClient`，重启

如果报错gson依赖，就导入google的gson依赖

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>
```

然后在nacos的服务列表里看到了renren-fast

在`gulimall-gateway`中application.yml按格式加入，lb代表负载均衡，

```yaml
- id: admin_route
          uri: lb://renren-fast # 路由给renren-fast
          predicates:  # 什么情况下路由给它
            - Path=/api/** # 默认前端项目都带上api前缀，就是我们前面题的localhost:88/api
          filters:
            - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}  # 把/api/* 改变成 /renren-fast/*
 # fast找

----------------------------------以下是全部：application.yml----------------------------------------

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

        - id: admin_route
          uri: lb://renren-fast # 路由给renren-fast
          predicates:  # 什么情况下路由给它
            - Path=/api/** # 默认前端项目都带上api前缀，就是我们前面题的localhost:88/api
          filters:
            - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}  # 把/api/* 改变成 /renren-fast/*

## 前端项目，统一加上  /api 统一标识符
## http://localhost:88/api/captcha.jpg    实际获取到的验证码请求地址：http://localhost:8080/renren-fast/captcha.ipg
```

### 网关-路径重写

上面已经直接写好了，这里只是说明一下，无需修改

修改过vue里的api后，此时验证码请求的是`http://localhost:88/api/captcha.jpg?uuid=72b9da67-0130-4d1d-8dda-6bfe4b5f7935`

也就是说，他请求网关，路由到了fast，然后取nacos里找fast。

找到后拼接成了`http://renren-fast:8080/api/captcha.jpg`

但是正确的是`localhost:8080/renren-fast/captcha.jpg`

所以要利用网关带的路径重写，

路径重写参考：[https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-rewritepath-gatewayfilter-factory](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-rewritepath-gatewayfilter-factory)

登录，还是报错：（出现了跨域的问题，就是说vue项目是8001端口，却要跳转到88端口，为了安全性，不可以）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

完整的异常提示：

Non-resolvable parent POM: Could not find artifact com.ecp:ecp-main:pom:0.0.1-SNAPSHOT and 'parent.relativePath' points at wrong local POM @ line 8, column 10 -> [Help 2]

原因：多模块项目构建时，**先将parent项目要先install一回**，**之后子项目才可以运行mvn compile命令,否则就会报如上异常**。

# 47、商品服务-API-三级分类-网关统一配置跨域 | 同源策略

问题描述：已拦截跨源请求：同源策略禁止8001端口页面读取位于 [http://localhost:88/api/sys/login](http://localhost:88/api/sys/login) 的远程资源。（原因：CORS 头缺少 ‘Access-Control-Allow-Origin’）。

问题分析：这是一种跨域问题。访问的域名或端口和原来请求的域名端口一旦不同，请求就会被限制

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

## 什么是跨域及同源策略：

**跨域：**指的是浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成的，是浏览器对`js`施加的安全限制。（ajax可以）

**同源策略：**是指协议，域名，端囗都要相同，其中有一个不同都会产生跨域；

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

## 跨域流程：

这个跨域请求的实现是通过预检请求实现的，先发送一个`OPSTIONS`探路，收到响应允许跨域后再发送真实请求。

什么意思呢？跨域是要请求的、新的端口那个服务器限制的，不是浏览器限制的。

![image-20220530150953595](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530150953595.png)

## 解决跨域：方式一：使用nginx部署为同一域 （开发过程中稍微麻烦点）

### **跨域的解决方案**

- 方法1：设置nginx包含admin和gateway。都先请求nginx，这样端口就统一了
- 方法2：让服务器告诉预检请求能跨域

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

## 解决跨域：方式二：配置当次请求允许跨域（开发期间使用的是这种）| 网关里写个filter配置类

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2011.png)

Access-Control-Allow-Origin ： 支持哪些来源的请求跨域
Access-Control-Allow-Method ： 支持那些方法跨域
Access-Control-Allow-Credentials ：跨域请求默认不包含cookie，设置为true可以包含cookie
Access-Control-Expose-Headers ： 跨域请求暴露的字段
CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：
Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma
如果想拿到其他字段，就必须在Access-Control-Expose-Headers里面指定。
Access-Control-Max-Age ：表明该响应的有效时间为多少秒。在有效时间内，浏览器无须为同一请求再次发起预检请求。请注意，浏览器自身维护了一个最大有效时间，如果该首部字段的值超过了最大有效时间，将失效

解决方法：在网关中定义“`GulimallCorsConfiguration`”类，该类用来做过滤，允许所有的请求跨域。

```java
package com.atguigu.gulimall.gateway.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.reactive.CorsConfigurationSource;
import org.springframework.web.cors.reactive.CorsWebFilter;
import org.springframework.web.cors.reactive.UrlBasedCorsConfigurationSource;
import org.springframework.web.server.ServerWebExchange;

/**
 * @Author Dali
 * @Date 2021/8/19 21:26
 * @Version 1.0
 * @Description: 解决跨域问题
 */
@Configuration // gateway
public class GulimallCorsConfiguration {
    @Bean  // 添加过滤器
    public CorsWebFilter corsWebFilter() {
        // 基于url跨域，选择reactive包下的
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        // 跨域配置信息
        CorsConfiguration corsConfiguration = new CorsConfiguration();

        //1、配置跨域问题
        corsConfiguration.addAllowedHeader("*");    // 允许跨域的头
        corsConfiguration.addAllowedMethod("*");    // 允许跨域的请求方式
        corsConfiguration.addAllowedOrigin("*");    // 允许跨域的请求来源
        corsConfiguration.setAllowCredentials(true);    // 是否允许携带cookie跨域

        source.registerCorsConfiguration("/**", corsConfiguration); // 任意url都要进行跨域配置
        return new CorsWebFilter(source);
    }
}
```

再次访问：[http://localhost:8001/#/login](http://localhost:8001/#/login)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)

[http://localhost:8001/renren](http://localhost:8001/renren)

已拦截跨源请求：同源策略禁止读取位于 [http://localhost:88/api/sys/login](http://localhost:88/api/sys/login) 的远程资源。

（原因：不允许有多个 ‘Access-Control-Allow-Origin’ CORS 头）

renren-fast/captcha.jpg?uuid=69c79f02-d15b-478a-8465-a07fd09001e6

出现了多个请求，并且也存在多个跨源请求。

为了解决这个问题，需要修改renren-fast项目，注释掉“`io.renren.config.CorsConfig`”类。然后再次进行访问。

注释`renren-fast` 工程下的`CorsConfig` 配置类，然后重启`renren-fast：RenrenApplication :8080`/和`gulimall-gateway：GulimallGatewayApplication :88/`。记得启动`gulimall-product：GulimallProductApplication :10000/` 服务。重新访问：[http://localhost:8001/#/login](http://localhost:8001/#/login)

```java
/**
 * Copyright (c) 2016-2019 人人开源 All rights reserved.
 *
 * https://www.renren.io
 *
 * 版权所有，侵权必究！
 */

package io.renren.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig implements WebMvcConfigurer {

//    @Override
//    public void addCorsMappings(CorsRegistry registry) {
//        registry.addMapping("/**")
//            .allowedOrigins("*")
//            .allowCredentials(true)
//            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
//            .maxAge(3600);
//    }
}
```

# 48、商品服务-API-三级分类查询-树形展示三级分类数据

## product请求路径重写

> 之前解决了登录验证码的问题，**/api/**请求重写成了**/renren-fast**，但是vue项目中或者你自己写的数据库中有些是以**/product**为前缀的，它要请求product微服务，你要也让它请求renren-fast显然是不合适的。
**解决办法是把请求在网关中以更小的范围先拦截一下，剩下的请求再交给renren-fast**
> 

重启各个服务，并重新登录：[http://localhost:8001/#/login](http://localhost:8001/#/login)，进入分类维护页面，F12查看网络，发现所有跟商品有关的服务都是**/api/product**/category/list/tree，所以我们需要在gulimall-gateway重新配置一个路径重写的配置。

在显示**商品系统/分类信息**的时候，出现了**404异常**，请求的**`http://localhost:88/api/product/category/list/tree`**不存在（请求发送的是这个，但是不是我们需要的）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

这是因为网关上所做的路径映射不正确，映射后的路径为**http://localhost:8001/renren-fast/product/category/list/tree**

但是只有通过`http://localhost:10000/product/category/list/tree`路径才能够正常访问，所以会报404异常。

解决方法就是定义一个product路由规则，进行路径重写：
在gulimall-gateway项目中的application.yml添加以下信息：

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

# 注意放的位置：精确路由要放在模糊路由上面，否则容易出现不必要的问题
        **- id: product_route
          uri: lb://gulimall-product # 注册中心的服务
          predicates:
            - Path=/api/product/**
          filters:
            - RewritePath=/api/(?<segment>/?.*),/$\{segment}**

        - id: admin_route
          uri: lb://renren-fast # 路由给renren-fast
          predicates:  # 什么情况下路由给它
            - Path=/api/** # 默认前端项目都带上api前缀，就是我们前面题的localhost:88/api
          filters:
            - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}  # 把/api/* 改变成 /renren-fast/*

## 前端项目，统一加上  /api 统一标识符
## http://localhost:88/api/captcha.jpg    实际获取到的验证码请求地址：http://localhost:8080/renren-fast/captcha.ipg

## http://localhost:88/api/product/category/list/tree不存在（请求发送的是这个，但是不是我们需要的）,
##只有通过http://localhost:10000/product/category/list/tree路径才能够正常访问
```

使用nacos配置中心，可以这么做：在nacos中新建命名空间“product”，用命名空间隔离项目，(可以在其中新建**gulimall-product.yml，视频没有添加此配置**)

在gulimall-product项目下新建bootstrap.properties文件并**添加nacos服务注册与发现配置，**

```bash
spring.application.name=gulimall-product
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
spring.cloud.nacos.config.namespace=a5d48f18-a529-48dc-bd2f-7ca552bd5541
```

**nacos配置中心地址：**[http://192.168.56.1:8848/nacos](http://192.168.56.1:8848/nacos)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

在gulimall-product中的`application.yml` 配置nacos注册中心，并在gulimall-product主启动类中添加：@EnableDiscoveryClient注解，启用弄个配置。

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_pms
    driver-class-name: com.mysql.jdbc.Driver
  **cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848**

# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto

server:
  port: 10000
```

重启gulimall-product：GulimallProductApplication :10000/服务访问nacos：[http://192.168.56.1:8848/nacos](http://192.168.56.1:8848/nacos)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

访问 [localhost:88/api/product/category/list/tree](http://localhost:88/api/product/category/list/tree) 提示**{"msg":"invalid token","code":401}**，非法令牌，后台管理系统中没有登录，所以没有带令牌。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

原因：**先匹配的先路由**，renren-fast和gulimall-product路由重叠，fast要求登录。

修正：**在路由规则的顺序上**，**将精确的路由规则放置到模糊的路由规则的前面**，否则的话，精确的路由规则将不会被匹配到，类似于异常体系中try catch子句中异常的处理顺序。

在gulimall-gateway项目中的application.yml修改顺序：（前期修改之后的不必要修改）

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

        ##将精确的路由规则放置到模糊的路由规则的前面--- Path=/api/product/**是精确路由
        - id: product_route
          uri: lb://gulimall-product # 注册中心的服务
          predicates:
            - Path=/api/product/**
          filters:
            - RewritePath=/api/(?<segment>/?.*),/$\{segment}
        ## 是模糊路由
        - id: admin_route
          uri: lb://renren-fast # 路由给renren-fast
          predicates:  # 什么情况下路由给它
            - Path=/api/** # 默认前端项目都带上api前缀，就是我们前面题的localhost:88/api
          filters:
            - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}  # 把/api/* 改变成 /renren-fast/*

## 前端项目，统一加上  /api 统一标识符
## http://localhost:88/api/captcha.jpg    实际获取到的验证码请求地址：http://localhost:8080/renren-fast/captcha.ipg
## http://localhost:88/api/product/category/list/tree不存在（请求发送的是这个，但是不是我们需要的）,只有通过http://localhost:10000/product/category/list/tree路径才能够正常访问
```

重启网关服务：gulimall-gateway：GulimallProductApplication :10000/再次访问以下连接：

访问：[http://localhost:88/api/product/category/list/tree](http://localhost:88/api/product/category/list/tree) 正常

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

访问：http://localhost:8001/#/product-category ，正常进入登录界面。

**原因是**：先访问gulimall-gateway网关88，网关路径重写后访问nacos8848，nacos找到服务，接着修改前端category.vue，这里改的是点击分类维护后的右侧显示。

`**data解构，加上{}**`，把data的地方改成menus

```bash
getMenus() {
      //   this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
        method: "get",
      }).then((**[{ data }](https://www.notion.so/06_-API-ea6a9cc47c4e455b993351635837391f)**) => {   // success 响应到数据后填充到绑定的标签中
        console.log("成功获取到菜单数据。。。", data.data);
        this.menus = data.data;  // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
      });
    },
```

此时有了3级结构，但是没有数据，在category.vue的模板中，数据是menus，而还有一个props。这是element-ui的规则，

```bash
<template>
  <!-- <div class=''>三级分类维护</div> -->
  <el-tree
    **:data="menus"**
    :props="defaultProps"
    @node-click="handleNodeClick"
  ></el-tree>
</template>

-----------------------------------------------------------------------

data() {
    return {
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",  //这里其实对应的是数据库表pms_category里面的name字段
      },
    };
  },
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

# 49、商品服务-API-三级分类删除-页面效果

删除前提：没有子菜单、没有被其他菜单引用。

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/tree)

`参考的是scoped slot那部分代码`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)

- 使用 render-content，渲染函数
- **使用 scoped slot**：[https://cn.vuejs.org/v2/guide/components-slots.html](https://cn.vuejs.org/v2/guide/components-slots.html)

可以通过两种方法进行树节点内容的自定义：`render-content`和 `scoped slot`。使用`render-content`指定渲染函数，该函数返回需要的节点区内容即可。渲染函数的用法请参考 `Vue` 文档。使用 `scoped slot` 会传入两个参数`node`和`data`，分别表示当前节点的 `Node` 对象和当前节点的数据。注意：由于 `jsfiddle`不支持 `JSX` 语法，所以`render-content`示例在 `jsfiddle` 中无法运行。但是在实际的项目中，只要正确地配置了相关依赖，就可以正常运行。

### node与data

在element-ui的tree中，有2个非常重要的属性

**node代表当前结点**（是否展开等信息，element-ui自带属性），
**data是结点数据，是自己的数据**。
**data从哪里来：**前面ajax发送请求，拿到data，赋值给menus属性，而menus属性绑定到标签的data属性。而node是ui的默认规则

D:\develop\workspace\renren-fast-vue\**src\views\modules\product\category.vue**

- **category.vue**
  
    ```html
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <el-tree
        :data="menus"
        :props="defaultProps"
        **:expand-on-click-node="false"
        show-checkbox
        node-key="catId">**
        **<span class="custom-tree-node" slot-scope="{ node, data }">
          <span>{{ node.label }}</span>
          <span>
            <el-button
              v-if="node.level <= 2"
              type="text"
              size="mini"
              @click="() => append(data)">Append</el-button>
            <el-button
              v-if="node.childNodes.length == 0"
              type="text"
              size="mini"
              @click="() => remove(node, data)">Delete</el-button>
          </span>
        </span>**
      </el-tree>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          menus: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        **append(data) {
          console.log("append", data);
        },
    
        remove(node, data) {
          console.log("remove", node, data);
        },**
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

# 50、商品服务API-三级分类删除逻辑删除

## @RequestBody：这个注解就是想要获取请求体，必须发送POST请求（Get请求是没有请求体的） SpringMVC会自动将请求体中的数据（一般是JSON）,转为对应的对象。

gulimall-product服务中的CategoryController类：商品三级分类

```java
/**
     * 删除
     * @RequestBody：这个注解就是想要获取请求体，必须发送POST请求（Get请求是没有请求体的）
     * SpringMVC会自动将请求体中的数据（一般是JSON）,转为对应的对象
     */
    @RequestMapping("/delete")
    //@RequiresPermissions("product:category:delete")
    public R delete(@RequestBody Long[] catIds) {
        categoryService.removeByIds(Arrays.asList(catIds));

        return R.ok();
    }
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)

测试删除数据，打开postman输入“ [http://localhost:88/api/product/category/delete](http://localhost:88/api/product/category/delete) ”，请求方式设置为POST，为了比对效果，可以在删除之前，查询数据库的pms_category表：发现catId为1432的内容没了，被删掉了。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)

这是逆向工程给我们自动生成的删除功能，但是**现实生成中我们使用的是逻辑删除**，并不是这么点单就把这条数据直接从数据库中直接删掉的。

## 逻辑删除：参照mybatis-plus官网步骤

然而多数时候，我们并不希望删除数据，而是标记它被删除了，这就是逻辑删除；

逻辑删除是mybatis-plus 的内容，会在项目中配置一些内容，告诉此项目执行delete语句时并不删除，只是标志位。

假设数据库中有字段**show_status为0**，标记它已经被删除。

![psm_category表](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)

psm_category表

mybatis-plus的逻辑删除：[https://baomidou.com/guide/logic-delete.html#使用方法](https://baomidou.com/guide/logic-delete.html#%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)

### mybatis-plus逻辑删除**说明:**

只对自动注入的sql起效:

- 插入: 不作限制
- 查找: 追加where条件过滤掉已删除数据,且使用 wrapper.entity 生成的where条件会忽略该字段
- 更新: 追加where条件防止更新到已删除数据,且使用 wrapper.entity 生成的where条件会忽略该字段
- 删除: 转变为 更新

例如:

- 删除: `update user set deleted=1 where id = 1 and deleted=0`
- 查找: `select id,name,deleted from user where deleted=0`

字段类型支持说明:

- 支持所有数据类型(推荐使用 `Integer`,`Boolean`,`LocalDateTime`)
- 如果数据库字段使用`datetime`,逻辑未删除值和已删除值支持配置为字符串`null`,另一个值支持配置为函数来获取值如`now()`

附录:

- 逻辑删除是为了方便数据恢复和保护数据本身价值等等的一种方案，但实际就是删除。
- 如果你需要频繁查出来看就不应使用逻辑删除，而是以一个状态去表示。

### 逻辑删除步骤：1：application.yml添加配置信息 | 2：实体类字段上加上@TableLogic注解

引用mybatis-plus 依赖：由于各个微服务都引入了gulimall-common，而gulimall-common又引用了mybatis-plus。

```xml
<!-- mybatisPLUS-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.2.0</version>
</dependency>
```

步骤**1：application.yml添加配置信息**

- gulimall-product中的application.yml
  
  
    ```yaml
    spring:
      datasource:
        username: root
        password: root
        url: jdbc:mysql://192.168.56.10:3306/gulimall_pms
        driver-class-name: com.mysql.jdbc.Driver
      cloud:
        nacos:
          discovery:
            server-addr: 127.0.0.1:8848
    
    # MapperScan
    # sql映射文件位置
    mybatis-plus:
      mapper-locations: classpath:/mapper/**/*.xml
      #主键自增
      global-config:
        db-config:
          id-type: auto
          **logic-delete-value: 1   #逻辑删除的字段 1：表示删除
          logic-not-delete-value: 0   #0代表没有删除**
    
    server:
      port: 10000
    
    #在idea控制台中打印debug日志信息配置
    logging:
      level:
        com.atguigu.gulimall: debug
    
    #另外在“src/main/resources/application.yml”文件中，设置日志级别，打印出SQL语句：
    ```
    

步骤**2：实体类字段上加上@TableLogic注解**

- CategoryEntity实体类中的showStatus字段添加：`@TableLogic(value = "1",delval = "0")`
  
    【**logic-delete-value: 1   #逻辑删除的字段 1：表示删除， logic-not-delete-value: 0   #0代表不删除】与** [**0-不显示**，**1显示**] 正好相反，故需要 `@TableLogic(value = "1",delval = "0")` 自定义规则。`value = "1"` 表示显示，`delval = "0"` 表示删除。
    
    ```yaml
    /**
         * 是否显示[**0-不显示**，**1显示**]
         */
        @TableLogic(value = "1",delval = "0")
        private Integer showStatus;
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)
    
    ```yaml
    ==>  Preparing: **UPDATE pms_category SET show_status=0 WHERE cat_id IN ( ? ) AND show_status=1**
    ==> Parameters: 1436(Long)
    <==    Updates: 1
    ```
    
    ![#在idea控制台中打印debug日志信息配置
    logging:
      level:
        com.atguigu.gulimall: debug](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)
    
    在idea控制台中打印debug日志信息配置
    
    ```json
    logging:
      level:
        com.atguigu.gulimall: debug
    ```
    
    
    
    

# 51、商品服务-API-三级分类-删除删除效果细化



总的的一个效果展示图：删除子菜单的时候，给用户【提示】信息，删除成功之后，页面不折叠。

- 删除时弹窗确认
- 删除成功弹窗
- 删除后重新展开父节点：重新ajax请求数据，指定展开的基准是:`default-expanded-keys=“expandedKey”`，返回数据后刷新`this.expandedKey = [node.parent.data.catId];`

![录制_2021_08_21_13_49_40_103.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/%E5%BD%95%E5%88%B6_2021_08_21_13_49_40_103.gif)

- `category.vue`本集总代码：src\views\modules\product\category.vue（位置）
  
    ```html
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <el-tree
        :data="menus"
        :props="defaultProps"
        :expand-on-click-node="false"
        show-checkbox
        node-key="catId"
        :default-expanded-keys="expandedKey"
      >
        <span class="custom-tree-node" slot-scope="{ node, data }">
          <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
          <span>{{ node.label }}</span>
          <span>
            <el-button
              v-if="node.level <= 2"
              type="text"
              size="mini"
              @click="() => append(data)"
            >
              Append
            </el-button>
            <el-button
              v-if="node.childNodes.length == 0"
              type="text"
              size="mini"
              @click="() => remove(node, data)"
            >
              Delete
            </el-button>
          </span>
        </span>
      </el-tree>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        append(data) {
          console.log("append", data);
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    
- 参考代码1：`httpRequest.js` 位置：src\utils\httpRequest.js
  
    ```bash
    import Vue from 'vue'
    import axios from 'axios'
    import router from '@/router'
    import qs from 'qs'
    import merge from 'lodash/merge'
    import { clearLoginInfo } from '@/utils'
    
    const http = axios.create({
      timeout: 1000 * 30,
      withCredentials: true,
      headers: {
        'Content-Type': 'application/json; charset=utf-8'
      }
    })
    
    /**
     * 请求拦截
     */
    http.interceptors.request.use(config => {
      config.headers['token'] = Vue.cookie.get('token') // 请求头带上token
      return config
    }, error => {
      return Promise.reject(error)
    })
    
    /**
     * 响应拦截
     */
    http.interceptors.response.use(response => {
      if (response.data && response.data.code === 401) { // 401, token失效
        clearLoginInfo()
        router.push({ name: 'login' })
      }
      return response
    }, error => {
      return Promise.reject(error)
    })
    
    /**
     * 请求地址处理
     * @param {*} actionName action方法名称
     */
    http.adornUrl = (actionName) => {
      // 非生产环境 && 开启代理, 接口前缀统一使用[/proxyApi/]前缀做代理拦截!
      return (process.env.NODE_ENV !== 'production' && process.env.OPEN_PROXY ? '/proxyApi/' : window.SITE_CONFIG.baseUrl) + actionName
    }
    
    /**
     * get请求参数处理
     * @param {*} params 参数对象
     * @param {*} openDefultParams 是否开启默认参数?
     */
    http.adornParams = (params = {}, openDefultParams = true) => {
      var defaults = {
        't': new Date().getTime()
      }
      return openDefultParams ? merge(defaults, params) : params
    }
    
    /**
     * post请求数据处理
     * @param {*} data 数据对象
     * @param {*} openDefultdata 是否开启默认数据?
     * @param {*} contentType 数据格式
     *  json: 'application/json; charset=utf-8'
     *  form: 'application/x-www-form-urlencoded; charset=utf-8'
     */
    http.adornData = (data = {}, openDefultdata = true, contentType = 'json') => {
      var defaults = {
        't': new Date().getTime()
      }
      data = openDefultdata ? merge(defaults, data) : data
      return contentType === 'json' ? JSON.stringify(data) : qs.stringify(data)
    }
    
    export default http
    ```
    
- 参考代码2：`role.vue` 位置：src\views\modules\sys\role.vue
- 抽取代码片段`vue.code-snippets` **文件=》首选项=》用户片段**
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2027.png)
    
    ```bash
    {
        "http-get请求":{
            "prefix":"httpget",
            "body":[
                "this.\\$http({",
                "url:this,\\$http.adornUrl(''),",
                "method:'get',",
                "params:this.\\$http.adornParams({})",
                "}).then({data})=>{",
                "})"
            ],
            "description":"httpGET请求"
        },
    
        "http-post请求":{
            "prefix":"httppost",
            "body":[
                "this.\\$http({",
                "url:this,\\$http.adornUrl(''),",
                "method:'post',",
                "data: this.\\$http.adornData(data, false)",
                "}).then({data})=>{ })"
            ],
            "description":"httpPOST请求"
        }
    }
    ```
    

## ElementUI涉及到的组件：MessageBox弹框 | Message消息提示 | Tree树形控件：`default-expanded-keys` 参数

MessageBox弹框：确认消息

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/message-box)

MessageBox弹框：确认消息

- MessageBox弹框：确认消息：
  
    ```bash
    <template>
      <el-button type="text" @click="open">点击打开 Message Box</el-button>
    </template>
    
    <script>
      export default {
        methods: {
          open() {
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
            }).catch(() => {
              this.$message({
                type: 'info',
                message: '已取消删除'
              });          
            });
          }
        }
      }
    </script>
    ```
    

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/message)

Message消息提示：成功时

- Message消息提示：成功时
  
    ```bash
    <template>
      <el-button :plain="true" @click="open2">成功</el-button>
      <el-button :plain="true" @click="open3">警告</el-button>
      <el-button :plain="true" @click="open1">消息</el-button>
      <el-button :plain="true" @click="open4">错误</el-button>
    </template>
    
    <script>
      export default {
        methods: {
          open1() {
            this.$message('这是一条消息提示');
          },
          open2() {
            this.$message({
              message: '恭喜你，这是一条成功消息',
              type: 'success'
            });
          },
    
          open3() {
            this.$message({
              message: '警告哦，这是一条警告消息',
              type: 'warning'
            });
          },
    
          open4() {
            this.$message.error('错了哦，这是一条错误消息');
          }
        }
      }
    </script>
    ```
    

> Tree树形控件：`default-expanded-keys` 参数：默认展开的节点的 key 的数组
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2028.png)

# 52、商品服务-API-三级分类新增新增效果完成 | Element Dialog 对话框组件

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/dialog)

Element Dialog 对话框组件网址

## Element Dialog 对话框组件显示效果：

Dialog 对话框：在保留当前页面状态的情况下，告知用户并承载相关操作。

![Element Dialog 对话框组件显示效果](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2029.png)

Element Dialog 对话框组件显示效果

- Dialog 对话框参考代码：
  
    需要设置`visible`属性，它接收`Boolean`，当为`true`时显示 `Dialog`。`Dialog` 分为两个部分：`body`和`footer`，`footer`需要具名为`footer`的`slot`。`title`属性用于定义标题，它是可选的，默认值为空。最后，本例还展示了`before-close`的用法。
    
    ```bash
    <el-button type="text" @click="dialogVisible = true">点击打开 Dialog</el-button>
    
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <span>这是一段信息</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
    
    <script>
      export default {
        data() {
          return {
            dialogVisible: false
          };
        },
        methods: {
          handleClose(done) {
            this.$confirm('确认关闭？')
              .then(_ => {
                done();
              })
              .catch(_ => {});
          }
        }
      };
    </script>
    ```
    
- 打开嵌套表单的 Dialog参考代码
  
    自定义内容：Dialog 组件的内容可以是任意的，甚至可以是表格或表单，下面是应用了 Element Table 和 Form 组件的两个样例。
    
    ```bash
    <!-- Form -->
    <el-button type="text" @click="dialogFormVisible = true">打开嵌套表单的 Dialog</el-button>
    
    <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="活动名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="活动区域" :label-width="formLabelWidth">
          <el-select v-model="form.region" placeholder="请选择活动区域">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
      </div>
    </el-dialog>
    ```
    

同时参照`Form表单`：[https://element.eleme.cn/#/zh-CN/component/form](https://element.eleme.cn/#/zh-CN/component/form)

[Form Attributes](https://www.notion.so/8c2103b415b846ecbca88facbb4130ed)

## 这节内容需要实现的效果：

![录制_2021_08_21_23_31_32_31.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/%E5%BD%95%E5%88%B6_2021_08_21_23_31_32_31.gif)

一个button的单击事件函数为`@click=“dialogVisible = true”`
一个会话的属性为`:visible.sync=“dialogVisible”`
导出的data中`"dialogVisible = false"`
点击确认或者取消后的逻辑都是`@click=“dialogVisible = false”` 关闭会话而已

- 总：实现代码
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="addCategory">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          category: { name: "", parentCid: 0, catLevel: 0, showStatus: 1, sort: 0 },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

# 53、商品服务-API-三 级分类修改-基本修改效果完成

- 点击修改弹出对话框，显示现有内容
- 输入新内容后确定，回显新内容
- 对话框是复用的添加的对话框，点击确定的时候回调的是同一个函数，为了区分当前对话框是单击修改还是点击添加打开的，所以添加一个`dialogType`、`title`属性。然后回调函数进行`if`判断
- 回显时候要发送请求获取最新数据

## 这节课代码：

- `category.vue`：位置：src\views\modules\product\category.vue
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button type="text" size="mini" @click="edit(data)">
                Edit
              </el-button>
    
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog
          :title="title"
          :visible.sync="dialogVisible"
          width="30%"
          :close-on-click-modal="false"
        >
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标">
              <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="计量单位">
              <el-input
                v-model="category.productUnit"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitData">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          title: "",
          dialogType: "", //哪种类型的弹窗标识符：edit和add即【修改】和【添加】时弹窗
          category: {
            name: "",
            parentCid: 0,
            catLevel: 0,
            showStatus: 1,
            sort: 0,
            productUnit: "",
            icon: "",
            catId: null,
          },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        //点击修改的方法
        edit(data) {
          console.log("要修改的数据", data);
          this.dialogType = "edit"; //添加分级目录时标识符置为【edit】
          this.title = "修改分类";
          this.dialogVisible = true;
          //数据回显时需要注意：发送请求获取当前节点最新的数据，然后再给他修改。
          this.$http({
            url: this.$http.adornUrl(`/product/category/info/${data.catId}`),
            method: "get",
          }).then(({ data }) => {
            //请求成功
            console.log("要回显的数据", data);
            this.category.name = data.data.name;
            this.category.catId = data.data.catId;
            this.category.icon = data.data.icon;
            this.category.productUnit = data.data.productUnit;
            this.category.parentCid = data.data.parentCid;
            this.category.catLevel = data.data.catLevel;
            this.category.sort = data.data.sort;
            this.category.showStatus = data.dad.showStatus;
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogType = "add"; //添加分级目录时标识符置为【add】
          this.title = "添加分类";
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
          this.category.catId = null;
          this.category.name = "";
          this.category.icon = "";
          this.category.productUnit = "";
          this.category.sort = 0;
          this.category.showStatus = 1;
        },
        submitData() {
          if (this.dialogType == "add") {
            this.addCategory();
          }
          if (this.dialogType == "edit") {
            this.editCategory();
          }
        },
        //修改三级分类数据的方法
        editCategory() {
          var { catId, name, icon, productUnit } = this.category; //解构
    
          // var data = {catId: catId,name: name,icon: icon,productUnit: productUnit}; k:v JavaBean中的属性名：vue对象定义的名字
          // var data = {catId, name, icon, productUnit}; //k和v都是同一个名字可以简写成这样：
          this.$http({
            url: this.$http.adornUrl("/product/category/update"),
            method: "post",
            data: this.$http.adornData({ catId, name, icon, productUnit }, false), //发送出去的数据
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            //修改分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

## 实现效果：

![录制_2021_08_22_15_07_37_839.gif](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/%E5%BD%95%E5%88%B6_2021_08_22_15_07_37_839.gif)

# 54、商品服务-API-三级分类 -修改-拖拽效果 | 菜单拖动

这一节的重要作用是了解一下tree组件。

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/tree)

## 拖拽相关函数：

<el-tree
`:data="menus"`  绑定的变量
`:props="defaultProps"` 配置选项
`:expand-on-click-node="false"`  只有点击箭头才会展开收缩
`show-checkbox` 显示多选框
`node-key="catId"` 数据库的id作为node id
`:default-expanded-keys="expandedKey"` 默认展开的数组
`:draggable="draggable"` 开启拖拽功能
`:allow-drop="allowDrop"`  是否允许拖拽到目标结点，函数为`Function(draggingNode源结点, dropNode目标结点, type前中后类型)`
`@node-drop="handleDrop"`  拖拽成功处理函数，函数为`Function(draggingNode源结点, dropNode拖拽成功后的父结点, type前中后类型)`
`ref="menuTree"`>

## 函数参数：

- `draggingNode`：正在拖拽的结点
- `dropNode`：拓展成功后的父节点，我们把他称为**目的父节点**
- `type`：分为before、after、inner。拖拽到某个结点上还是两个结点之间
- allow-drop拖拽时判定目标节点能否被放置
- 被拖动的当前节点以及所在的父节点总层数不能大于3

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2030.png)

关注的焦点在于，拖动到目标节点中，使得目标节点的catlevel+deep小于3即可。

## 参考ElementUI：可拖拽节点 : draggable参数

- 可拖拽节点参考代码
  
    ```bash
    <el-tree
      :data="data"
      node-key="id"
      default-expand-all
      @node-drag-start="handleDragStart"
      @node-drag-enter="handleDragEnter"
      @node-drag-leave="handleDragLeave"
      @node-drag-over="handleDragOver"
      @node-drag-end="handleDragEnd"
      @node-drop="handleDrop"
      draggable
      :allow-drop="allowDrop"
      :allow-drag="allowDrag">
    </el-tree>
    -----------------------------------------------------------------------
    
    allowDrop(draggingNode, dropNode, type) {
            if (dropNode.data.label === '二级 3-1') {
              return type !== 'inner';
            } else {
              return true;
            }
          },
    ```
    

[Attributes](https://www.notion.so/c8ae3b6e6a1b4215a3f8d6bd9ceb6681)

## 这节课代码

- `category.vue`：位置：src\views\modules\product\category.vue
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
          draggable
          :allow-drop="allowDrop"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button type="text" size="mini" @click="edit(data)">
                Edit
              </el-button>
    
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog
          :title="title"
          :visible.sync="dialogVisible"
          width="30%"
          :close-on-click-modal="false"
        >
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标">
              <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="计量单位">
              <el-input
                v-model="category.productUnit"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitData">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          maxLevel: 0,
          title: "",
          dialogType: "", //哪种类型的弹窗标识符：edit和add即【修改】和【添加】时弹窗
          category: {
            name: "",
            parentCid: 0,
            catLevel: 0,
            showStatus: 1,
            sort: 0,
            productUnit: "",
            icon: "",
            catId: null,
          },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        allowDrop(draggingNode, dropNode, type) {
          //1、被拖动的当前节点以及所在的父节点总层数不能大于3
          //1)、被拖动的当前节点总层数
          console.log("allowDrop:", draggingNode, dropNode, type);
          this.countNodeLevel(draggingNode.data);
          //当前正在拖动的节点+父节点所在的深度不大于3即可
          let deep = this.maxLevel - draggingNode.data.catLevel + 1;
          console.log("深度：", deep);
    
          // this.maxLevel
          if (type == "inner") {
            return deep + dropNode.level <= 3;
          } else {
            return deep + dropNode.parent.level <= 3;
          }
        },
        countNodeLevel(node) {
          //1、找到所有子节点，求出最大深度
          if (node.children != null && node.children.length > 0) {
            for (let i = 0; i < node.children.length; i++) {
              if (node.children[i].catLevel > this.maxLevel) {
                this.maxLevel = node.children[i].catLevel;
              }
              this.countNodeLevel(node.children[i]);
            }
          }
        },
        //点击修改的方法
        edit(data) {
          console.log("要修改的数据", data);
          this.dialogType = "edit"; //添加分级目录时标识符置为【edit】
          this.title = "修改分类";
          this.dialogVisible = true;
          //数据回显时需要注意：发送请求获取当前节点最新的数据，然后再给他修改。
          this.$http({
            url: this.$http.adornUrl(`/product/category/info/${data.catId}`),
            method: "get",
          }).then(({ data }) => {
            //请求成功
            console.log("要回显的数据", data);
            this.category.name = data.data.name;
            this.category.catId = data.data.catId;
            this.category.icon = data.data.icon;
            this.category.productUnit = data.data.productUnit;
            this.category.parentCid = data.data.parentCid;
            this.category.catLevel = data.data.catLevel;
            this.category.sort = data.data.sort;
            this.category.showStatus = data.dad.showStatus;
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogType = "add"; //添加分级目录时标识符置为【add】
          this.title = "添加分类";
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
          this.category.catId = null;
          this.category.name = "";
          this.category.icon = "";
          this.category.productUnit = "";
          this.category.sort = 0;
          this.category.showStatus = 1;
        },
        submitData() {
          if (this.dialogType == "add") {
            this.addCategory();
          }
          if (this.dialogType == "edit") {
            this.editCategory();
          }
        },
        //修改三级分类数据的方法
        editCategory() {
          var { catId, name, icon, productUnit } = this.category; //解构
    
          // var data = {catId: catId,name: name,icon: icon,productUnit: productUnit}; k:v JavaBean中的属性名：vue对象定义的名字
          // var data = {catId, name, icon, productUnit}; //k和v都是同一个名字可以简写成这样：
          this.$http({
            url: this.$http.adornUrl("/product/category/update"),
            method: "post",
            data: this.$http.adornData({ catId, name, icon, productUnit }, false), //发送出去的数据
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            //修改分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

# 55、商品服务-API-三级分类修改-拖拽数据收集 | 参考ElementUI：可拖拽节点

## 这节课前端代码汇总

- `category.vue`：位置：src\views\modules\product\category.vue
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
          draggable
          :allow-drop="allowDrop"
          @node-drop="handleDrop"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button type="text" size="mini" @click="edit(data)">
                Edit
              </el-button>
    
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog
          :title="title"
          :visible.sync="dialogVisible"
          width="30%"
          :close-on-click-modal="false"
        >
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标">
              <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="计量单位">
              <el-input
                v-model="category.productUnit"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitData">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          updateNodes: [],
          maxLevel: 0,
          title: "",
          dialogType: "", //哪种类型的弹窗标识符：edit和add即【修改】和【添加】时弹窗
          category: {
            name: "",
            parentCid: 0,
            catLevel: 0,
            showStatus: 1,
            sort: 0,
            productUnit: "",
            icon: "",
            catId: null,
          },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        handleDrop(draggingNode, dropNode, dropType, ev) {
          console.log("handleDrop: ", draggingNode, dropNode, dropType);
          //1、当前节点最新的父节点id
          let pCid = 0;
          let siblings = null;
          if (dropType == "before" || dropType == "after") {
            pCid =
              dropNode.parent.data.catId == undefined
                ? 0
                : dropNode.parent.data.catId;
            siblings = dropNode.parent.childNodes;
          } else {
            pCid = dropNode.data.catId;
            siblings = dropNode.childNodes;
          }
          //2、当前拖拽节点的最新顺序
          for (let i = 0; i < siblings.length; i++) {
            if (siblings[i].data.catId == draggingNode.data.catId) {
              //如果变量的时当前拖拽的节点
              let catLevel = draggingNode.level;
              if (siblings[i].level != draggingNode.level) {
                //当前节点的层级发生了变化
                catLevel = siblings[i].level;
                //修改子节点的层级
                this.updateChildNodeLevel(siblings[i]);
              }
              this.updateNodes.push({
                catId: siblings[i].data.catId,
                sort: i,
                parentCid: pCid,
                catLevel: catLevel,
              });
            } else {
              this.updateNodes.push({ catId: siblings[i].data.catId, sort: i });
            }
          }
          //3、当前拖拽节点的最新层级
          console.log("updateNodes", this.updateNodes);
        },
        updateChildNodeLevel(node) {
          if (node.childNodes.length > 0) {
            for (let i = 0; i < node.childNodes.length; i++) {
              var cNode = node.childNodes[i].data;
              this.updateNodes.push({catId: cNode.catId, catLevel: node.childNodes[i].level});
              this.updateChildNodeLevel(node.childNodes[i]);
            }
          }
        },
        allowDrop(draggingNode, dropNode, type) {
          //1、被拖动的当前节点以及所在的父节点总层数不能大于3
          //1)、被拖动的当前节点总层数
          console.log("allowDrop:", draggingNode, dropNode, type);
          this.countNodeLevel(draggingNode.data);
          //当前正在拖动的节点+父节点所在的深度不大于3即可
          let deep = this.maxLevel - draggingNode.data.catLevel + 1;
          console.log("深度：", deep);
    
          // this.maxLevel
          if (type == "inner") {
            return deep + dropNode.level <= 3;
          } else {
            return deep + dropNode.parent.level <= 3;
          }
        },
        countNodeLevel(node) {
          //1、找到所有子节点，求出最大深度
          if (node.children != null && node.children.length > 0) {
            for (let i = 0; i < node.children.length; i++) {
              if (node.children[i].catLevel > this.maxLevel) {
                this.maxLevel = node.children[i].catLevel;
              }
              this.countNodeLevel(node.children[i]);
            }
          }
        },
        //点击修改的方法
        edit(data) {
          console.log("要修改的数据", data);
          this.dialogType = "edit"; //添加分级目录时标识符置为【edit】
          this.title = "修改分类";
          this.dialogVisible = true;
          //数据回显时需要注意：发送请求获取当前节点最新的数据，然后再给他修改。
          this.$http({
            url: this.$http.adornUrl(`/product/category/info/${data.catId}`),
            method: "get",
          }).then(({ data }) => {
            //请求成功
            console.log("要回显的数据", data);
            this.category.name = data.data.name;
            this.category.catId = data.data.catId;
            this.category.icon = data.data.icon;
            this.category.productUnit = data.data.productUnit;
            this.category.parentCid = data.data.parentCid;
            this.category.catLevel = data.data.catLevel;
            this.category.sort = data.data.sort;
            this.category.showStatus = data.dad.showStatus;
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogType = "add"; //添加分级目录时标识符置为【add】
          this.title = "添加分类";
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
          this.category.catId = null;
          this.category.name = "";
          this.category.icon = "";
          this.category.productUnit = "";
          this.category.sort = 0;
          this.category.showStatus = 1;
        },
        submitData() {
          if (this.dialogType == "add") {
            this.addCategory();
          }
          if (this.dialogType == "edit") {
            this.editCategory();
          }
        },
        //修改三级分类数据的方法
        editCategory() {
          var { catId, name, icon, productUnit } = this.category; //解构
    
          // var data = {catId: catId,name: name,icon: icon,productUnit: productUnit}; k:v JavaBean中的属性名：vue对象定义的名字
          // var data = {catId, name, icon, productUnit}; //k和v都是同一个名字可以简写成这样：
          this.$http({
            url: this.$http.adornUrl("/product/category/update"),
            method: "post",
            data: this.$http.adornData({ catId, name, icon, productUnit }, false), //发送出去的数据
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            //修改分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

# 56、商品服务-API-三级分类修改拖拽功能完成

- `CategoryController：`com.atguigu.gulimall.product.controller.CategoryController ,新增一个方法
  
    ```java
    /**
         * 批量修改层级
         * @param category 
         * @return
         */
    @RequestMapping("/update/sort")
        //@RequiresPermissions("product:category:update")
        public R updateSort(@RequestBody CategoryEntity[] category) {
    //        categoryService.updateById(category);
            categoryService.updateBatchById(Arrays.asList(category));   //将category数组通过Arrays.asList()转成集合
    
            return R.ok();
        }
    ```
    

拖动【手机通讯】=》【手机】上面

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2031.png)

使用PostMan测试：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2032.png)

打开数据库查看数据：发现已经更改了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2033.png)

前端部分代码：

```java
this.$http({
        url: this.$http.adornUrl("/product/category/update/sort"),
        method: "post",
        data: this.$http.adornData(this.updateNodes, false),
      }).then(({ data }) => {
        //发送成功之后 成功的提示消息
        this.$message({
          message: "菜单顺序等修改成功",
          type: "success",
        });
        //修改成功刷出新的菜单
        this.getMenus();
        //设置需要默认展开的菜单
        this.expandedKey = [pCid];
        this.updateNodes = [];
        this.maxLevel = 0;
      });

// this.maxLevel
      if (type == "inner") {
        console.log(
          `this.maxLevel:${this.maxLevel};draggingNode.data.catLevel:${draggingNode.data.catLevel};dropNode.level:${dropNode.level}`
        );
        return deep + dropNode.level <= 3;
      } else {
        return deep + dropNode.parent.level <= 3;
      }
```

# 57、商品服务-API-三级分类 -修改-批量拖拽效果 | 按钮 | 参照elementUI的Switch 开关组件 | 存在问题

需求：整个界面需要拖动功能的时候才可以拖动，防止一切误操作带来的问题。给页面添加一个**拖拽功能的按钮，需要拖拽功能的时候就开启这项功能。**

## 遗留问题：这样的话二级目录就不能拖拽到根目录 虽然显示保存成功

countNodeLevel方法还要讨论没有子节点的操作，不然可能二级节点拖不动

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2034.png)

[Element - The world's most popular Vue UI framework](https://element.eleme.cn/#/zh-CN/component/switch)

- 这节课代码：`category.vue：`位置：src\views\modules\product\category.vue
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-switch
          v-model="draggable"
          active-text="开启拖拽"
          inactive-text="关闭拖拽"
        ></el-switch>
        <el-button v-if="draggable" @click="batchSave">批量保存</el-button>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
          :draggable="draggable"
          :allow-drop="allowDrop"
          @node-drop="handleDrop"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button type="text" size="mini" @click="edit(data)">
                Edit
              </el-button>
    
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog
          :title="title"
          :visible.sync="dialogVisible"
          width="30%"
          :close-on-click-modal="false"
        >
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标">
              <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="计量单位">
              <el-input
                v-model="category.productUnit"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitData">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          pCid: [],
          draggable: false, //默认不可以拖拽
          updateNodes: [],
          maxLevel: 0,
          title: "",
          dialogType: "", //哪种类型的弹窗标识符：edit和add即【修改】和【添加】时弹窗
          category: {
            name: "",
            parentCid: 0,
            catLevel: 0,
            showStatus: 1,
            sort: 0,
            productUnit: "",
            icon: "",
            catId: null,
          },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        batchSave() {
          this.$http({
            url: this.$http.adornUrl("/product/category/update/sort"),
            method: "post",
            data: this.$http.adornData(this.updateNodes, false),
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单顺序等修改成功",
              type: "success",
            });
            //修改成功刷出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = this.pCid;
            this.updateNodes = [];
            this.maxLevel = 0;
            // this.pCid = 0;
          });
        },
        handleDrop(draggingNode, dropNode, dropType, ev) {
          console.log("handleDrop: ", draggingNode, dropNode, dropType);
          //1、当前节点最新的父节点id
          let pCid = 0;
          let siblings = null;
          if (dropType == "before" || dropType == "after") {
            pCid =
              dropNode.parent.data.catId == undefined
                ? 0
                : dropNode.parent.data.catId;
            siblings = dropNode.parent.childNodes;
          } else {
            pCid = dropNode.data.catId;
            siblings = dropNode.childNodes;
          }
          this.pCid.push(pCid);
          //2、当前拖拽节点的最新顺序
          for (let i = 0; i < siblings.length; i++) {
            if (siblings[i].data.catId == draggingNode.data.catId) {
              //如果变量的时当前拖拽的节点
              let catLevel = draggingNode.level;
              if (siblings[i].level != draggingNode.level) {
                //当前节点的层级发生了变化
                catLevel = siblings[i].level;
                //修改子节点的层级
                this.updateChildNodeLevel(siblings[i]);
              }
              this.updateNodes.push({
                catId: siblings[i].data.catId,
                sort: i,
                parentCid: pCid,
                catLevel: catLevel,
              });
            } else {
              this.updateNodes.push({ catId: siblings[i].data.catId, sort: i });
            }
          }
          //3、当前拖拽节点的最新层级
          console.log("updateNodes", this.updateNodes);
        },
        updateChildNodeLevel(node) {
          if (node.childNodes.length > 0) {
            for (let i = 0; i < node.childNodes.length; i++) {
              var cNode = node.childNodes[i].data;
              this.updateNodes.push({
                catId: cNode.catId,
                catLevel: node.childNodes[i].level,
              });
              this.updateChildNodeLevel(node.childNodes[i]);
            }
          }
        },
        allowDrop(draggingNode, dropNode, type) {
          //1、被拖动的当前节点以及所在的父节点总层数不能大于3
          //1)、被拖动的当前节点总层数
          console.log("allowDrop:", draggingNode, dropNode, type);
          this.countNodeLevel(draggingNode);
          //当前正在拖动的节点+父节点所在的深度不大于3即可
          let deep = Math.abs(this.maxLevel - draggingNode.level + 1);
          console.log("深度：", deep);
    
          // this.maxLevel
          if (type == "inner") {
            // console.log(
            //   `this.maxLevel:${this.maxLevel};draggingNode.data.catLevel:${draggingNode.data.catLevel};dropNode.level:${dropNode.level}`
            // );
            return deep + dropNode.level <= 3;
          } else {
            return deep + dropNode.parent.level <= 3;
          }
        },
        countNodeLevel(node) {
          //1、找到所有子节点，求出最大深度
          if (node.childNodes != null && node.childNodes.length > 0) {
            for (let i = 0; i < node.childNodes.length; i++) {
              if (node.childNodes[i].level > this.maxLevel) {
                this.maxLevel = node.childNodes[i].level;
              }
              this.countNodeLevel(node.childNodes[i]);
            }
          }
        },
        //点击修改的方法
        edit(data) {
          console.log("要修改的数据", data);
          this.dialogType = "edit"; //添加分级目录时标识符置为【edit】
          this.title = "修改分类";
          this.dialogVisible = true;
          //数据回显时需要注意：发送请求获取当前节点最新的数据，然后再给他修改。
          this.$http({
            url: this.$http.adornUrl(`/product/category/info/${data.catId}`),
            method: "get",
          }).then(({ data }) => {
            //请求成功
            console.log("要回显的数据", data);
            this.category.name = data.data.name;
            this.category.catId = data.data.catId;
            this.category.icon = data.data.icon;
            this.category.productUnit = data.data.productUnit;
            this.category.parentCid = data.data.parentCid;
            this.category.catLevel = data.data.catLevel;
            this.category.sort = data.data.sort;
            this.category.showStatus = data.dad.showStatus;
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogType = "add"; //添加分级目录时标识符置为【add】
          this.title = "添加分类";
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
          this.category.catId = null;
          this.category.name = "";
          this.category.icon = "";
          this.category.productUnit = "";
          this.category.sort = 0;
          this.category.showStatus = 1;
        },
        submitData() {
          if (this.dialogType == "add") {
            this.addCategory();
          }
          if (this.dialogType == "edit") {
            this.editCategory();
          }
        },
        //修改三级分类数据的方法
        editCategory() {
          var { catId, name, icon, productUnit } = this.category; //解构
    
          // var data = {catId: catId,name: name,icon: icon,productUnit: productUnit}; k:v JavaBean中的属性名：vue对象定义的名字
          // var data = {catId, name, icon, productUnit}; //k和v都是同一个名字可以简写成这样：
          this.$http({
            url: this.$http.adornUrl("/product/category/update"),
            method: "post",
            data: this.$http.adornData({ catId, name, icon, productUnit }, false), //发送出去的数据
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            //修改分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```
    

# 58、商品服务API-三级分类删除批量删除&小结 | 参考ElementUI：Button 按钮

`[getCheckedNodes：](https://element.eleme.cn/#/zh-CN/component/tree)`若节点可被选择（即 show-checkbox 为 true），则返回目前被选中的节点所组成的数组。

(leafOnly, includeHalfChecked) 接收两个 boolean 类型的参数，1. 是否只是叶子节点，默认值为 false 2. 是否包含半选节点，默认值为 false

- category.vue：位置：src\views\modules\product\category.vue
  
    ```bash
    <!--  -->
    <template>
      <!-- <div class=''>三级分类维护</div> -->
      <div>
        <el-switch
          v-model="draggable"
          active-text="开启拖拽"
          inactive-text="关闭拖拽"
        ></el-switch>
        <el-button v-if="draggable" @click="batchSave">批量保存</el-button>
        <el-button type="danger" @click="batchDelete">批量删除</el-button>
        <el-tree
          :data="menus"
          :props="defaultProps"
          :expand-on-click-node="false"
          show-checkbox
          node-key="catId"
          :default-expanded-keys="expandedKey"
          :draggable="draggable"
          :allow-drop="allowDrop"
          @node-drop="handleDrop"
          ref="menuTree"
        >
          <span class="custom-tree-node" slot-scope="{ node, data }">
            <!--scoped slot（插槽）：在el-tree标签里把内容写到span标签栏里即可 -->
            <span>{{ node.label }}</span>
            <span>
              <el-button
                v-if="node.level <= 2"
                type="text"
                size="mini"
                @click="() => append(data)"
              >
                Append
              </el-button>
              <el-button type="text" size="mini" @click="edit(data)">
                Edit
              </el-button>
    
              <el-button
                v-if="node.childNodes.length == 0"
                type="text"
                size="mini"
                @click="() => remove(node, data)"
              >
                Delete
              </el-button>
            </span>
          </span>
        </el-tree>
    
        <el-dialog
          :title="title"
          :visible.sync="dialogVisible"
          width="30%"
          :close-on-click-modal="false"
        >
          <el-form :model="category">
            <el-form-item label="分类名称">
              <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标">
              <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="计量单位">
              <el-input
                v-model="category.productUnit"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitData">确 定</el-button>
          </span>
        </el-dialog>
      </div>
    </template>
    
    <script>
    //这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
    //例如：import 《组件名称》 from '《组件路径》';
    
    export default {
      //import引入的组件需要注入到对象中才能使用
      components: {},
      //   data() {
      //     //这里存放数据
      //     return {};
      //   },
      data() {
        return {
          pCid: [],
          draggable: false, //默认不可以拖拽
          updateNodes: [],
          maxLevel: 0,
          title: "",
          dialogType: "", //哪种类型的弹窗标识符：edit和add即【修改】和【添加】时弹窗
          category: {
            name: "",
            parentCid: 0,
            catLevel: 0,
            showStatus: 1,
            sort: 0,
            productUnit: "",
            icon: "",
            catId: null,
          },
          dialogVisible: false,
          menus: [],
          expandedKey: [],
          defaultProps: {
            children: "children",
            label: "name", //这里其实对应的是数据库表pms_category里面的name字段
          },
        };
      },
      //监听属性 类似于data概念
      computed: {},
      //监控data中的数据变化
      watch: {},
      //方法集合
      //   methods: {},
      methods: {
        getMenus() {
          //   this.dataListLoading = true;
          this.$http({
            url: this.$http.adornUrl("/product/category/list/tree"), //给后台发送请求：体会一下我们要重写product项目里这个controller
            method: "get",
          }).then(({ data }) => {
            // success 响应到数据后填充到绑定的标签中
            console.log("成功获取到菜单数据。。。", data.data);
            this.menus = data.data; // 数组内容，把数据给menus，就是给了vue实例，最后绑定到视图上
          });
        },
        batchDelete() {
          let catIds = [];
          let checkedNodes = this.$refs.menuTree.getCheckedNodes();
          console.log("被选中的元素", checkedNodes);
          for (let i = 0; i < checkedNodes.length; i++) {
            catIds.push(checkedNodes[i].catId);
          }
          this.$confirm(`是否批量删除【${catIds}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(catIds, false),
              }).then(({ data }) => {
                //成功的提示消息
                this.$message({
                  message: "菜单批量删除成功",
                  type: "success",
                });
                this.getMenus();
              });
            })
            .catch(() => {});
        },
        batchSave() {
          this.$http({
            url: this.$http.adornUrl("/product/category/update/sort"),
            method: "post",
            data: this.$http.adornData(this.updateNodes, false),
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单顺序等修改成功",
              type: "success",
            });
            //修改成功刷出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = this.pCid;
            this.updateNodes = [];
            this.maxLevel = 0;
            // this.pCid = 0;
          });
        },
        handleDrop(draggingNode, dropNode, dropType, ev) {
          console.log("handleDrop: ", draggingNode, dropNode, dropType);
          //1、当前节点最新的父节点id
          let pCid = 0;
          let siblings = null;
          if (dropType == "before" || dropType == "after") {
            pCid =
              dropNode.parent.data.catId == undefined
                ? 0
                : dropNode.parent.data.catId;
            siblings = dropNode.parent.childNodes;
          } else {
            pCid = dropNode.data.catId;
            siblings = dropNode.childNodes;
          }
          this.pCid.push(pCid);
          //2、当前拖拽节点的最新顺序
          for (let i = 0; i < siblings.length; i++) {
            if (siblings[i].data.catId == draggingNode.data.catId) {
              //如果变量的时当前拖拽的节点
              let catLevel = draggingNode.level;
              if (siblings[i].level != draggingNode.level) {
                //当前节点的层级发生了变化
                catLevel = siblings[i].level;
                //修改子节点的层级
                this.updateChildNodeLevel(siblings[i]);
              }
              this.updateNodes.push({
                catId: siblings[i].data.catId,
                sort: i,
                parentCid: pCid,
                catLevel: catLevel,
              });
            } else {
              this.updateNodes.push({ catId: siblings[i].data.catId, sort: i });
            }
          }
          //3、当前拖拽节点的最新层级
          console.log("updateNodes", this.updateNodes);
        },
        updateChildNodeLevel(node) {
          if (node.childNodes.length > 0) {
            for (let i = 0; i < node.childNodes.length; i++) {
              var cNode = node.childNodes[i].data;
              this.updateNodes.push({
                catId: cNode.catId,
                catLevel: node.childNodes[i].level,
              });
              this.updateChildNodeLevel(node.childNodes[i]);
            }
          }
        },
        allowDrop(draggingNode, dropNode, type) {
          //1、被拖动的当前节点以及所在的父节点总层数不能大于3
          //1)、被拖动的当前节点总层数
          console.log("allowDrop:", draggingNode, dropNode, type);
          this.countNodeLevel(draggingNode);
          //当前正在拖动的节点+父节点所在的深度不大于3即可
          let deep = Math.abs(this.maxLevel - draggingNode.level + 1);
          console.log("深度：", deep);
    
          // this.maxLevel
          if (type == "inner") {
            // console.log(
            //   `this.maxLevel:${this.maxLevel};draggingNode.data.catLevel:${draggingNode.data.catLevel};dropNode.level:${dropNode.level}`
            // );
            return deep + dropNode.level <= 3;
          } else {
            return deep + dropNode.parent.level <= 3;
          }
        },
        countNodeLevel(node) {
          //1、找到所有子节点，求出最大深度
          if (node.childNodes != null && node.childNodes.length > 0) {
            for (let i = 0; i < node.childNodes.length; i++) {
              if (node.childNodes[i].level > this.maxLevel) {
                this.maxLevel = node.childNodes[i].level;
              }
              this.countNodeLevel(node.childNodes[i]);
            }
          }
        },
        //点击修改的方法
        edit(data) {
          console.log("要修改的数据", data);
          this.dialogType = "edit"; //添加分级目录时标识符置为【edit】
          this.title = "修改分类";
          this.dialogVisible = true;
          //数据回显时需要注意：发送请求获取当前节点最新的数据，然后再给他修改。
          this.$http({
            url: this.$http.adornUrl(`/product/category/info/${data.catId}`),
            method: "get",
          }).then(({ data }) => {
            //请求成功
            console.log("要回显的数据", data);
            this.category.name = data.data.name;
            this.category.catId = data.data.catId;
            this.category.icon = data.data.icon;
            this.category.productUnit = data.data.productUnit;
            this.category.parentCid = data.data.parentCid;
            this.category.catLevel = data.data.catLevel;
            this.category.sort = data.data.sort;
            this.category.showStatus = data.dad.showStatus;
          });
        },
        append(data) {
          console.log("append", data);
          this.dialogType = "add"; //添加分级目录时标识符置为【add】
          this.title = "添加分类";
          this.dialogVisible = true;
          this.category.parentCid = data.catId;
          this.category.catLevel = data.catLevel * 1 + 1;
          this.category.catId = null;
          this.category.name = "";
          this.category.icon = "";
          this.category.productUnit = "";
          this.category.sort = 0;
          this.category.showStatus = 1;
        },
        submitData() {
          if (this.dialogType == "add") {
            this.addCategory();
          }
          if (this.dialogType == "edit") {
            this.editCategory();
          }
        },
        //修改三级分类数据的方法
        editCategory() {
          var { catId, name, icon, productUnit } = this.category; //解构
    
          // var data = {catId: catId,name: name,icon: icon,productUnit: productUnit}; k:v JavaBean中的属性名：vue对象定义的名字
          // var data = {catId, name, icon, productUnit}; //k和v都是同一个名字可以简写成这样：
          this.$http({
            url: this.$http.adornUrl("/product/category/update"),
            method: "post",
            data: this.$http.adornData({ catId, name, icon, productUnit }, false), //发送出去的数据
          }).then(({ data }) => {
            //发送成功之后 成功的提示消息
            this.$message({
              message: "菜单修改成功",
              type: "success",
            });
            //修改分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
        //添加三级分类
        addCategory() {
          console.log("提交的三级分类数据", this.category);
          this.$http({
            url: this.$http.adornUrl("/product/category/save"),
            method: "post",
            data: this.$http.adornData(this.category, false),
          }).then(({ data }) => {
            //成功的提示消息
            this.$message({
              message: "菜单保存成功",
              type: "success",
            });
            //新增分类，保存成功之后，关闭对话框
            this.dialogVisible = false;
            //然后刷新出新的菜单
            this.getMenus();
            //设置需要默认展开的菜单
            this.expandedKey = [this.category.parentCid];
          });
        },
    
        remove(node, data) {
          var ids = [data.catId];
          this.$confirm(`是否删除【${data.name}】菜单？`, "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          })
            .then(() => {
              this.$http({
                url: this.$http.adornUrl("/product/category/delete"),
                method: "post",
                data: this.$http.adornData(ids, false),
              }).then(({ data }) => {
                this.$message({
                  message: "菜单删除成功",
                  type: "success",
                });
                this.getMenus(); //刷新出新的菜单
                //设置需要默认展开的菜单
                this.expandedKey = [node.parent.data.catId];
              });
            })
            .catch(() => {});
    
          console.log("remove", node, data);
        },
      },
      //生命周期 - 创建完成（可以访问当前this实例）
      created() {
        this.getMenus();
      },
      //生命周期 - 挂载完成（可以访问DOM元素）
      mounted() {},
      beforeCreate() {}, //生命周期 - 创建之前
      beforeMount() {}, //生命周期 - 挂载之前
      beforeUpdate() {}, //生命周期 - 更新之前
      updated() {}, //生命周期 - 更新之后
      beforeDestroy() {}, //生命周期 - 销毁之前
      destroyed() {}, //生命周期 - 销毁完成
      activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
    };
    </script>
    <style scoped>
    /* @import url(); 引入公共css类 */
    </style>
    ```