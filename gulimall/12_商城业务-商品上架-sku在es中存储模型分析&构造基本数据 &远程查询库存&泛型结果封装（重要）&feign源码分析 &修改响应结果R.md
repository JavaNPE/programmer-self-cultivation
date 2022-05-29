# 12_商城业务-商品上架-sku在es中存储模型分析&构造基本数据 &远程查询库存&泛型结果封装（重要）&feign源码分析 &修改响应结果R

# P128、商城业务-商品上架-sku在es中存储模型分析

## 一、商品上架

**ES在内存中**，所以在检索中优于mysql。ES也支持集群，数据分片存储。

需求：

- 上架的商品才可以在网站展示。
- 上架的商品需要可以被检索。

### **分析sku在es中如何存储？**

商品mapping

分析：商品上架在es中是存sku还是spu？

1）、检索的时候输入名字，是需要按照sku的title进行全文检索的

2）、检素使用商品规格，规格是spu的公共属性，每个spu是一样的

3）、按照分类id进去的都是直接列出spu的，还可以切换。

4〕、我们如果将sku的全量信息保存到es中（包括spu属性〕就太多字段了

方案1：

```java
{
    skuId:1
    spuId:11
    skyTitile:华为xx
    price:999
    saleCount:99
    attr:[
        {尺寸:5},
        {CPU:高通945},
        {分辨率:全高清}
	]
缺点：如果每个sku都存储规格参数(如尺寸)，会有冗余存储，因为每个spu对应的sku的规格参数都一样
```

方案2：

```java
sku索引
{
    spuId:1
    skuId:11
}
attr索引
{
    skuId:11
    attr:[
        {尺寸:5},
        {CPU:高通945},
        {分辨率:全高清}
	]
}
先找到4000个符合要求的spu，再根据4000个spu查询对应的属性，封装了4000个id，long 8B*4000=32000B=32KB
1K个人检索，就是32MB

结论：如果将规格参数单独建立索引，会出现检索时出现大量数据传输的问题，会引起网络网络
```

因此选用方案1，以空间换时间。

### **建立product索引**

最终选用的数据模型：

`{ “type”: “keyword” }`, # 保持数据精度问题，可以检索，但不分词

`“analyzer”: “ik_smart”` # 中文分词器

`“index”: false`, # 不可被检索，不生成index

`“doc_values”: false` # 默认为true，不可被聚合，es就不会维护一些聚合的信息

```java
PUT product
{
    "mappings":{
        "properties": {
            "skuId":{ "type": "long" },
            "spuId":{ "type": "keyword" },  # 不可分词
            "skuTitle": {
                "type": "text",
                "analyzer": "ik_smart"  # 中文分词器
            },
            "skuPrice": { "type": "keyword" },  # 保证精度问题
            "skuImg"  : { "type": "keyword" },  # 视频中有false
            "saleCount":{ "type":"long" },
            "hasStock": { "type": "boolean" },
            "hotScore": { "type": "long"  },
            "brandId":  { "type": "long" },
            "catalogId": { "type": "long"  },
            "brandName": {"type": "keyword"}, # 视频中有false
            "brandImg":{
                "type": "keyword",
                "index": false,  # 不可被检索，不生成index，只用做页面使用
                "doc_values": false # 不可被聚合，默认为true
            },
            "catalogName": {"type": "keyword" }, # 视频里有false
            "attrs": {
                "type": "nested",
                "properties": {
                    "attrId": {"type": "long"  },
                    "attrName": {
                        "type": "keyword",
                        "index": false,
                        "doc_values": false
                    },
                    "attrValue": {"type": "keyword" }
                }
            }
        }
    }
}
```

如果检索不到商品，自己用postman测试一下，可能有的字段需要更改，你也可以把没必要的"keyword"去掉

冗余存储的字段：不用来检索，也不用来分析，节省空间

> 库存是bool。
> 
> 
> 检索品牌id，但是不检索品牌名字、图片
> 
> 用skuTitle检索
> 

### **nested嵌入式对象**

属性是"type": “nested”,因为是内部的属性进行检索

数组类型的对象会被扁平化处理（对象的每个属性会分别存储到一起）

```java
user.name=["aaa","bbb"]
user.addr=["ccc","ddd"]

这种存储方式，可能会发生如下错误：
错误检索到{aaa,ddd}，这个组合是不存在的
```

数组的扁平化处理会使检索能检索到本身不存在的，为了解决这个问题，就采用了嵌入式属性，数组里是对象时用嵌入式属性（不是对象无需用嵌入式属性）

