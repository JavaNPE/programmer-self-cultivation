# 17_Nginx动静分离-kibana-Elasticsearc排序、分页、高亮&DSL测试

# P173、商城业务-检索服务-搭建页面环境

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016457.png)

因为nginx中我们配置了static文件，又搜索服务为

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016459.png)

统一替换：

替换超链接：`href=”  —> href="/static/search/`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016460.png)

替换图片：`src=”` —> `src="/static/search/`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016461.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016462.png)

将静态资源文件放置在`/mydata/nginx/html/static/search` 服务器目录中

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016463.png)

## Nginx动静分离（Search服务）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016464.png)

修改配置文件

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016465.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016466.png)

wq 退出并保存

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016467.png)

然后重启docker、nginx

`[root@10 conf.d]# docker restart nginx`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016468.png)

## 修改系统host文件：

新增：`192.168.56.10 [search.gulimall.com](http://search.gulimall.com/)` 配置

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016469.png)

## Nginx转发效果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016470.png)

## 测试：

我们访问：[http://search.gulimall.com/](http://search.gulimall.com/) 地址 会自动跳转到 检索服务页面，而不是在首页

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016471.png)

修改完配置之后重启nginx  `[root@10 conf.d]# docker restart nginx`

查看是否重启成功：`[root@10 conf.d]# docker ps`

server_name 中可以使用 空格配置多个地址服务。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016472.png)

