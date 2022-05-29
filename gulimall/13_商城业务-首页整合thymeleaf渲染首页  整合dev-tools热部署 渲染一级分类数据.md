# 13_商城业务-首页整合thymeleaf渲染首页 | 整合dev-tools热部署|渲染一级分类数据

# P136、商城业务-首页整合thymeleaf渲染首页



## 微服务项目架构

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

### 动静分离

静：图片，JS、CSS等静态资源(以实际文件存在的方式)。

动：服务器需要处理的请求。

每一个微服务都可以独立部署、运行、升级。

独立自治：技术，架构，业务。

```
5、模板引擎
1)、thymeleaf-starter:关闭缓存（为了能够实时显示数据）
2)、静态资源都放在static文件夹下就可以按照路径直接访问
3)、页面放在templates下，直接访问
SpringBoot,访问项目的时候，默认会找index
```

> 将对应的文件放到对应的文件夹下
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

将所有的资源放在对应的位置之后，我们又将原来的controller报修改成了app

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

- application.yml
  
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
        alicloud:
          access-key: LTAI5tQJe43xkmKionyVWfkH
          secret-key: NYJesMLZJcVdFvhVx5bjEPw20vBe2z
          oss:
            endpoint: oss-cn-beijing.aliyuncs.com
      jackson:
        date-format: yyyy-MM-dd HH:mm:ss
      **thymeleaf:
        cache: false**
    
    # MapperScan
    # sql映射文件位置
    mybatis-plus:
      mapper-locations: classpath:/mapper/**/*.xml
      #主键自增
      global-config:
        db-config:
          id-type: auto
          logic-delete-value: 1   #逻辑删除的字段 1：表示删除
          logic-not-delete-value: 0   #0代表没有删除
    
    server:
      port: 10000
    
    #在idea控制台中打印debug日志信息配置
    logging:
      level:
        com.atguigu.gulimall: debug
    ```
    

## 测试

重启guliamll-product微服务，浏览器访问以下地址

[](http://localhost:10000/)

结果图：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

# P137、商城业务-首页整合dev-tools渲染一级分类数据

无论我们是访问：

[http://localhost:10000/](http://localhost:10000/)
还是
[http://localhost:10000/index.html（暂时不显示，需要我们做映射）](http://localhost:10000/index.html%EF%BC%88%E6%9A%82%E6%97%B6%E4%B8%8D%E6%98%BE%E7%A4%BA%EF%BC%8C%E9%9C%80%E8%A6%81%E6%88%91%E4%BB%AC%E5%81%9A%E6%98%A0%E5%B0%84%EF%BC%89)
都可以访问我们的首页

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

## thymeleaf官网

[Thymeleaf](https://www.thymeleaf.org/documentation.html)

记得添加**thymeleaf**名称空间：

```html
!DOCTYPE html>
<html **xmlns:th="http://www.thymeleaf.org">**
	<head>
		<title>Good Thymes Virtual Grocery</title>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
		<link rel="stylesheet" type="text/css" media="all"
		href="../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
	</head>
<body>
	<p th:text="#{home.welcome}">Welcome to our grocery store!</p>
</body>
</html>
```

## 热部署：dev-tools

如果页面修改之后不想重启服务器，而且还要实时更新页面内容怎么办？
     1、引入dev-tools
     2、修改元贝面controller shift f9重新自动编详下贝面，*`如果是代码配置，还是推荐重启服务`*

pom依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>2.1.5.RELEASE</version>
</dependency>

-- 使用下面的 记得<optional>true</optional>
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

重启服务：多出一个de'vtools标识，前提是先关闭thymeleaf的换成cache:false

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

index.html修改内容，记得添加thymeleaf的名称空间

```html
!DOCTYPE html>
<html **xmlns:th="http://www.thymeleaf.org">

。。。。。。。。**
<!--轮播主体内容-->
    <div class="header_main">
      <div class="header_banner">
        <div class="header_main_left">
          <ul>
            **<li th:each="category : ${categorys}">
              <a href="#" class="header_main_left_a" th:attr="ctg-data=${category.catId}"><b th:text="${category.name}">家用电器1111</b></a>
            </li>**
          </ul>
