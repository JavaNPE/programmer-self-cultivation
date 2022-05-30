# 09_新增商品-VsCode安装PubSub-VM options设置-启动多个微服务

谷粒商城-接口文档地址：[11、添加属性与分组关联关系](https://easydoc.net/s/78237135/ZUqEdvA4/VhgnaedC)

# P83、商品服务-API-新增商品-调试会员等级相关接口（需要处理前端-安装PubSub：解决PubSub is not defined"）

`npm install --save pubsub-js` 安装PubSub

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

![PubSub is not defined"](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

PubSub is not defined"

## 问题：安装PubSub：npm install --save pubsub-js

※P83或者P84 关于pubsub、publish报错，无法发送查询品牌信息的请求：

1、安装PubSub：`npm install --save pubsub-js` 

2、在src下的**main.js**中引用：

①`import PubSub from 'pubsub-js'`

② `Vue.prototype.PubSub = PubSub`

然后 npm run dev启动前端页面

![1、安装PubSub：`npm install --save pubsub-js` ](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

1、安装PubSub：`npm install --save pubsub-js` 

![2、src下的**main.js**中引用：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

2、src下的**main.js**中引用：

![同样会一上来就报错：因为有些接口还没写（网关gateway没有配置路由，或者没有启动member服务）](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

同样会一上来就报错：因为有些接口还没写（网关gateway没有配置路由，或者没有启动member服务）

浏览器端进入【商品发布】模块 F12以下路径报错

```java
Request URL: http://localhost:88/api/member/memberlevel/list?t=1633512192391&page=1&limit=500
```

01、获取所有会员等级接口文档地址：[https://easydoc.net/s/78237135/ZUqEdvA4/jCFganpf](https://easydoc.net/s/78237135/ZUqEdvA4/jCFganpf)

## 在gulimall-gateway服务application.yml中配置gulimall-member的路由请求

```yaml
- id: member_route
          uri: lb://gulimall-member
          predicates:
            - Path=/api/member/**     #只要是/api/thirdparty这些前缀的 就自动路由给第三方服务gulimall-third-party
          filters:
            - RewritePath=/api/(?<segment>/?.*),/$\{segment}
```

> 异常：
> 

Caused by: com.alibaba.nacos.api.exception.NacosException: endpoint is blank（暂时没管它）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

## 问题：会员等级-显示空页面，需要添加.vue文件

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

![重启前端服务：npm run dev](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

重启前端服务：npm run dev

# P84、商品服务-API-新增商品获取分类关联的品牌

原型页面：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

## 商品系统-接口文档：14、获取分类关联的品牌

[14、获取分类关联的品牌](https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV)

14、获取分类关联的品牌：**/product/categorybrandrelation/brands/list**

## Controller和Service作用：

1. Controller就只是来接受请求和处理页面提交来的数据，把数据封装成业务想要的，或者进行校验数据。
2. Service用来接收Controller传来的数据，进行业务处理。
3. Controller用来接收Service处理完的数据，封装成页面指定的Vo

**BrandVo：**Vo里面对应的都是接口文档里面的响应数据字段 ：

14、获取分类关联的品牌：https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV

```java
package com.atguigu.gulimall.product.vo;

import lombok.Data;

/**
 * @Author Dali
 * @Date 2021/10/6 21:51
 * @Version 1.0
 * @Description： Vo里面对应的都是接口文档里面的响应数据字段 ： 14、获取分类关联的品牌：https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV
 * <p>
 * 老师说，不是entity的全量数据都可以封装vo
 */
@Data
public class BrandVo {

    /**
     * "brandId":0
     * "brandName":"string",
     */
    private Long brandId;
    private String brandName;
}
```

**CategoryBrandRelationController：**

```java
/**
     * 获取某个分类下关联的所有品牌信息
     * 14、获取分类关联的品牌：https://easydoc.net/s/78237135/ZUqEdvA4/HgVjlzWV
     * 1、Controller就只是来接受请求和处理页面提交来的数据，把数据封装成业务想要的，或者进行校验数据
     * 2、Service用来接收Controller传来的数据，进行业务处理
     * 3、Controller用来接收Service处理完的数据，封装成页面指定的Vo
     *
     * @param catId
     * @return
     */
    //product/categorybrandrelation/brands/list
    @GetMapping("/brands/list")
    public R relationBrandsList(@RequestParam(value = "catId", required = true) Long catId) {
        List<BrandEntity> vos = categoryBrandRelationService.getBrandsByCatId(catId);
        List<BrandVo> collect = vos.stream().map(item -> {
            BrandVo brandVo = new BrandVo();
            brandVo.setBrandId(item.getBrandId());
            brandVo.setBrandName(item.getName());   //因为vos中的为name，brandVo中的为brandName，名称字段不一致，故不能使用BeanUtils进行对拷
            return brandVo;
        }).collect(Collectors.toList());    //将brandVo封装成一个集合
        return R.ok().put("data", collect);
    }
```

**CategoryBrandRelationService：**

```java
List<BrandEntity> getBrandsByCatId(Long catId);
```

**CategoryBrandRelationServiceImpl：**

```java
		@Autowired
    BrandDao brandDao;

    @Autowired
    CategoryDao categoryDao;

		@Autowired
    CategoryBrandRelationDao relationDao;

    @Autowired
    BrandService brandService;     //注入Service可以有着更丰富的业务逻辑（相比较注入dao而言【brandDao】）

		/**
     * 获取某个分类下关联的所有品牌信息
     * @param catId
     * @return
     */
    @Override
    public List<BrandEntity> getBrandsByCatId(Long catId) {
        //我们要查出一个集合
        List<CategoryBrandRelationEntity> catelogId = relationDao.selectList(new QueryWrapper<CategoryBrandRelationEntity>().eq("catelog_id", catId));
        List<BrandEntity> collect = catelogId.stream().map(item -> {
		//—— service与dao的区别
		//brandDao.selectById()     //通过brandService和brandDao都可以查出数据，但是一般业务比较复杂的情况下还是推荐使用brandService这种方式查询
            Long brandId = item.getBrandId();
            BrandEntity byId = brandService.getById(brandId);   //根据brandId查出品牌详情
            return byId;    //将品牌详情返回
        }).collect(Collectors.toList());    //将查到的【品牌详情：byId】封装成一个集合返回
        return collect;
    }
```

测试：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)

# P85、商品服务-API-新增商品-获取分类下所有分组以及属性



## 对应的接口文档地址：17、获取分类下所有分组&关联属性

17、获取分类下所有分组&关联属性：[https://easydoc.net/s/78237135/ZUqEdvA4/6JM6txHf](https://easydoc.net/s/78237135/ZUqEdvA4/6JM6txHf)

## 勘误：P77和P85（无法获取值类型）左右开始数据库里少了value_type字段，把数据库字段添上，再去mapper.xml和对应Entity与Vo中添加即可



P77（无法获取值类型）左右开始数据库里少了value_type字段，把数据库字段添上，再去mapper.xml和对应Entity与Vo中添加即可

**电风扇吹啊吹到85报错才注意到，我补充详细一点吧；在数据库的 pms_attr 表加上value_type字段，类型为tinyint就行；在代码中，AttyEntity.java、AttrVo.java中各添加：private Integer valueType，在AttrDao.xml中添加：《result property=valueType column=value_type/》  （把尖括号换成英文的）；**

## 勘误P85【发布商品-》规格参数（不显示内容）】——解决办法

无显示的把response拿出来看看是不是有些分组attrs为null,他前端没有判空逻辑，会出错！

如果P85【规格参数】里面不显示，F12查看控制台看看attrs是否存在null的情况，如果存在attrs为null的情况，一般就不会显示， 此时需要，前段页面（product→spuadd.vue文件）在第679行或者是680行将代码修改为 `(item.attrs || [] ).forEach(attr => {`进行非空判断。（如果attrs==null查看gulimail_pms数据库找出对应的数据先将其在数据库这条null空数据删除也可以正常显示【权宜之计】）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

显示不出来在680行加上判断 (item.attrs || [] ).forEach(attr => {

显示不出来在680行加上判断 (item.attrs || [] ).forEach(attr => {

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2011.png)

# P86、商品服务API-新增商品-商品新增vo抽取

## 勘误：P86销售属性不显示

销售属性如果不显示或者显示不正确，一定是数据库数据有问题，往数据库插入数据的时候极力不推荐直接在数据库插入，应该在以前写好的模块里面插入，因为涉及到级联保存。这个页面显示的所有销售属性，都是在前面录入进去的

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

## 将json转成JavaBean小工具（将json逆向生成JavaBean）

[JSON转JAVA实体|在线JSON转JavaBean工具 - JSON.cn](https://www.json.cn/json/json2java.html)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)

自动生成的JavaBean

[bejson_gen_beans.zip](09_%E5%95%86%E5%93%81%E6%9C%8D%E5%8A%A1-API-%E6%96%B0%E5%A2%9E%E5%95%86%E5%93%81-%E8%B0%83%E8%AF%95%E4%BC%9A%E5%91%98%E7%AD%89%E7%BA%A7%E7%9B%B8%E5%85%B3%E6%8E%A5%E5%8F%A3%203cc748e5c18942288db5b652c78f95a3/bejson_gen_beans.zip)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

# P87、商品服务-API-新增商品-商品新增业务流程分析

由于上述生成的代码字段类型可能不太能够满足我们的精度，所以我们需要对其适当的修改，比如关于价格的字段我们使用`BigDecimal` 而非**`double` ，**有关id的字段我们使用`Long`类型，而不是`int`类型对其进行修饰。使用Lombok注解的形式更加简洁。当然还可以添加jsr303注解对字段进行校验。

### 需要调整的内容：

- SpuSaveVo
  
    ```java
    /**
     * Copyright 2021 json.cn
     */
    package com.atguigu.gulimall.product.vo;
    
    import lombok.Data;
    
    import java.math.BigDecimal;
    import java.util.List;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class SpuSaveVo {
        private String spuName;
        private String spuDescription;
        private Long catalogId;
        private Long brandId;
        private BigDecimal weight;
        private int publishStatus;
        private List<String> decript;
        private List<String> images;
        /**
         * 积分信息
         */
        private Bounds bounds;
        /**
         * spu的基本属性
         */
        private List<BaseAttrs> baseAttrs;
        private List<Skus> skus;
    }
    ```
    
- Bounds
  
    ```java
    /**
     * Copyright 2021 json.cn
     */
    package com.atguigu.gulimall.product.vo;
    
    import lombok.Data;
    
    import java.math.BigDecimal;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class Bounds {
        private BigDecimal buyBounds;
        private BigDecimal growBounds;
    }
    ```
    
- BaseAttrs
  
    ```java
    /**
     * Copyright 2021 json.cn
     */
    package com.atguigu.gulimall.product.vo;
    
    import lombok.Data;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class BaseAttrs {
        private Long attrId;
        private String attrValues;
        private int showDesc;
    }
    ```
    
- Skus
  
    ```java
    /**
      * Copyright 2021 json.cn 
      */
    package com.atguigu.gulimall.product.vo;
    import lombok.Data;
    
    import java.math.BigDecimal;
    import java.util.List;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class Skus {
        private List<Attr> attr;
        private String skuName;
        private BigDecimal price;
        private String skuTitle;
        private String skuSubtitle;
        private List<Images> images;
        private List<String> descar;
        private int fullCount;
        private BigDecimal discount;
        private int countStatus;
        private BigDecimal fullPrice;
        private BigDecimal reducePrice;
        private int priceStatus;
        private List<MemberPrice> memberPrice;
    }
    ```
    
- Images
  
    ```java
    /**
      * Copyright 2021 json.cn 
      */
    package com.atguigu.gulimall.product.vo;
    
    import lombok.Data;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class Images {
    
        private String imgUrl;
        private int defaultImg;
    }
    ```
    
- MemberPrice
  
    ```java
    /**
     * Copyright 2021 json.cn
     */
    package com.atguigu.gulimall.product.vo;
    
    import lombok.Data;
    
    import java.math.BigDecimal;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class MemberPrice {
        private Long id;
        private String name;
        private BigDecimal price;
    }
    ```
    

### gulimall_pms表介绍：（跨库）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

### gulimall_sms表介绍：（跨库）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

由于前端发送的请求字段太多，一不小心点击一下刷新按钮，数据就会重新填写，很是浪费时间，所以这里使用postman很重要，直接复制要发送的请求，到postmansku

SpuInfoServiceImpl：

```java
/**
     * 保存前端传送的spu信息，可能会保存很多，所以我们使用事务注解@Transactional
     *
     * @param vo
     */
    @Transactional
    @Override
    public void saveSpuInfo(SpuSaveVo vo) {
        //1、保存spu基本信息：pms_spu_info
        //2、保存spu的描述图片：spm_spu_info_desc

        //3、保存spu的规格参数：pms_product_attr_value

        //4、保存spu的规格参数：pms_product_attr_value

        //5、保存spu的积分信息：gulimall_sms ——》 sms_spu_bounds （跨库）

        //5、保存当前spu对应的所有sku信息(需要跨库操作)
        //5.1 保存sku的基本信息：pms_sku_info
        //5.2 保存sku的图片信息：pms_sku_images
        //5.3 保存sku的销售属性信息：pms_sku_sale_attr_value
        //5.4 保存sku的优惠，满减等信息：gulimall_sms数据库中的sms_sku_ladder\sms_sku_full_reduction\
    }
```

# P88、商品服务-API-新增商品-保存SPU基本信息



```java
		/**
     * 保存前端传送的spu信息，可能会保存很多，所以我们使用事务注解@Transactional
     *
     * @param vo
     */
    @Transactional
    @Override
    public void saveSpuInfo(SpuSaveVo vo) {
        //1、保存spu基本信息：pms_spu_info
        //操作pms_spu_info表需要提前创建一个与其对应的实体类
        SpuInfoEntity infoEntity = new SpuInfoEntity();
        //进行属性对拷
        BeanUtils.copyProperties(vo, infoEntity);
        infoEntity.setCreateTime(new Date());
        infoEntity.setUpdateTime(new Date());
        this.saveBaseSpuInfo(infoEntity);

        //2、保存spu的描述图片：spm_spu_info_desc
        List<String> decript = vo.getDecript();
        SpuInfoDescEntity descEntity = new SpuInfoDescEntity();
        descEntity.setSpuId(infoEntity.getId());
        //使用join对其进行分割操作
        descEntity.setDecript(String.join(",", decript));
        //保存spu的描述图片信息
        spuInfoDescService.saveSpuInfoDesc(descEntity);

        //3、保存spu的规格参数：pms_product_attr_value
        List<String> images = vo.getImages();
        imagesService.saveImages(infoEntity.getId(), images);

        //4、保存spu的规格参数：pms_product_attr_value
        List<BaseAttrs> baseAttrs = vo.getBaseAttrs();
        List<ProductAttrValueEntity> collect = baseAttrs.stream().map(attr -> {
            ProductAttrValueEntity valueEntity = new ProductAttrValueEntity();
            valueEntity.setAttrId(attr.getAttrId());
            /**
             * attrName:如果用户不上传  我们需要去表中查询
             */
            AttrEntity id = attrService.getById(attr.getAttrId());
            valueEntity.setAttrName(id.getAttrName());
            valueEntity.setAttrValue(attr.getAttrValues());
            valueEntity.setQuickShow(attr.getShowDesc());
            valueEntity.setSpuId(infoEntity.getId());

            return valueEntity;
        }).collect(Collectors.toList());

        attrValueService.saveProductAttr(collect);

        //5、保存spu的积分信息：gulimall_sms ——》 sms_spu_bounds （跨库）

        
        //5.3 保存sku的销售属性信息：pms_sku_sale_attr_value
        //5.4 保存sku的优惠，满减等信息：gulimall_sms数据库中的sms_sku_ladder\sms_sku_full_reduction\sms_member_price
    }
```

# P89、商品服务-API-新增商品-保存SKU基本信息

在SpuInfoServiceImpl实现类中  具体以码云代码库为准

```java
	@Autowired
    SpuInfoDescService spuInfoDescService;

    @Autowired
    SpuImagesService imagesService;

    @Autowired
    AttrService attrService;

    @Autowired
    ProductAttrValueService attrValueService;

    @Autowired
    SkuInfoService skuInfoService;

    @Autowired
    SkuImagesService skuImagesService;

    @Autowired
    SkuSaleAttrValueService skuSaleAttrValueService;
```

SpuInfoServiceImpl

```java
//5、保存当前spu对应的所有sku信息(需要跨库操作)
        List<Skus> skus = vo.getSkus();
        if (skus != null && skus.size() > 0) {
            skus.forEach(item -> {
                /**
                 * 获取默认图片
                 */
                String defaultImg = ""; //默认图片
                for (Images image : item.getImages()) {
                    if (image.getDefaultImg() == 1) {     //1: 表示默认图片
                        defaultImg = image.getImgUrl();
                    }
                }
                //    private String skuName;
                //    private BigDecimal price;
                //    private String skuTitle;
                //    private String skuSubtitle;
                SkuInfoEntity skuInfoEntity = new SkuInfoEntity();
                BeanUtils.copyProperties(item, skuInfoEntity);
                skuInfoEntity.setBrandId(infoEntity.getBrandId());  //品牌id
                skuInfoEntity.setCatalogId(infoEntity.getCatalogId());  //三级分类id
                skuInfoEntity.setSaleCount(0L); //销量默认值0
                skuInfoEntity.setSpuId(infoEntity.getId());
                skuInfoEntity.setSkuDefaultImg(defaultImg);   //默认图片
                /**
                 * 5.1 保存sku的基本信息：pms_sku_info
                 */
                skuInfoService.saveSkuInfo(skuInfoEntity);
                /**
                 * sku的自增主键
                 */
                Long skuId = skuInfoEntity.getSkuId();

                /**
                 * 获取所有图片:
                 * 图片的保存是先保存sku 才保存图片的，所以获取默认图片要在保存sku之前获取
                 */
                List<SkuImagesEntity> imagesEntities = item.getImages().stream().map(img -> {
                    SkuImagesEntity skuImagesEntity = new SkuImagesEntity();
                    skuImagesEntity.setSkuId(skuId);
                    skuImagesEntity.setImgUrl(img.getImgUrl());
                    skuImagesEntity.setDefaultImg(img.getDefaultImg());
                    return skuImagesEntity;
                }).collect(Collectors.toList());
                /**
                 * 5.2 保存sku的图片信息：pms_sku_images
                 */
                skuImagesService.saveBatch(imagesEntities);

                /**
                 * 5.3 保存sku的销售属性信息：pms_sku_sale_attr_value
                 */
                List<Attr> attr = item.getAttr();
                List<SkuSaleAttrValueEntity> skuSaleAttrValueEntities = attr.stream().map(a -> {
                    SkuSaleAttrValueEntity attrValueEntity = new SkuSaleAttrValueEntity();
                    //如果属性值相同的话可以进行对拷
                    BeanUtils.copyProperties(a, attrValueEntity);
                    attrValueEntity.setSkuId(skuId);

                    return attrValueEntity;
                }).collect(Collectors.toList());
                //5.3 保存sku的销售属性信息：pms_sku_sale_attr_value
                skuSaleAttrValueService.saveBatch(skuSaleAttrValueEntities);
            });
        }
```

# P90、商品服务-API-新增商品-调用远程 服务保存优惠等信息

5、保存spu的积分信息：gulimall_sms ——》 sms_spu_bounds （跨库）

5.4 保存sku的优惠，满减等信息：gulimall_sms数据库中的sms_sku_ladder\sms_sku_full_reduction

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

product商品服务调用coupon远程服务可以通过feign进行远程调取：

首先保证以下三点：

1. 远程服务coupon必须上线：放到注册中心中
   
    ```java
    **@EnableDiscoveryClient**
    @SpringBootApplication
    public class GulimallCouponApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(GulimallCouponApplication.class, args);
        }
    }
    ```
    
2. 远程服务coupon必须开启服务注册与发现功能，远程调用功能
   
    ```java
    **@EnableFeignClients**(basePackages="com.atguigu.gulimall.member.feign")
    @EnableDiscoveryClient
    @SpringBootApplication
    public class GulimallMemberApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(GulimallMemberApplication.class, args);
        }
    }
    ```
    
    ```
    想要远程调用的步骤：
    1引入openfeign
    2编写一个接口，接口告诉springcloud这个接口需要调用远程服务
    2.1在接口里声明@FeignClient("gulimall-coupon")他是一个远程调用客户端且要调用coupon服务
    2.2要调用coupon服务的/coupon/coupon/member/list方法
    3开启远程调用功能@EnableFeignClients，要指定远程调用功能放的基础包
    ```
    
3. 编写一个接口，接口告诉springcloud这个接口需要调用远程服务
   
    3.1在接口里声明`@FeignClient("gulimall-coupon")`他是一个远程调用客户端且要调用coupon服务
    3.2要调用coupon服务的`/coupon/coupon/member/list`方法   
    
    例如：
    
    ```java
    package com.atguigu.gulimall.member.feign;
    
    import com.atguigu.common.utils.R;
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    /**
     * @Author Dali
     * @Date 2021/8/14 18:38
     * @Version 1.0
     * @Description
     */
    
    /**
     * 这是一个声明式的远程调用
     * @FeignClient("gulimall-coupon")他是一个远程调用客户端且要调用coupon服务
     */
    @FeignClient("gulimall-coupon") //告诉spring cloud这个接口是一个远程客户端，要调用coupon服务(nacos中找到)，具体是调用coupon服务的/coupon/coupon/member/list对应的方法
    public interface CouponFeignService {
        // 远程服务的url
        @RequestMapping("/coupon/coupon/member/list")//注意写全优惠券类上还有映射//注意我们这个地方不是控制层，所以这个请求映射请求的不是我们服务器上的东西，而是nacos注册中心的
        public R membercoupons();   //得到一个R对象
    }
    ```
    

我们在些这步代码逻辑的时候：***5、保存spu的积分信息：gulimall_sms ——》 sms_spu_bounds （跨库）***  要调用远程coupon服务，需要进行以下处理：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

1. 在GulimallProductApplication启动类中添加**@EnableFeignClients注解**
   
    ```java
    **@EnableFeignClients(basePackages = "com.atguigu.gulimall.product.feign")**
    @EnableDiscoveryClient
    @MapperScan("com.atguigu.gulimall.product.dao")
    @SpringBootApplication
    public class GulimallProductApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(GulimallProductApplication.class, args);
        }
    }
    ```
    
2. 创建一个包feign，然后在该包中创建一个接口，并添加`@FeignClient("gulimall-coupon")` 注释
   
    ```java
    package com.atguigu.gulimall.product.feign;
    
    import org.springframework.cloud.openfeign.FeignClient;
    
    /**
     * @Author Dali
     * @Date 2021/11/6 20:42
     * @Version 1.0
     * @Description
     */
    **@FeignClient("gulimall-coupon")**
    public interface CouponFeignService {
    }
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)
    
3. 在gulimall-product中`SpuInfoServiceImpl` 实现类中注入`CouponFeignService` 服务，帮助我们调用远程的所有功能
   
    ```java
    		/**
         * 为了能够使用远程服务，我们需要注入CouponFeignService接口
         * 帮我们调用远程的所用功能
         */
        @Autowired
        CouponFeignService couponFeignService;
    ```
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)
    
    ## 关于TO数据模型：
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)
    
    创建各自对应的To：
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)
    
    MemberPrice：
    
    ```java
    /**
     * Copyright 2021 json.cn
     */
    package com.atguigu.common.to;
    
    import lombok.Data;
    
    import java.math.BigDecimal;
    
    /**
     * Auto-generated: 2021-10-31 11:44:1
     *
     * @author json.cn (i@json.cn)
     * @website http://www.json.cn/java2pojo/
     */
    @Data
    public class MemberPrice {
        private Long id;
        private String name;
        private BigDecimal price;
    }
    ```
    
    SkuReductionTo：
    
    ```java
    import lombok.Data;
    
    import java.math.BigDecimal;
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/11/6 22:36
     * @Version 1.0
     * @Description： 数据传输对象：sku的积分信息，优惠信息，和满减信息等
     */
    @Data
    public class SkuReductionTo {
        private Long skuId;
        private int fullCount;
        private BigDecimal discount;
        private int countStatus;
        private BigDecimal fullPrice;
        private BigDecimal reducePrice;
        private int priceStatus;
        private List<MemberPrice> memberPrice;
    }
    ```
    
    SpuBoundTo：
    
    ```java
    package com.atguigu.common.to;
    
    import lombok.Data;
    
    import java.math.BigDecimal;
    
    /**
     * @Author Dali
     * @Date 2021/11/6 21:35
     * @Version 1.0
     * @Description： 数据传输对象: 积分信息
     */
    @Data
    public class SpuBoundTo {
        private Long spuId;
        /**
         * 成长积分
         */
        private BigDecimal buyBounds;
        /**
         * 购物积分
         */
        private BigDecimal growBounds;
    }
    ```
    
    ## 代码：
    
    GulimallProductApplication：
    
    ```java
    **@EnableFeignClients(basePackages = "com.atguigu.gulimall.product.feign")**
    @EnableDiscoveryClient
    @MapperScan("com.atguigu.gulimall.product.dao")
    @SpringBootApplication
    public class GulimallProductApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(GulimallProductApplication.class, args);
        }
    
    }
    ```
    
    SpuInfoServiceImpl：
    
    ```java
    /**
     * 为了能够使用远程服务，我们需要注入CouponFeignService接口
     * 帮我们调用远程的所用功能
     */
    @Autowired
    CouponFeignService couponFeignService;
    
    //5、保存spu的积分信息：gulimall_sms ——》 sms_spu_bounds （跨库）
    Bounds bounds = vo.getBounds(); //页面提交的积分
    SpuBoundTo spuBoundTo = new SpuBoundTo();   //数据传输对象
    BeanUtils.copyProperties(bounds, spuBoundTo);
    spuBoundTo.setSpuId(infoEntity.getId());
    //调用远程服务，结束之后我们最好需判断一下调用结果，日志记录。
    R r = couponFeignService.saveSpuBounds(spuBoundTo);
    if (r.getCode() != 0) {
        log.error("远程保存spu积分信息失败");
    }
    
    //调用远程服务
    //5.4 保存sku的优惠，满减等信息：gulimall_sms数据库中的sms_sku_ladder\sms_sku_full_reduction\sms_member_price
    SkuReductionTo skuReductionTo = new SkuReductionTo();
    BeanUtils.copyProperties(item, skuReductionTo);
    skuReductionTo.setSkuId(skuId);
    //调用远程服务，结束之后我们最好需判断一下调用结果，日志记录。
    R r1 = couponFeignService.saveSkuReduction(skuReductionTo);
    if (r1.getCode() != 0) {
        log.error("远程保sku优惠信息失败");
    }
    ```
    
    CouponFeignService：
    
    ```java
    package com.atguigu.gulimall.product.feign;
    
    import com.atguigu.common.to.SkuReductionTo;
    import com.atguigu.common.to.SpuBoundTo;
    import com.atguigu.common.utils.R;
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    
    /**
     * @Author Dali
     * @Date 2021/11/6 20:42
     * @Version 1.0
     * @Description
     */
    @FeignClient("gulimall-coupon")
    public interface CouponFeignService {
        /**
         * SpringCloud远程调用的逻辑：
         * 1、如果我们有一个service调用了CouponFeignService的saveSpuBounds(spuBoundTo)方法而且还给它传了一个对象，而不是一个基本数据类型的数据
         *      1.1 SpringCloud会做的第一步操作：使用@RequestBody注解将spuBoundTo这个对象转成json
         *      1.2 SpringCloud会去注册中心找到gulimall-coupon这个服务，给该服务的/coupon/spubounds/save发送请求，
         *          将上一步转的json放在请求体位置，发送请求；
         *      1.3 对方服务收到请求，其实收到的是请求体中的json数据
         *          (@RequestBody SpuBoundsEntity spuBounds)的作用：将请求体中的json转为SpuBoundsEntity
         * 小总结：  只要json数据模型是兼容的。双方服务无需使用同一个To
         *
         * @param spuBoundTo
         * @return
         */
        @PostMapping("/coupon/spubounds/save")
        R saveSpuBounds(@RequestBody SpuBoundTo spuBoundTo);
    
        @PostMapping("/coupon/skufullreduction/saveinfo")
        R saveSkuReduction(@RequestBody SkuReductionTo skuReductionTo);
    
        /**
         * com.atguigu.gulimall.coupon.controller.SpuBoundsController
         */
        //@PostMapping("/save")
        //@RequiresPermissions("coupon:spubounds:save")
        //只要接收到的json中的字段能够与SpuBoundsEntity对应没必要都写成(@RequestBody SpuBoundTo spuBoundTo)这种类型
        //public R save(@RequestBody SpuBoundsEntity spuBounds) { 只要json数据对的上（兼容），就可以使用这种
        //public R save(@RequestBody SpuBoundTo spuBoundTo) {
    }
    ```
    
    SpuBoundsController：
    
    ```java
    		/**
         * 保存
         */
        @PostMapping("/save")
        //@RequiresPermissions("coupon:spubounds:save")
        public R save(@RequestBody SpuBoundsEntity spuBounds) {  //只要接收到的json中的字段能够与SpuBoundsEntity对应没必要都写成(@RequestBody SpuBoundTo spuBoundTo)这种类型
        //public R save(@RequestBody SpuBoundTo spuBoundTo) {
            spuBoundsService.save(spuBounds);
    
            return R.ok();
        }
    ```
    
    SkuFullReductionController：
    
    ```java
    /**
     * 保存基础信息
     */
    @PostMapping("/saveinfo")
    //@RequiresPermissions("coupon:skufullreduction:list")
    public R saveInfo(@RequestBody SkuReductionTo skuReductionTo) {
        skuFullReductionService.saveSkuReduction(skuReductionTo);
        return R.ok();
    }
    ```
    
    SkuFullReductionService:
    
    ```java
    void saveSkuReduction(SkuReductionTo skuReductionTo);
    ```
    
    SkuFullReductionServiceImpl:
    
    ```java
    @Autowired
    SkuLadderService skuLadderService;
    /**
     * 如果是SkuFullReductionService的话我们就不需要再次注入了，直接通过this.调用即可
     */
    @Autowired
    SkuFullReductionService skuFullReductionService;
    @Autowired
    MemberPriceService memberPriceService;
    
    .....................
    @Override
        public void saveSkuReduction(SkuReductionTo reductionTo) {
            //1、5.4 保存sku的优惠，满减，会员价格等信息：gulimall_sms数据库中的sms_sku_ladder\sms_sku_full_reduction\sms_member_price
            //1、sms_sku_ladder
            SkuLadderEntity skuLadderEntity = new SkuLadderEntity();
            skuLadderEntity.setSkuId(reductionTo.getSkuId());
            skuLadderEntity.setFullCount(reductionTo.getFullCount());
            skuLadderEntity.setDiscount(reductionTo.getDiscount());
            skuLadderEntity.setAddOther(reductionTo.getCountStatus());
            skuLadderService.save(skuLadderEntity);
    
            //2、sms_sku_full_reduction：满减信息
            SkuFullReductionEntity reductionEntity = new SkuFullReductionEntity();
            //属性对拷
            BeanUtils.copyProperties(reductionTo, reductionEntity);
            /**
             * 如果是SkuFullReductionService(自己本身的Service)的话我们就不需要再次注入了，直接通过this.调用即可
             */
            this.save(reductionEntity);
    
            //3、会员价格
            List<MemberPrice> memberPrice = reductionTo.getMemberPrice();
            List<MemberPriceEntity> collect = memberPrice.stream().map(item -> {
                MemberPriceEntity priceEntity = new MemberPriceEntity();
                /**
                 * 因为MemberPriceEntity中的属性值与SkuReductionTo的属性值没有对应关系，
                 *      在此不适用BeanUtils.copyProperties进行对拷，而是使用常规赋值法。
                 */
                priceEntity.setSkuId(reductionTo.getSkuId());
                priceEntity.setMemberLevelId(item.getId());
                priceEntity.setMemberLevelName(item.getName());
                priceEntity.setMemberPrice(item.getPrice());
                priceEntity.setAddOther(1);
                return priceEntity;
            }).collect(Collectors.toList());
    
            memberPriceService.saveBatch(collect);
        }
    ```
    

# P91、商品服务- API-新增商品-商品保存debug完成

## 需要启动太多的微服务，内存吃紧解决办法

对每个服务的内存占用进行设置一下

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)

创建Compound

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2027.png)

## 设置微服务占用最大内存

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2028.png)

### 在VM options中添加 `-Xmx100m` 限制占用内存大小

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2029.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2030.png)

在Compound中改名字：gulimall

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2031.png)

### 一次重启/启动多个微服务

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2032.png)

### 执行debug

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2033.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2034.png)

MySQL默认的隔离级别是：可重复读，也就是必须最起码读到已经提交了的数据，所以为了测试方便我们使用以下命令：

```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

将当前会话的隔离等级设置成读未提交，当前窗口就可以读到没有提交的数据。
```

如果报错就去虚拟机下面的mydata/mysql/conf/里面的配置文件

### navicat 设置隔离级别还是读不出来，怎么破？navicat，不显示读未提交！

方式一：新建查询，运行选中语句

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2035.png)

方式二：鼠标右键gulimall_pms数据库，选择【命令列界面】输入`SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;` 回车，提示成功之后。通过查询语句才可以查出新数据，

navicat可以选择数据库右键运行命令行，执行这个语句

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2036.png)

Navicat通过：`SELECT * FROM pms_spu_info` 才可以查出，我们刚才页面新增的数据

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2037.png)

### 插入图片描述信息的时候抛出的异常：**`catch** (InvocationTargetException ex)`

控制台打印：Preparing: `INSERT INTO pms_spu_info_desc ( decript ) VALUES ( ? )` mybatis默认把**`spu_id`**字段当成了自增的。插入的时候只插入了`decript` 字段，并没有插入`spu_id` 字段，所以抛出异常。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2038.png)

但是我们在设计表的时候，`spu_id`这个字段并不是自增的

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2039.png)

放行debug之后，因为我们开启了事务`@Transactional` 抛出异常，所有的都结束了，其他东西肯定也都没提交，再次执行sql查询语句 ，发现我们页面录入的P50相关的数据就没有了了。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2040.png)

既然`spu_id` 字段不是自增的我们就对其修改为：**@TableId(type = IdType.INPUT) 使其成为我们自己输入的**

```sql
package com.atguigu.gulimall.product.entity;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;

import java.io.Serializable;
import java.util.Date;
import lombok.Data;

/**
 * spu信息介绍
 * 
 * @author ÍõÈ½ê¿
 * @email daki9981@qq.com
 * @date 2021-08-13 19:53:20
 */
@Data
@TableName("pms_spu_info_desc")
public class SpuInfoDescEntity implements Serializable {
	private static final long serialVersionUID = 1L;

	/**
	 * 商品id
	 */
	**@TableId(type = IdType.INPUT)**
	private Long spuId;
	/**
	 * 商品介绍
	 */
	private String decript;

}
```

修改完之后我们重启：GulimallProductApplication :10000/服务，重新debug，发现成功保存spu的描述图片信息。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2041.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2042.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2043.png)

这里失败的话在商品配置文件设置超时时间 ribbon.ReadTimeout=5000 ribbon.ConnectTime=5000

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2044.png)

修改：R中的getCode()方法，然后重启GulimallProductApplication :10000/服务

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2045.png)

```sql
public Integer getCode() {
    return (Integer) this.get("code");
}
```

执行sql语句查询：`SELECT * FROM pms_sku_info`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2046.png)

5.2 保存sku的图片信息：pms_sku_images  ***`TODO 没有图片路径的，无需保存`***

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2047.png)

5.3 保存sku的销售属性信息：pms_sku_sale_attr_value

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2048.png)

debug完成之后：回到对应的数据库表中，就可以查看我们录入的信息了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2049.png)

初级开发工程师

# P92、商品服务-API-新增商品-商品保存其他问题处理

## 如何比较BigDecimal类型的值？

```java
private BigDecimal fullPrice;

//fullPrice与0比较 如果返回1则表示fullPrice的值比0大
skuReductionTo.getFullPrice().compareTo(new BigDecimal(0)) == 1
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2050.png)

SpuInfoServiceImpl:

```java
/**
 * 获取所有图片:
 * 图片的保存是先保存sku 才保存图片的，所以获取默认图片要在保存sku之前获取
 */
List < SkuImagesEntity > imagesEntities = item.getImages().stream().map(img - > {
    SkuImagesEntity skuImagesEntity = new SkuImagesEntity();
    skuImagesEntity.setSkuId(skuId);
    skuImagesEntity.setImgUrl(img.getImgUrl());
    skuImagesEntity.setDefaultImg(img.getDefaultImg());
    return skuImagesEntity;
**}).filter(entity - > {
    //返回true就是需要，返回false就会过滤掉（剔除）
    return !StringUtils.isEmpty(entity.getImgUrl());**
}).collect(Collectors.toList());
```

```java
//fullPrice与0比较 如果返回1则表示fullPrice的值比0大
if(skuReductionTo.getFullCount() > 0 || skuReductionTo.getFullPrice().compareTo(new BigDecimal(0)) == 1) {
    //调用远程服务，结束之后我们最好需判断一下调用结果，日志记录。
    R r1 = couponFeignService.saveSkuReduction(skuReductionTo);
    if(r1.getCode() != 0) {
        log.error("远程保sku优惠信息失败");
    }
}
```

### BigDecimal类型的值与0进行比较：

SkuFullReductionServiceImpl：

```java
//如果实际打折大于0，意思就是有实际打折的情况，我们就保存
if(reductionTo.getFullCount() > 0) {
    skuLadderService.save(skuLadderEntity);
}
```

```java
//fullPrice与0比较 如果返回==1,则表示fullPrice的值大于0，意思就是有实际的满减信息
 if(reductionEntity.getFullPrice().compareTo(new BigDecimal("0")) == 1) {
     /**
      * 如果是SkuFullReductionService(自己本身的Service)的话我们就不需要再次注入了，直接通过this.调用即可
      */
     this.save(reductionEntity);
 }
```

```java
//3、会员价格
List < MemberPrice > memberPrice = reductionTo.getMemberPrice();
List < MemberPriceEntity > collect = memberPrice.stream().map(item - > {
    MemberPriceEntity priceEntity = new MemberPriceEntity();
    /**
     * 因为MemberPriceEntity中的属性值与SkuReductionTo的属性值没有对应关系，
     *      在此不适用BeanUtils.copyProperties进行对拷，而是使用常规赋值法。
     */
    priceEntity.setSkuId(reductionTo.getSkuId());
    priceEntity.setMemberLevelId(item.getId());
    priceEntity.setMemberLevelName(item.getName());
    priceEntity.setMemberPrice(item.getPrice());
    priceEntity.setAddOther(1);
    return priceEntity;
**}).filter(item - > { //memberPrice的值与0比较 如果返回1则表示memberPrice的值比0大
    return item.getMemberPrice().compareTo(new BigDecimal("0")) == 1;**
}).collect(Collectors.toList());
```