我们访问：[http://search.gulimall.com/](http://search.gulimall.com/) 地址 会自动跳转到 检索服务页面，而不是在首页。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016473.png)

### 本期代码：

- gulimall-gateway中application.yml
  
    ```java
    修改：
    # 只要是gulimall.com域名下的所有请求，我们都转给gulimall-product微服务,PS一样要放在最后面
    - id: gulimall_host_route
      uri: lb://gulimall-product
      predicates:
        - Host=gulimall.com
    
    新增：
    # 只要以search.gulimall.com开头，我们就将其负载均衡到gulimall-search服务
    - id: gulimall_search_route
      uri: lb://gulimall-search
      predicates:
        - Host=search.gulimall.com
    ```
    
- gulimall-search 中 src\main\resources\templates    index.html
  
    ```html
    
    ```
    
- gulimall-search pom.xml
  
    ```json
    <!--模板引擎：thymeleaf-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```
    

# P174、商城业务-检索服务-调整页面跳转

关闭thymeleaf缓存

```java
spring.thymeleaf.cache=false
```

```java
<div style="overflow: hidden">
    <a href="http://gulimall.com" class="header_head_p_a1" style="width:73px;">
        谷粒商城首页
    </a>
    <a href="/static/search/#" class="header_head_p_a">
        <!--<img src="/static/search/img/img_05.png" style="border-radius: 50%;"/>-->
        北京</a>
</div>
```

```java
<div class="logo">
    <a href="http://gulimall.com"><img src="/static/search/./image/logo1.jpg" alt=""></a>
</div>
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016474.png)

**gulimall.conf**

```java
server {
    listen       80;
    server_name  gulimall.com  *.gulimall.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;
    location /static/ {
        root    /usr/share/nginx/html;
    }
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
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

针对**search.gmall.com**

```java
http://search.gmall.com/list.html?catalog3Id=250
```

如若跳转显示[http://search.gulimall.com/list.html?catalog3Id](http://search.gulimall.com/list.html?catalog3Id)  需要在product模块中catalogLoader.js里找到gmall改一下

```java
var cata3link = $("<a href=\"http://search.gulimall.com/list.html?catalog3Id="+ctg3.id+"\" style=\"color: #999;\">" + ctg3.name + "</a>");
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016475.png)

别忘了nginx中也需要改

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016476.png)

将index.html 改成list.html之后，浏览器访问[http://search.gulimall.com/](http://search.gulimall.com/) 显示

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016477.png)

尝试添加 在/mydata/nginx/conf/conf.d/gulimall.conf配置文件中添加 [search.gulimall.com](http://search.gulimall.com) 然后重启

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016478.png)

发现还是不行，然后保留index.thml的形式，不在改名list.html。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016479.png)

- 修改：gulimall-product-index.html
  
    ```html
    <div class="header_form">
      <input id="searchText" type="text" placeholder="" />
      <span style="background: url('/static/index/img/img_12.png') 0 -1px;"></span>
      <!--<button><i class="glyphicon"></i></button>-->
      **<a href="javascript:search();" ><img src="/static/index/img/img_09.png"/></a>**
    </div>
    ```
    
    ```java
    function search() {
      var keyword=$("#searchText").val()
      window.location.href="http://search.gulimall.com/list.html?keyword="+keyword;
    }
    ```
    
- 修改：gulimall-product-application.yml  关闭thymeleaf缓存
  
    ```html
    thymeleaf:
        cache: false
    ```
    
- 添加：gulimall-search-pom热部署
  
    ```html
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
    ```
    

# P175、商城业务-检索服务-检索查询参数模型分析抽取

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016480.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016481.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016482.png)

## 代码：

- SearchController.java
  
    ```java
    package com.atguigu.gulimall.search.controller;
    
    import com.atguigu.gulimall.search.service.MallSearchService;
    import com.atguigu.gulimall.search.vo.SearchParam;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    
    /**
     * @Author Dali
     * @Date 2022/3/30 22:03
     * @Version 1.0
     * @Description
     */
    @Controller
    public class SearchController {
    
    	@Autowired
    	MallSearchService mallSearchService;
    
    	/**
    	 * 自动将页面提交过来的所有请求查询参数封装成指定的对象
    	 *
    	 * @param param
    	 * @return
    	 */
    	@GetMapping("/list.html")
    	public String listPage(SearchParam param) {
    
    		Object result = mallSearchService.search(param);
    		return "index";
    	}
    }
    ```
    
- MallSearchService.java
  
    ```java
    package com.atguigu.gulimall.search.service;
    
    import com.atguigu.gulimall.search.vo.SearchParam;
    
    /**
     * @Author Dali
     * @Date 2022/4/3 15:41
     * @Version 1.0
     * @Description
     */
    public interface MallSearchService {
    	/**
    	 *
    	 * @param param 检索的所有参数
    	 * @return 返回检索的结果
    	 */
    	Object search(SearchParam param);
    }
    ```
    
- MallSearchServiceImpl.java
  
    ```java
    package com.atguigu.gulimall.search.service.impl;
    
    import com.atguigu.gulimall.search.service.MallSearchService;
    import com.atguigu.gulimall.search.vo.SearchParam;
    import org.springframework.stereotype.Service;
    
    /**
     * @Author Dali
     * @Date 2022/4/3 15:41
     * @Version 1.0
     * @Description
     */
    @Service
    public class MallSearchServiceImpl implements MallSearchService {
    	@Override
    	public Object search(SearchParam param) {
    		return null;
    	}
    }
    ```
    
- SearchParam.java
  
    ```java
    package com.atguigu.gulimall.search.vo;
    
    import lombok.Builder;
    import lombok.Data;
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2022/4/3 15:36
     * @Version 1.0
     * @Description 封装页面可能传递过来的所有查询条件
     */
    @Data
    @Builder
    public class SearchParam {
    	/**
    	 * 页面传递过来的全文匹配关键字
    	 */
    	private String keyword;
    
    	/**
    	 * 三级分类id
    	 */
    	private Long catalog3Id;
    
    	/**
    	 * 排序条件：
    	 * sort=saleCount_asc/desc 销量
    	 * sort=skuPrice_asc/desc  sku
    	 * sort=hotScore_asc/desc  热度
    	 */
    	private String sort;
    
    	/**
    	 * 好多的过滤条件
    	 * hasStock(是否有货),skuPrice区间，brandId，catalog3Id,attrs
    	 */
    	private Integer hasStock;	//是否只显示有货0、1
    	private String skuPrice;	//价格区间
    	private List<Long> brandId;		//按照品牌id进行查询、可以多选
    	private List<String> attrs;		//按照属性进行筛选
    	private Integer pageNum;		//按照页码筛选
    }
    ```
    

# P176、商城业务-检索服务-检索返回结果模型分析抽取

gulimall-search服务

- SearchController.java
  
    ```java
    /**
    	 * 自动将页面提交过来的所有请求查询参数封装成指定的对象
    	 *
    	 * @param param
    	 * @return
    	 */
    	@GetMapping("/list.html")
    	public String listPage(SearchParam param, Model model) {
    
    		// 1、根据传递过来的页面查询参数，去es中检索商品
    		SearchResult result = mallSearchService.search(param);
    		model.addAttribute("result", result);
    		return "index";
    	}
    ```
    
- MallSearchService.java
  
    ```java
    public interface MallSearchService {
    	/**
    	 *
    	 * @param param 检索的所有参数
    	 * @return 返回检索的结果，里面包含页面的所有信息
    	 */
    	SearchResult search(SearchParam param);
    }
    ```
    
- MallSearchServiceImpl.java
  
    ```java
    @Service
    public class MallSearchServiceImpl implements MallSearchService {
    	@Override
    	public SearchResult search(SearchParam param) {
    		return null;
    	}
    }
    ```
    
- SearchResult.java
  
    ```java
    package com.atguigu.gulimall.search.vo;
    
    import com.atguigu.common.to.es.SkuEsModel;
    import lombok.Builder;
    import lombok.Data;
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2022/4/3 16:17
     * @Version 1.0
     * @Description
     */
    @Data
    @Builder
    public class SearchResult {
    	// 查询到的所有商品信息
    	private List<SkuEsModel> products;
    	/**
    	 * 以下是分页信息
    	 */
    	private Integer pageNum;    //当前页面
    	private Long total;            //总记录数
    	private Integer totalPages;    //总页码
    
    	private List<BrandVo> brands;    //当前查询到的结果，所有涉及到的品牌
    	private List<CatalogVo> catalogs;    //当前查询到的结果，所有涉及到的分类
    	private List<AttrVo> attrs;    //当前查询到的结果，所有涉及到的属性
    
    	//=============以上是返回给页面的所有信息================
    
    	@Data
    	public static class BrandVo {
    		private Long brandId;
    		private String brandName;
    		private String brandImg;
    	}
    
    	@Data
    	public static class CatalogVo {
    		private Long catalogId;
    		private String catalogName;
    	}
    
    	@Data
    	public static class AttrVo {
    		private Long attrId;
    		private String attrName;
    		private List<String> attrValue;
    	}
    }
    ```
    

# P177、商城业务-检索服务-检索DSL测试-查询部分

## kibana地址：

[](http://192.168.56.10:5601/app/kibana#/dev_tools/console?_g=())

检查所有商品：

```java
GET product/_search
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016483.png)

按条件搜索华为相关：

```java
GET product/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "skuTitle": "华为"
          }
        }
      ]
    }
  }
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206031016484.png)

# P179、商城业务-检索服务-SearchRequest构建-检索

## 代码：

- gulimall-search    MallSearchServiceImpl.java
  
    ```java
    package com.atguigu.gulimall.search.service.impl;
    
    import java.io.IOException;
    
    /**
     * @Author Dali
     * @Date 2022/4/3 15:41
     * @Version 1.0
     * @Description
     */
    @Service
    public class MallSearchServiceImpl implements MallSearchService {
    	@Autowired
    	private RestHighLevelClient client;
    
    	//去es中进行检索
    	@Override
    	public SearchResult search(SearchParam param) {
    
    		// 1、动态构建出查询需要的DSL语句
    		SearchResult result = null;
    
    		// 1、准备检索请求
    		SearchRequest searchRequest = buildSearchRequest(param);
    		try {
    			// 2、执行检索请求
    			SearchResponse response = client.search(searchRequest, GulimallElasticSearchConfig.COMMON_OPTIONS);
    
    			// 3、分析响应数据封装成我们需要的格式
    			result = buildSearchResult(response);
    		} catch (IOException e) {
    			e.printStackTrace();
    		}
    		return result;
    	}
    
    	/**
    	 * 准备检索请求
    	 * 模糊匹配。过滤(按照属性,分类，品牌，价格区间，库存)，排序，分页，高亮，聚合分析
    	 *
    	 * @return
    	 */
    	private SearchRequest buildSearchRequest(SearchParam param) {
    		// 构建DSL语句
    		SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
    		SearchRequest searchRequest = new SearchRequest(new String[]{EsConstant.PRODUCT_INDEX}, sourceBuilder);
    
    		/*
    		 * 查询：模糊匹配，过滤(按照属性，分类，品牌，价格区间，库存)
    		 */
    		BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    		if (!StringUtils.isEmpty(param.getKeyword())) {
    			boolQuery.must(QueryBuilders.matchQuery("skuTitle", param.getKeyword()));
    		}
    
    		if (param.getCatalog3Id() != null) {
    			boolQuery.filter(QueryBuilders.termQuery("catalog3Id", param.getCatalog3Id()));
    		}
    
    		if (param.getBrandId() != null && param.getBrandId().size() > 0) {
    			boolQuery.filter((QueryBuilders.termsQuery("brandId", param.getBrandId())));
    		}
    
    		if (param.getAttrs() != null && param.getAttrs().size() > 0) {
    
    			for (String attrStr : param.getAttrs()) {
    				BoolQueryBuilder nestedBoolQuery = QueryBuilders.boolQuery();
    				// 格式：2_16G:8G
    				String[] s = attrStr.split("_");
    				String attrId = s[0];    // 检索的属性id
    				String[] attrValues = s[1].split(":");    // 这个属性的检索用的值
    				nestedBoolQuery.must(QueryBuilders.termQuery("attrs.attrId", attrId));
    				nestedBoolQuery.must(QueryBuilders.termsQuery("attrs.attrValue", attrValues));
    
    				// 每一个必须都得生成一个nested查询
    				NestedQueryBuilder nestedQuery = QueryBuilders.nestedQuery("attrs", nestedBoolQuery, ScoreMode.None);
    				boolQuery.filter(nestedQuery);
    			}
    
    		}
    
    		// 1:有库存，0：无库存
    		boolQuery.filter(QueryBuilders.termQuery("hasStock", param.getHasStock() == 1));
    
    		if (!StringUtils.isEmpty(param.getSkuPrice())) {
    			// 价格区间：1_500/_500/500_
    			RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("skuPrice");
    			String[] s = param.getSkuPrice().split("_");
    			if (s.length == 2) {
    				// 区间 大于等于第一位，小于等于第二位
    				rangeQuery.gte(s[0]).lte(s[1]);
    			} else if (s.length == 1) {
    				if (param.getSkuPrice().startsWith("_")) {
    					rangeQuery.lte(s[1]);
    				}
    
    				if (param.getSkuPrice().endsWith("_")) {
    					rangeQuery.gte(s[0]);
    				}
    			}
    		}
    
    		sourceBuilder.query(boolQuery);
    
    		/*
    		 * 排序，分页，高亮，
    		 */
    
    		/**
    		 *  聚合分析
    		 */
    		return searchRequest;
    	}
    
    	/**
    	 * 构建结果数据
    	 *
    	 * @param response
    	 * @return
    	 */
    	private SearchResult buildSearchResult(SearchResponse response) {
    		return null;
    	}
    }
    ```
    
- EsConstant.java
  
    ```java
    public class EsConstant {
        /**
         * sku数据在es中的索引(product 迁移至 guliamll_product)
         */
        public static final String PRODUCT_INDEX = "gulimall_product";
    }
    ```
    

# P180、商城业务-检索服务-SearchRequest构建-排序、分页、高亮&测试

postman:[http://localhost:12000/list.html](http://localhost:12000/list.html)

idea控制台打印：

```java
构建的DSL{"from":0,"size":2,"query":{"bool":{"filter":[{"term":{"hasStock":{"value":true,"boost":1.0}}}],"adjust_pure_negative":true,"boost":1.0}}}
```

postman:[http://localhost:12000/list.html?keyword=华为](http://localhost:12000/list.html?keyword=%E5%8D%8E%E4%B8%BA)

idea控制台打印：

```java
构建的DSL{"from":0,"size":2,"query":{"bool":{"must":[{"match":{"skuTitle":{"query":"华为","operator":"OR","prefix_length":0,"max_expansions":50,"fuzzy_transpositions":true,"lenient":false,"zero_terms_query":"NONE","auto_generate_synonyms_phrase_query":true,"boost":1.0}}}],"filter":[{"term":{"hasStock":{"value":true,"boost":1.0}}}],"adjust_pure_negative":true,"boost":1.0}},"highlight":{"pre_tags":["<b style='color:red'>"],"post_tags":["</b>"],"fields":{"skuTitle":{}}}}
```