```

# P138、 商城业务-首页渲染二 级三级分类数据

## 代码（含P137）

- IndexController.java
  
    ```java
    package com.atguigu.gulimall.product.web;
    ........
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    
    /**
     * @Author Dali
     * @Date 2021/12/12 10:44
     * @Version 1.0
     * @Description: 与web访问相关的controller
     */
    @Controller
    public class IndexController {
    
        @Autowired
        CategoryService categoryService;
    
        /**
         * 无论我们是访问：
         * <p>
         * [http://localhost:10000/]
         * 还是
         * [http://localhost:10000/index.html（暂时不显示，需要我们做映射）]
         * 都可以访问我们的首页
         *
         * @return
         */
        @GetMapping({"/", "/index.html"})
        public String indexPage(Model model) {
    
            //TODO 1、查出所有的一级分类
            List<CategoryEntity> categoryEntities = categoryService.getLevel1Categorys();
    
            // 视图解析器进行拼串
            // 源码中含有默认后缀：classpath:/templates/
            // 源码中含有默认后缀：.html    然后进行拼串
    
            model.addAttribute("categorys", categoryEntities);
            return "index";         //所以我们只需返回一个index即可
        }
    
        //index/catalog.json   , Map是一个JSON对象？
        @ResponseBody
        @GetMapping("/index/catalog.json")
        public Map<String, List<Catelog2Vo>> getCatalogJson() {
    
            //查出所有的分类
            Map<String, List<Catelog2Vo>> catalogJson = categoryService.getCatalogJson();
            return catalogJson;
        }
    }
    ```
    
- CategoryService.java
  
    ```java
    List<CategoryEntity> getLevel1Categorys();
    
    Map<String, List<Catelog2Vo>> getCatalogJson();
    ```
    
- CategoryServiceImpl.java
  
    ```java
    /**
     * 查询所有的一级分类：
     * "pms_category"表中parent_cid=0（没有上一级分类）或者cat_level=1表示为1级分类
     *
     * @return
     */
    @Override
    public List<CategoryEntity> getLevel1Categorys() {
        //selectList查询集合
        List<CategoryEntity> categoryEntities = baseMapper.selectList(new QueryWrapper<CategoryEntity>().eq("parent_cid", 0));
    
        return categoryEntities;
    }
    
    /**
     * 查出所有的分类
     *
     * @return
     */
    @Override
    public Map<String, List<Catelog2Vo>> getCatalogJson() {
    
        //1、查出所有1级分类
        List<CategoryEntity> level1Categorys = getLevel1Categorys();
    
        //2、封装数据【重要】
        Map<String, List<Catelog2Vo>> parent_cid = level1Categorys.stream().collect(Collectors.toMap(k -> k.getCatId().toString(), v -> {
            //1、每一个的一级分类，查到这个一级分类的二级分类
            List<CategoryEntity> categoryEntities = baseMapper.selectList(new QueryWrapper<CategoryEntity>().eq("parent_cid", v.getCatId()));
    
            //2、封装上面的结果
            List<Catelog2Vo> catelog2Vos = null;
            if (categoryEntities != null) {
                catelog2Vos = categoryEntities.stream().map(l2 -> {
                    Catelog2Vo catelog2Vo = new Catelog2Vo(v.getCatId().toString(), null, l2.getCatId().toString(), l2.getName());
                    //1、找当前二级分类的三级分类，封装成vo
                    List<CategoryEntity> level3Catelog = baseMapper.selectList(new QueryWrapper<CategoryEntity>().eq("parent_cid", l2.getCatId()));
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
    ```
    
- Catelog2Vo.java
  
    ```java
    package com.atguigu.gulimall.product.vo;
    ........
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/12 16:05
     * @Version 1.0
     * @Description
     */
    //2级分类vo
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Catelog2Vo {
        private String catalog1Id;  //1级父分类id
        private List<Catelog3Vo> catalog3List;  //三级子分类
        private String id;
        private String name;
    
        /**
         * 三级分类vo
         * "catalog2Id": "1",
         * "id": "1",
         * "name": "电子书"
         */
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public static class Catelog3Vo {
            private String catalog2Id;  //父分类，2级分类id
            private String id;
            private String name;
        }
    }
    ```
    
- catalogLoader.js：静态资源里面的
  
    ```java
    $(function(){
        **$.getJSON("index/catalog.json",function (data) {**
    ```
    
- index.html
  
    ```java
    <!DOCTYPE html>
    <html lang="en" **xmlns:th="http://www.thymeleaf.org">**
    
    <head>
      <meta charset="UTF-8">
    
    ......
    
    <!--轮播主体内容-->
        <div class="header_main">
          <div class="header_banner">
            <div class="header_main_left">
              <ul>
                **<li th:each="category : ${categorys}">
                  <a href="#" class="header_main_left_a" th:attr="ctg-data=${category.catId}"><b th:text="${category.name}">家用电器1111</b></a>
                </li>**
              </ul>
    ```
    
- pom.xml
  
    ```xml
    <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
    ```
    

## 浏览器测试

[http://localhost:10000/index/catalog.json](http://localhost:10000/index/catalog.json)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)