nested阅读：[https://blog.csdn.net/weixin_40341116/article/details/80778599](https://blog.csdn.net/weixin_40341116/article/details/80778599)

使用聚合：[https://blog.csdn.net/kabike/article/details/101460578](https://blog.csdn.net/kabike/article/details/101460578)

### Es-数组的扁平化处理

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

# P130、商城业务商品上架-构造基本数据

## 商品上架接口地址：

[20、商品上架](https://easydoc.net/doc/75716633/ZUqEdvA4/DhOtFr4A)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

按skuId上架

POST /product/spuinfo/{spuId}/up

- SpuInfoController
  
    ```java
    /**
         * 20、商品上架POST :product/spuinfo/{spuId}/up
         * 接口文档地址：https://easydoc.net/doc/75716633/ZUqEdvA4/DhOtFr4A
         *
         * @param spuId
         * @return
         */
        @PostMapping("/{spuId}/up")
        public R spuUp(@PathVariable("spuId") Long spuId) {
            spuInfoService.up(spuId);
            return R.ok();
        }
    ```
    
- SpuInfoService.java
  
    ```java
    /**
         * 商品上架
         *
         * @param spuId
         */
        void up(Long spuId);
    ```
    
- SpuInfoServiceImpl.java
  
    ```java
    /**
         * 为了远程调用品牌服务，查询相应的品牌名称等信息
         */
        @Autowired
        BrandService brandService;
    
        /**
         * 为了远程调用商品分类服务，查询相应的分类名称等信息
         */
        @Autowired
        CategoryService categoryService;
    
    @Override
        public void up(Long spuId) {
            //1、查出当前spuId对应的所有sku信息，品牌的名字。
            List<SkuInfoEntity> skus = skuInfoService.getSkusBySpuId(spuId);
    
            //TODO 4、查询当前sku的所有可以被用来检索（search_type：0否，1是）的规格属性。
    
            //2、封装每个sku的信息
            List<SkuEsModel> upProducts = skus.stream().map(sku -> {
                //组装需要的数据
                SkuEsModel esModel = new SkuEsModel();
                /**
                 * 将当前正在遍历的sku里面的数据，拷贝到esModel中
                 */
                BeanUtils.copyProperties(sku, esModel);
    
                //对比SkuInfoEntity和SkuEsModel中的字段发现：skuPrice,skuImg,hasStock,hotScore等有些字段名称不一样，或者压根就没有以下字段，需要我们单独处理（查询）
                esModel.setSkuPrice(sku.getPrice());
                esModel.setSkuImg(sku.getSkuDefaultImg());
                //hasStock,hotScore
                //TODO 1、发送远程调用，库存系统查询是否含有库存（hasStock）
    
                //TODO 2、热度评分。新产品默认置：0
    
                //TODO 3、查询品牌（brandName）和分类的名字(catalogName)信息。
                BrandEntity brand = brandService.getById(esModel.getBrandId());
                esModel.setBrandName(brand.getName());
                esModel.setBrandImg(brand.getLogo());
    
                CategoryEntity category = categoryService.getById(esModel.getCatalogId());
                esModel.setCatalogName(category.getName());
    
                /**
                 *  private String brandName;
                 *  private String brandImg;
                 *  private String catalogName;
                 *  private List<Attr> attrs;
                 *
                 *@Data
                 *public static class Attr {
                 *      private Long attrId;
                 *      private String attrName;
                 *      private String attrValue;
                 *}
                 */
                return esModel;
            }).collect(Collectors.toList());
    
            //TODO 5、将数据发给es进行保存：gulimall-search
        }
    ```
    
- SkuInfoService.java
  
    ```java
    List<SkuInfoEntity> getSkusBySpuId(Long spuId);
    ```
    
- SkuInfoServiceImpl.java
  
    ```java
    @Override
        public List<SkuInfoEntity> getSkusBySpuId(Long spuId) {
            /**
             * 查询集合信息
             */
            List<SkuInfoEntity> list = this.list(new QueryWrapper<SkuInfoEntity>().eq("spu_id", spuId));
            return list;
        }
    ```
    
- 商品上架需要在es中保存spu信息并更新spu的状态信息，由于`SpuInfoEntity`与索引的数据模型并不对应，所以我们要建立专门的SkuEsModel进行数据传输
- SkuEsModel.java
  
    ```java
    package com.atguigu.common.to.es;
    
    import jdk.internal.util.xml.impl.Attrs;
    import lombok.Data;
    
    import java.math.BigDecimal;
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/4 9:36
     * @Version 1.0
     * @Description
     */
    
    /**
     * PUT product
     * {
     *     "mappings":{
     *         "properties": {
     *             "skuId":{ "type": "long" },
     *             "spuId":{ "type": "keyword" },  # 不可分词
     *             "skuTitle": {
     *                 "type": "text",
     *                 "analyzer": "ik_smart"  # 中文分词器
     *             },
     *             "skuPrice": { "type": "keyword" },  # 保证精度问题
     *             "skuImg"  : { "type": "keyword" },  # 视频中有false
     *             "saleCount":{ "type":"long" },
     *             "hasStock": { "type": "boolean" },
     *             "hotScore": { "type": "long"  },
     *             "brandId":  { "type": "long" },
     *             "catalogId": { "type": "long"  },
     *             "brandName": {"type": "keyword"}, # 视频中有false
     *             "brandImg":{
     *                 "type": "keyword",
     *                 "index": false,  # 不可被检索，不生成index，只用做页面使用
     *                 "doc_values": false # 不可被聚合，默认为true
     *             },
     *             "catalogName": {"type": "keyword" }, # 视频里有false
     *             "attrs": {
     *                 "type": "nested",
     *                 "properties": {
     *                     "attrId": {"type": "long"  },
     *                     "attrName": {
     *                         "type": "keyword",
     *                         "index": false,
     *                         "doc_values": false
     *                     },
     *                     "attrValue": {"type": "keyword" }
     *                 }
     *             }
     *         }
     *     }
     * }
     */
    @Data
    public class SkuEsModel {
        private Long skuId;
        private Long spuId;
        private String skuTitle;
        private BigDecimal skuPrice;
        private String skuImg;
        private Long saleCount;
        /**
         * 是否拥有库存
         */
        private boolean hasStock;
        /**
         * 热度评分
         */
        private Long hotScore;
        /**
         * 品牌id
         */
        private Long brandId;
        /**
         * 分类id
         */
        private Long catalogId;
        private String brandName;
        private String brandImg;
        private String catalogName;
        /**
         * 商品规格属性信息
         */
        private List<Attrs> attrs;
    
        @Data
        public static class Attrs{
            private Long attrId;
            private String attrName;
            private String attrValue;
        }
    }
    ```
    

# P131、 商城业务-商品上架-构造sku检索属性



## 代码

- SpuInfoServiceImpl.java
  
    ```java
    //TODO 4、查询当前sku的所有可以被用来检索（search_type：0否，1是）的规格属性。
            List<ProductAttrValueEntity> baseAttrs = attrValueService.baseAttrlistforspu(spuId);
            List<Long> attrIds = baseAttrs.stream().map(attr -> {
                return attr.getAttrId();
            }).collect(Collectors.toList());
    
            List<Long> searchAttrIds = attrService.selectSearchAttrIds(attrIds);
            //将List转成Set:无序不可重复
            Set<Long> idSet = new HashSet<>(searchAttrIds);
    
    //        List<SkuEsModel.Attrs> attrs = new ArrayList<>();
            List<SkuEsModel.Attrs> attrsList = baseAttrs.stream().filter(item -> {
                return idSet.contains(item.getAttrId());
            }).map(item -> {
                SkuEsModel.Attrs attrs1 = new SkuEsModel.Attrs();
                BeanUtils.copyProperties(item, attrs1);
                return attrs1;
            }).collect(Collectors.toList());
    
    //TODO 2、热度评分(hotScore)。新产品默认置：0 【暂时默认      】
                esModel.setHotScore(0L);
    
    //设置检索属性
                esModel.setAttrs(attrsList);
    ```
    
- AttrService.java
  
    ```java
    /**
         * 在指定的pms_attr所有属性集合里面，挑选出检索属性【search_type】
         *
         * @param attrIds
         * @return
         */
        List<Long> selectSearchAttrIds(List<Long> attrIds);
    ```
    
- AttrServiceImpl.java
  
    ```java
    @Override
        public List<Long> selectSearchAttrIds(List<Long> attrIds) {
            /**
             * SELECT attr_id FROM pms_attr WHERE attr_id IN (?) AND search_type = 1
             */
            return baseMapper.selectSearchAttrIds(attrIds);
        }
    ```
    
- AttrDao.java
  
    ```java
    List<Long> selectSearchAttrIds(@Param("attrIds") List<Long> attrIds);
    ```
    
- AttrDao.xml
  
    ```java
    <select id="selectSearchAttrIds" resultType="java.lang.Long">
            SELECT attr_id FROM pms_attr WHERE attr_id IN
            <foreach collection="attrIds" item="id" separator="," open="(" close=")">
                #{id}
            </foreach>
            AND search_type = 1
    </select>
    ```
    

# P132、商城业务-商品上架-**远程查询库存**&泛型结果封装

在ware微服务里添加"查询sku是否有库存"的controller

- WareSkuController.java
  
    ```java
    /**
         * 查询是否有库存
         * sku的规格参数相同，因此我们要将查询规格参数提前，只查询一次
         *
         * @RequestBody: 将请求体中的json数据转成List<Long>集合
         */
        @PostMapping("/hasstock")
        public R<List<SkuHasStockVo>> getSkusHashStock(@RequestBody List<Long> skuIds) {
    
            //表：wms_ware_sku 只需要返回当前sku_id和当前的库存量stock只要大于0就是有库存
            List<SkuHasStockVo> vos = wareSkuService.getSkusHashStock(skuIds);
    
            R<List<SkuHasStockVo>> ok = R.ok();
            ok.setData(vos);
            //return R.ok().put("data", vos);
            return ok;
        }
    ```
    
- SpuInfoServiceImpl.java
  
    ```java
        @Autowired
        WareFeignService wareFeignService;
    
    List<Long> skuIdList = skus.stream().map(SkuInfoEntity::getSkuId).collect(Collectors.toList());
    
    //hasStock,hotScore
            //TODO 1、发送远程调用，库存系统查询是否含有库存（hasStock）如果有：true,否则：false
            Map<Long, Boolean> stockMap = null;
            try {
                //因为是远程调用，会存在网络等问题导致远程调用失败，我们需要使用try进行捕获
    
                R<List<SkuHasStockVo>> skusHashStock = wareFeignService.getSkusHashStock(skuIdList);
    //        List<SkuHasStockVo> data = skusHashStock.getData();
    
                //将List转成Map（重要）
                stockMap = skusHashStock.getData().stream().collect(Collectors.toMap(SkuHasStockVo::getSkuId, item -> item.getHasStock()));
    
            } catch (Exception e) {
                log.error("库存服务查询异常：原因{}", e);
            }
    
            //2、封装每个sku的信息
            Map<Long, Boolean> finalStockMap = stockMap;
    
    List<SkuEsModel> upProducts = skus.stream().map(sku -> {
                //组装需要的数据
                SkuEsModel esModel = new SkuEsModel();
                /**
                 * 将当前正在遍历的sku里面的数据，拷贝到esModel中
                 */
                BeanUtils.copyProperties(sku, esModel);
                //对比SkuInfoEntity和SkuEsModel中的字段发现：skuPrice,skuImg,hasStock,hotScore等有些字段名称不一样，或者压根就没有以下字段，需要我们单独处理（查询）
                esModel.setSkuPrice(sku.getPrice());
                esModel.setSkuImg(sku.getSkuDefaultImg());
                **//设置库存信息：hasStock,hotScore
                if (finalStockMap == null) {     //此时说明远程服务有问题： 默认有库存hasStock = true
                    esModel.setHasStock(true);
                } else {
                    esModel.setHasStock(finalStockMap.get(sku.getSkuId()));
                }**
    
                //TODO 2、热度评分(hotScore)。新产品默认置：0 【暂时默认      】
                esModel.setHotScore(0L);
                //TODO 3、查询品牌（brandName）和分类的名字(catalogName)信息。
                BrandEntity brand = brandService.getById(esModel.getBrandId());
                esModel.setBrandName(brand.getName());
                esModel.setBrandImg(brand.getLogo());
    
                CategoryEntity category = categoryService.getById(esModel.getCatalogId());
                esModel.setCatalogName(category.getName());
                //设置检索属性
                esModel.setAttrs(attrsList);
    
                /**
                 *  private String brandName;
                 *  private String brandImg;
                 *  private String catalogName;
                 *  private List<Attr> attrs;
                 *
                 *@Data
                 *public static class Attr {
                 *      private Long attrId;
                 *      private String attrName;
                 *      private String attrValue;
                 *}
                 */
                return esModel;
            }).collect(Collectors.toList());
    
            //TODO 5、将数据发给es进行保存：gulimall-search
    ```
    
- WareSkuDao.java
  
    ```java
    /**
         * 如果 入参只有一个参数的话，我们写什么都可以，如果有多个入参，我们一定要使用@Param("skuId")注解标注每一个参数的属性名
         * 检查库存
         *
         * @param skuId
         * @return
         */
        Long getSkuStock(Long skuId);
    ```
    
- WareSkuService.java
  
    ```java
    List<SkuHasStockVo> getSkusHashStock(List<Long> skuIds);
    ```
    
- WareSkuServiceImpl.java
  
    ```java
    /**
         * 查看是否有库存
         *
         * @param skuIds
         * @return
         */
        @Override
        public List<SkuHasStockVo> getSkusHashStock(List<Long> skuIds) {
            List<SkuHasStockVo> collect = skuIds.stream().map(skuId -> {
                SkuHasStockVo vo = new SkuHasStockVo();
                //查询当前sku的总库存量 (库存数: stock - 锁定库存: stock_locked)
                //SELECT SUM(stock - stock_locked) FROM wms_ware_sku WHERE sku_id =1
                Long count = baseMapper.getSkuStock(skuId);
                vo.setSkuId(skuId);
                vo.setHasStock(count > 0);
                return vo;
            }).collect(Collectors.toList());
            return collect;
        }
    ```
    
- SkuHasStockVo.java
  
    ```java
    package com.atguigu.gulimall.ware.vo;
    
    import lombok.Data;
    
    /**
     * @Author Dali
     * @Date 2021/12/5 11:10
     * @Version 1.0
     * @Description
     */
    @Data
    public class SkuHasStockVo {
        private Long skuId;
        private Boolean hasStock;
    }
    ```
    
- WareSkuDao.xml
  
    ```xml
    <select id="getSkuStock" resultType="java.lang.Long">
            SELECT SUM(stock - stock_locked) FROM wms_ware_sku WHERE sku_id = #{skuId}
    </select>
    ```
    
- WareFeignService.java
  
    ```java
    package com.atguigu.gulimall.product.feign;
    
    import com.atguigu.common.to.SkuHasStockVo;
    import com.atguigu.common.utils.R;
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/5 11:40
     * @Version 1.0
     * @Description: 远程调用库存相关的接口服务
     */
    @FeignClient("gulimall-ware")
    public interface WareFeignService {
    
        /**
         * 1、R设计的时候可以加上泛型
         * 2、直接放回我们想要的结果
         * 3、自己封装返回结果
         *
         * @param skuIds
         * @return
         */
        @PostMapping("/ware/waresku/hasstock")
        R<List<SkuHasStockVo>> getSkusHashStock(@RequestBody List<Long> skuIds);
    }
    ```
    
- SkuHasStockVo.java
  
    ```java
    package com.atguigu.common.to;
    
    import lombok.Data;
    
    /**
     * @Author Dali
     * @Date 2021/12/5 11:10
     * @Version 1.0
     * @Description
     */
    @Data
    public class SkuHasStockVo {
        private Long skuId;
        private Boolean hasStock;
    }
    ```
    
- R
  
    ```java
    public class **R<T>** extends HashMap<String, Object> {
        private static final long serialVersionUID = 1L;
    
        **private T data;
    
        public T getData() {
            return data;
        }
    
        public void setData(T data) {
            this.data = data;
        }
    。。。。。。。。。。。。。。。。。。
    }**
    ```
    

# P133、 商城业务-商品上架远程上架接口

> gulimall-common：
> 
- ProductConstant.java
  
    ```java
    /**
         * 商品上架枚举
         */
        public enum StatusEnum {
            NEW_SPU(0, "新建"), SPU_UP(1, "商品上架"), SPU_DOWN(2, "商品下架");
    
            private int code;
            private String msg;
    
            StatusEnum(int code, String msg) {
                this.code = code;
                this.msg = msg;
            }
    
            public int getCode() {
                return code;
            }
    
            public String getMsg() {
                return msg;
            }
        }
    ```
    
- BizCodeEnume.java
  
    ```java
    PRODUCT_UP_EXCEPTION(11000, "商品上架异常");
    ```
    

> gulimall-product：
> 
- SpuInfoDao.java
  
    ```java
    void updateSpuStatus(@Param("spuId") Long spuId, @Param("code") int code);
    ```
    
- SearchFeignService.java
  
    ```java
    package com.atguigu.gulimall.product.feign;
    
    import com.atguigu.common.to.es.SkuEsModel;
    import com.atguigu.common.utils.R;
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 20:37
     * @Version 1.0
     * @Description
     */
    @FeignClient("gulimall-search")
    public interface SearchFeignService {
        /**
         * 远程调用gulimall-search服务，商品上架：远程接口（供商品服务：gulimall-product调用）
         *
         * @param skuEsModels
         * @return
         */
        @PostMapping("/search/save/product")
        R productStatusUp(@RequestBody List<SkuEsModel> skuEsModels);
    }
    ```
    
- SpuInfoServiceImpl.java
  
    ```java
        @Autowired
        SearchFeignService searchFeignService;  //远程调用
    
    //TODO 5、将数据发给es进行保存：gulimall-search 【远程调用gulimall-search服务】
            R r = searchFeignService.productStatusUp(upProducts);
            if (r.getCode() == 0) {
                //远程调用成功
                //TODO 6、修改当前spu状态
                baseMapper.updateSpuStatus(spuId, ProductConstant.StatusEnum.NEW_SPU.getCode());
            } else {
                //远程调用失败
                //TODO 7、重复调用？？ 接口幂等性：重试机制？？？（重要！！！）
            }
    ```
    
- SpuInfoDao.xml
  
    ```java
    <update id="updateSpuStatus">
            UPDATE pms_spu_info SET publish_status = #{code} ,update_time=NOW() WHERE id = #{spuId}
        </update>
    ```
    

> gulimall-search:
> 
- EsConstant.java
  
    ```java
    package com.atguigu.gulimall.search.constant;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:50
     * @Version 1.0
     * @Description
     */
    public class EsConstant {
        /**
         * sku数据在es中的索引
         */
        public static final String PRODUCT_INDEX = "product";
    }
    ```
    
- ElasticSaveController.java
  
    ```java
    package com.atguigu.gulimall.search.controller;
    
    import com.atguigu.common.exception.BizCodeEnume;
    import com.atguigu.common.to.es.SkuEsModel;
    import com.atguigu.common.utils.R;
    import com.atguigu.gulimall.search.service.ProductSaveService;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:38
     * @Version 1.0
     * @Description
     */
    @Slf4j
    @RequestMapping("/search/save")
    @RestController
    public class ElasticSaveController {
    
        @Autowired
        ProductSaveService productSaveService;
    
        /**
         * 商品上架：远程接口（供商品服务调用）
         *
         * @param skuEsModels
         * @return
         */
        @PostMapping("/product")
        public R productStatusUp(@RequestBody List<SkuEsModel> skuEsModels) {
    
            boolean b = false;
            try {
                b = productSaveService.productStatusUp(skuEsModels);
            } catch (Exception e) {
                log.error("ElasticSaveController商品上架错误：{}", e);
                return R.error(BizCodeEnume.PRODUCT_UP_EXCEPTION.getCode(), BizCodeEnume.PRODUCT_UP_EXCEPTION.getMsg());
            }
            if (b) {
                return R.ok();
            } else {
                return R.error(BizCodeEnume.PRODUCT_UP_EXCEPTION.getCode(), BizCodeEnume.PRODUCT_UP_EXCEPTION.getMsg());
            }
    
        }
    }
    ```
    
- ProductSaveServiceImpl.java
  
    ```java
    package com.atguigu.gulimall.search.service.impl;
    
    import com.alibaba.fastjson.JSON;
    import com.atguigu.common.to.es.SkuEsModel;
    import com.atguigu.gulimall.search.config.GulimallElasticSearchConfig;
    import com.atguigu.gulimall.search.constant.EsConstant;
    import com.atguigu.gulimall.search.service.ProductSaveService;
    import lombok.extern.slf4j.Slf4j;
    import netscape.javascript.JSObject;
    import org.elasticsearch.action.bulk.BulkRequest;
    import org.elasticsearch.action.bulk.BulkResponse;
    import org.elasticsearch.action.index.IndexRequest;
    import org.elasticsearch.client.RestHighLevelClient;
    import org.elasticsearch.common.xcontent.XContentType;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import java.io.IOException;
    import java.util.Arrays;
    import java.util.List;
    import java.util.stream.Collectors;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:45
     * @Version 1.0
     * @Description
     */
    @Service
    @Slf4j
    public class ProductSaveServiceImpl implements ProductSaveService {
        @Autowired
        RestHighLevelClient restHighLevelClient;
    
        @Override
        public boolean productStatusUp(List<SkuEsModel> skuEsModels) throws IOException {
            //保存到ES中
    
            //1、给es建立索引，product
    
            //2、给es中保存这些数据
            //批量保存：bulk方法（adj. 大批的）中有这俩参数BulkRequest bulkRequest, RequestOptions options
            BulkRequest bulkRequest = new BulkRequest();
            for (SkuEsModel model : skuEsModels) {
                //1、构造保存请求
                IndexRequest indexRequest = new IndexRequest(EsConstant.PRODUCT_INDEX);
                //使用toString()将Long转成String
                indexRequest.id(model.getSkuId().toString());
                //将对象转成JSON
                String s = JSON.toJSONString(model);
                indexRequest.source(s, XContentType.JSON);
    
                bulkRequest.add(indexRequest);
            }
    
            //执行批量操作
            BulkResponse bulk = restHighLevelClient.bulk(bulkRequest, GulimallElasticSearchConfig.COMMON_OPTIONS);
    
            //统计批量操作失败的情况
            //TODO 1、如果批量错误，先将错误信息日志打印
            boolean b = bulk.hasFailures();
            List<String> collect = Arrays.stream(bulk.getItems()).map(item -> {
                return item.getId();
            }).collect(Collectors.toList());
            log.error("商品上架错误：{}", collect);
    
            return b;
        }
    }
    ```
    
- ProductSaveService.java
  
    ```java
    package com.atguigu.gulimall.search.service;
    
    import com.atguigu.common.to.es.SkuEsModel;
    
    import java.io.IOException;
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:43
     * @Version 1.0
     * @Description
     */
    public interface ProductSaveService {
    
        boolean productStatusUp(List<SkuEsModel> skuEsModels) throws IOException;
    }
    ```
    
- product-mapping.txt
  
    ```java
    //映射关系
    PUT product
    {
        "mappings":{
            "properties": {
                "skuId":{ "type": "long" },
                "spuId":{ "type": "keyword" },  # 不可分词
                "skuTitle": {
                    "type": "text",
                    "analyzer": "ik_smart"  # 中文分词器
                },
                "skuPrice": { "type": "keyword" },  # 保证精度问题
                "skuImg"  : { "type": "keyword" },  # 视频中有false
                "saleCount":{ "type":"long" },
                "hasStock": { "type": "boolean" },
                "hotScore": { "type": "long"  },
                "brandId":  { "type": "long" },
                "catalogId": { "type": "long"  },
                "brandName": {"type": "keyword"}, # 视频中有false
                "brandImg":{
                    "type": "keyword",
                    "index": false,  # 不可被检索，不生成index，只用做页面使用
                    "doc_values": false # 不可被聚合，默认为true
                },
                "catalogName": {"type": "keyword" }, # 视频里有false
                "attrs": {
                    "type": "nested",
                    "properties": {
                        "attrId": {"type": "long"  },
                        "attrName": {
                            "type": "keyword",
                            "index": false,
                            "doc_values": false
                        },
                        "attrValue": {"type": "keyword" }
                    }
                }
            }
        }
    }
    ```
    

# P134、商城业务-商品上架-上架接口调试&feign源码

项目启动：

1. 启动虚拟机
2. 启动nacos
3. npm run dev 启动前端页面
4. 启动idea各个微服务模块

## 统一配置：一次启动多个微服务

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

修改gulimall-search微服务的application.properties端口号

```java
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
spring.application.name=gulimall-search
server.port=12000
```

## 功能展示

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

## Feign的源码解析：

SpuInfoServiceImpl.java

```
//Feign流程调用（含与那么解析）：https://www.bilibili.com/video/BV1np4y1C7Yf?p=134&spm_id_from=pageDriver
/**
 * 1、构造请求数据，将对象转成json：
*          RequestTemplate template = bui LdTemplateFromArgs. create (argv);
 * 2、发送请求进行执行(执行成功会解码响应数据)：
*          executeAndDecode ( template);
 * 3、执行请求会有重试机制
*      while(true){
 *         try{
 *            executeAndDecode(template);
 *             }catch {
 *            try{retryer.continueOrPropagate(e);}catch() {throw es;}
 *                  continue;
 *         }
 *      }
 */
```

ProductSaveServiceImpl.java

```java
log.info("商品上架完成：{}", collect);
```

application.properties

```java
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
spring.application.name=gulimall-search
server.port=12000
```

WareSkuServiceImpl.java

进行非空判断：防止空指针异常

```java
vo.setHasStock(count == null ? false : count > 0);
```

# P135、 商城业务-商品上架-抽取响应结果&上架测试完成

功能图：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

## 本例代码：

针对之前我们修改过的返回响应头R，由于通过JSON传输过程中，丢失数据，原来的我们自己添加的data的在传输过程中，不会被保存。

- R
  
    ```java
    package com.atguigu.common.utils;
    
    import com.alibaba.fastjson.JSON;
    import com.alibaba.fastjson.TypeReference;
    import org.apache.http.HttpStatus;
    .......
    import java.util.Map;
    
    /**
     * 返回数据
     *
     * @author Mark sunlightcs@gmail.com
     */
    public class R extends HashMap<String, Object> {
        private static final long serialVersionUID = 1L;
    -- 这是原来的写法
    //    private T data;
    //
    //    public T getData() {
    //        return data;
    //    }
    //
    //    public void setData(T data) {
    //        this.data = data;
    //    }
    
        /**
         * 利用alibaba提供的fastjson进行类型转换
         *
         * @param typeReference
         * @param <T>
         * @return
         */
        **public <T> T getData(TypeReference<T> typeReference) {
            Object data = get("data");  //此处get("data")默认是map类型
            String s = JSON.toJSONString(data);    //将Object转成String
            T t = JSON.parseObject(s, typeReference);     //将文本String逆转成T类型
            return t;
        }
    
        public R setData(Object data) {
            put("data", data);
            return this;
        }
    
    ......**
    ```
    

父类实现序列化，子类就不需要实现序列化（也相当于实现了序列化），这里存不进去值是因为R的保存属性是一个复杂类型，这个类型（SkuHasStockVo）没有实现序列化接口。

这里存不进去值应该是HashMap实现了序列化接口，所以put的数据可以在远程调用的时候携带着，反序列化得到，R没有实现序列化接口，所以R的属性在远程传输的时候就丢失了。

将java对象通过字节流传输才是序列化，这是通过反射进行的json拼串是变成的字符流，哪里是序列化。

- WareFeignService.java
  
    ```java
    package com.atguigu.gulimall.product.feign;
    
    ......
    /**
     * @Author Dali
     * @Date 2021/12/5 11:40
     * @Version 1.0
     * @Description: 远程调用库存相关的接口服务
     */
    @FeignClient("gulimall-ware")
    public interface WareFeignService {
    
        /**
         * 1、R设计的时候可以加上泛型
         * 2、直接放回我们想要的结果
         * 3、自己封装返回结果
         *
         * @param skuIds
         * @return
         */
        @PostMapping("/ware/waresku/hasstock")
        **R getSkusHashStock(@RequestBody List<Long> skuIds);**
    }
    ```
    

修改typeReference后stream爆红是因为R类上面还有R<T>。

```java
public class R extends HashMap<String, Object> {
```

`new TypeReference<List<SkuHasStockVo>>(){}`，为什么加花括号？是因为 那个类的构造方法是用**`protected`**修饰的，生成的话既不是本类也不是子类 所以需要用匿名类的方式 这样就可用new了。

直接强转是不行的,接收到的Object类型里面的对象被自动反序列化成Map了,所以只能转JSON接受。

- SpuInfoServiceImpl.java
  
    ```java
    //hasStock,hotScore
            //TODO 1、发送远程调用，库存系统查询是否含有库存（hasStock）如果有：true,否则：false
            Map<Long, Boolean> stockMap = null;
            try {
                //因为是远程调用，会存在网络等问题导致远程调用失败，我们需要使用try进行捕获
    
                **R skusHashStock = wareFeignService.getSkusHashStock(skuIdList);**
    //        List<SkuHasStockVo> data = skusHashStock.getData();
                **TypeReference<List<SkuHasStockVo>> typeReference = new TypeReference<List<SkuHasStockVo>>() {**
                };
                //将List转成Map（重要）
                **stockMap = skusHashStock.getData(typeReference).stream().collect(Collectors.toMap(SkuHasStockVo::getSkuId, item -> item.getHasStock()));**
    
            } catch (Exception e) {
                log.error("库存服务查询异常：原因{}", e);
            }
    
    .......
    
    -- 修改SPU_UP
    
    //TODO 5、将数据发给es进行保存：gulimall-search 【远程调用gulimall-search服务】
            R r = searchFeignService.productStatusUp(upProducts);
            if (r.getCode() == 0) {
                //远程调用成功
                //TODO 6、修改当前spu状态
                **baseMapper.updateSpuStatus(spuId, ProductConstant.StatusEnum.SPU_UP.getCode());**
            } else {
    
    .......
    ```
    
- ElasticSaveController.java
  
    ```java
    package com.atguigu.gulimall.search.controller;
    
    ....
    
    import java.util.List;
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:38
     * @Version 1.0
     * @Description
     */
    @Slf4j
    @RequestMapping("/search/save")
    @RestController
    public class ElasticSaveController {
    
        @Autowired
        ProductSaveService productSaveService;
    
        /**
         * 商品上架：远程接口（供商品服务调用）
         *
         * @param skuEsModels
         * @return
         */
        @PostMapping("/product")
        public R productStatusUp(@RequestBody List<SkuEsModel> skuEsModels) {
    
            boolean b = false;
            try {
                b = productSaveService.productStatusUp(skuEsModels);
            } catch (Exception e) {
                log.error("ElasticSaveController商品上架错误：{}", e);
                return R.error(BizCodeEnume.PRODUCT_UP_EXCEPTION.getCode(), BizCodeEnume.PRODUCT_UP_EXCEPTION.getMsg());
            }
            //如果是false 我们就返回R.ok();
            **if (!b) {**
                return R.ok();
            } else {
                return R.error(BizCodeEnume.PRODUCT_UP_EXCEPTION.getCode(), BizCodeEnume.PRODUCT_UP_EXCEPTION.getMsg());
            }
    
        }
    }
    ```
    
- ProductSaveServiceImpl.java
  
    ```java
    package com.atguigu.gulimall.search.service.impl;
    
    ........
    
    /**
     * @Author Dali
     * @Date 2021/12/7 19:45
     * @Version 1.0
     * @Description
     */
    @Service
    @Slf4j
    public class ProductSaveServiceImpl implements ProductSaveService {
        @Autowired
        RestHighLevelClient restHighLevelClient;
    
        @Override
        public boolean productStatusUp(List<SkuEsModel> skuEsModels) throws IOException {
            //保存到ES中
    
            //1、给es建立索引，product
    
            //2、给es中保存这些数据
            //批量保存：bulk方法（adj. 大批的）中有这俩参数BulkRequest bulkRequest, RequestOptions options
            BulkRequest bulkRequest = new BulkRequest();
            for (SkuEsModel model : skuEsModels) {
                //1、构造保存请求
                IndexRequest indexRequest = new IndexRequest(EsConstant.PRODUCT_INDEX);
                //使用toString()将Long转成String
                indexRequest.id(model.getSkuId().toString());
                //将对象转成JSON
                String s = JSON.toJSONString(model);
                indexRequest.source(s, XContentType.JSON);
    
                bulkRequest.add(indexRequest);
            }
    
            //执行批量操作
            BulkResponse bulk = restHighLevelClient.bulk(bulkRequest, GulimallElasticSearchConfig.COMMON_OPTIONS);
    
            //统计批量操作失败的情况
            //TODO 1、如果批量错误，先将错误信息日志打印
            boolean b = bulk.hasFailures();
            List<String> collect = Arrays.stream(bulk.getItems()).map(item -> {
                return item.getId();
            }).collect(Collectors.toList());
            **log.info("商品上架完成：{}， 返回数据:{}", collect, bulk.toString());**
    
            return b;
        }
    }
    ```
    
- WareSkuController.java
  
    ```java
    /**
         * 查询是否有库存
         * sku的规格参数相同，因此我们要将查询规格参数提前，只查询一次
         *
         * @RequestBody: 将请求体中的json数据转成List<Long>集合
         */
        @PostMapping("/hasstock")
        **public R getSkusHashStock(@RequestBody List<Long> skuIds) {**
    //    public R<List<SkuHasStockVo>> getSkusHashStock(@RequestBody List<Long> skuIds) {
    
            //表：wms_ware_sku 只需要返回当前sku_id和当前的库存量stock只要大于0就是有库存
            **List<SkuHasStockVo> vos = wareSkuService.getSkusHashStock(skuIds);**
    
            /*R<List<SkuHasStockVo>> ok = R.ok();
            ok.setData(vos);*/
            //return R.ok().put("data", vos);
            **return R.ok().setData(vos);**
        }
    ```
    

## kibana测试

[](http://192.168.56.10:5601/app/kibana#/dev_tools/console?_g=())

```bash
kibana中执行以下查询命令

GET product/_search
```

[](http://192.168.56.10:9200/)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)