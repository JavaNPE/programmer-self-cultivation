# 10_商品管理-SPU&仓储服务-仓库管理整合&分布式基础篇总结 &注册中心Nacos使用【P1-P101】

# P93、商品服务-API-商品管理-SPU检索

功能页：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209365.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209366.png)

## spu参考接口文档地址：

[18、spu检索](https://easydoc.net/s/78237135/ZUqEdvA4/9LISLvy7)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209368.png)

## 数据库表：pms_spu_info

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209369.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209370.png)

前端改成一样的

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209371.png)

按此条件进行查询：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209372.png)

控制台打印：

```sql
==>  Preparing: SELECT id,spu_description,spu_name,catalog_id,create_time,brand_id,weight,update_time,publish_status FROM pms_spu_info WHERE (( (id = ? OR spu_name LIKE ?) ) AND publish_status = ? AND brand_id = ? AND catalog_id = ?) LIMIT ?,?
==> Parameters: 华为(String), %华为%(String), 0(String), 2(String), 225(String), 0(Long), 10(Long)
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209373.png)

## 修改返回的时间戳格式问题：通过**`jackson` 统一修改时间格式**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209374.png)

指定时间格式：

```yaml
jackson:
    date-format: yyyy-MM-dd HH:mm:ss
```

在配置文件中添加这个配置，指定之间格式，重启gulimall-pruduct服务重新测试

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209375.png)

重新查询试一下：返回正常格式的时间

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209376.png)

# P94、商品服务API-商品管理SKU检索

菜单页面：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209377.png)

## 接口文档：21、SKU检索

[21、sku检索](https://easydoc.net/s/78237135/ZUqEdvA4/ucirLq1D)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209378.png)

```yaml
http://localhost:88/api/product/skuinfo/list?t=1636882465490&page=2&limit=10&key=&catelogId=0&brandId=0&min=0&max=0
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209379.png)

## Mybatis-Plus eq、ne、gt、lt、ge、le分别代表含义

```
eq 就是 equal等于
ne就是 not equal不等
gt 就是 greater than大于
lt 就是 less than小于
ge 就是 greater than or equal 大于等于
le 就是 less than or equal 小于等于
in 就是 in 包含（数组）
isNull 就是 等于null
between 就是 在2个条件之间(包括边界值)
like就是 模糊查询

```

## 代码：

SkuInfoController：

```java
/**
 * 列表：根据检索条件进行分页查询
 */
@RequestMapping("/list")
//@RequiresPermissions("product:skuinfo:list")
public R list(@RequestParam Map<String, Object> params){
    //PageUtils page = skuInfoService.queryPage(params);
    PageUtils page = skuInfoService.queryPageByCondition(params);
    return R.ok().put("page", page);
}
```

SkuInfoServiceImpl：

```java
		/**
     * http://localhost:88/api/product/skuinfo/list?t=1636882465490&page=2&limit=10&key=&catelogId=0&brandId=0&min=0&max=0
     *
     * @param params
     * @return
     */
    @Override
    public PageUtils queryPageByCondition(Map<String, Object> params) {
        QueryWrapper<SkuInfoEntity> queryWrapper = new QueryWrapper<>();
        /**
         * key:
         * catelogId: 0
         * brandId: 0
         * min: 0
         * max: 0
         */

        String key = (String) params.get("key");
        if (!StringUtils.isEmpty(key)) {
            // sku_id=1 and (sku_id=1 or sku_name like xxx),因为上面用了and()的方式拼接，拼接的条件在and()括号里面。
            // 若是使用and()进行拼接的话catelog_id x=1 and sku_id=1 or sku_name like xxx 会有恒成立的问题
            //==>  Preparing: SELECT * FROM pms_sku_info WHERE (catalog_id = ? AND brand_id = ? AND price >= ? AND price <= ?) LIMIT ?,?
            
            //==>  Preparing: SELECT * FROM pms_sku_info WHERE (( (sku_id = ? OR sku_name LIKE ?) ) AND catalog_id = ? AND brand_id = ? AND price >= ? AND price <= ?) LIMIT ?,?
						queryWrapper.and((wapper) -> {
                wapper.eq("sku_id", key).or().like("sku_name", key);
            });
        }
        String catelogId = (String) params.get("catelogId");
        if (!StringUtils.isEmpty(catelogId) && !"0".equalsIgnoreCase(catelogId)) {
            queryWrapper.eq("catalog_id", catelogId);
        }
        String brandId = (String) params.get("brandId");
        if (!StringUtils.isEmpty(brandId) && !"0".equalsIgnoreCase(catelogId)) {
            queryWrapper.eq("brand_id", brandId);
        }
        String min = (String) params.get("min");
        if (!StringUtils.isEmpty(min)) {
            //ge 就是 greater than or equal 大于等于
            queryWrapper.ge("price", min);
        }
        String max = (String) params.get("max");
        if (!StringUtils.isEmpty(max)) {
            try {
                BigDecimal bigDecimal = new BigDecimal(max);
                //max的值与0比较 如果==1则表示fullPrice的值比0大
                if (bigDecimal.compareTo(new BigDecimal("0")) == 1) {
                    //le 就是 less than or equal 小于等于
                    queryWrapper.le("price", max);
                }
            } catch (Exception e) {

            }

        }

        IPage<SkuInfoEntity> page = this.page(
                new Query<SkuInfoEntity>().getPage(params),
                queryWrapper
        );

        return new PageUtils(page);
    }

}
```

SpuInfoServiceImpl：

```java
String brandId = (String) params.get("brandId");
if(!StringUtils.isEmpty(brandId) && !"0".equalsIgnoreCase(brandId)) {
     wrapper.eq("brand_id", brandId);
}

String catelogId = (String) params.get("catelogId");
if(!StringUtils.isEmpty(key) && !"0".equalsIgnoreCase(catelogId)) {
     wrapper.eq("catalog_id", catelogId);
}
```

SkuInfoService：

```java
PageUtils queryPageByCondition(Map<String, Object> params);
```

# P95、仓储服务-API-仓库管理整合ware服务&获取仓库列表

1. 把gulimall-ware微服务添加到注册中心中
2. 配置网关的路由规则

涉及到的数据库表：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209380.png)

## 1、把gulimall-ware微服务添加到注册中心中

```yaml
server:
  port: 11000

spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_wms
    driver-class-name: com.mysql.jdbc.Driver
**# 将该服务注册到nacos中
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
# 指定项目的名字
  application:
    name: gulimall-ware**
# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto
```

在启动类中开启服务注册与发现功能：

```java
package com.atguigu.gulimall.ware;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.transaction.annotation.EnableTransactionManagement;

**@EnableTransactionManagement    //开启事务
@MapperScan("com.atguigu.gulimall.ware.dao")  //包扫描
@EnableDiscoveryClient  //服务注册与发现**
@SpringBootApplication
public class GulimallWareApplication {

    public static void main(String[] args) {
        SpringApplication.run(GulimallWareApplication.class, args);
    }

}
```

启动gulimall-ware服务，然后访问：[http://192.168.56.103:8848/nacos/index.htm](http://192.168.56.103:8848/nacos/index.htm)l

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209381.png)

发现gulimall-ware服务已经注册上来了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209382.png)

### 给gulimall-ware服务添加批量启动

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209383.png)

修改占用内存大小：**-Xmx100m**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209384.png)

前端工程添加ware相关代码：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209385.png)

### 重启VsCode：`npm run dev`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209386.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209387.png)

## 2、配置网关的路由规则

```yaml
- id: ware_route
  uri: lb://gulimall-ware
  predicates:
    - Path=/api/ware/**     #只要是/api/ware 这些前缀的 就自动路由给第三方服务gulimall-third-party
  filters:
    - RewritePath=/api/(?<segment>/?.*),/$\{segment}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209388.png)

重启guliamll-geteway和guliamll-ware服务，重新响应库存维护功能：如下图

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209389.png)

## 配置文件开启：idea控制台打印SQL语句

D:\develop\workspace\gulimall\gulimall-ware\src\main\resources\application.yml

application.yml

```yaml
#idea控制台打印SQL语句
logging:
  level:
    com.atguigu: debug
```

## 本节代码：

```java
package com.atguigu.gulimall.ware.service.impl;

import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Map;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.atguigu.common.utils.PageUtils;
import com.atguigu.common.utils.Query;

import com.atguigu.gulimall.ware.dao.WareInfoDao;
import com.atguigu.gulimall.ware.entity.WareInfoEntity;
import com.atguigu.gulimall.ware.service.WareInfoService;

@Service("wareInfoService")
public class WareInfoServiceImpl extends ServiceImpl<WareInfoDao, WareInfoEntity> implements WareInfoService {

    @Override
    public PageUtils queryPage(Map<String, Object> params) {
//==>Preparing: SELECT * FROM wms_ware_info WHERE (id = ? OR name LIKE ? OR address LIKE ? OR areacode LIKE ?)
        QueryWrapper<WareInfoEntity> wareInfoEntityQueryWrapper = new QueryWrapper<>();
        String key = (String) params.get("key");
        if (!StringUtils.isEmpty(key)) {
            wareInfoEntityQueryWrapper.eq("id", key)
                    .or().like("name", key)
                    .or().like("address", key)
                    .or().like("areacode", key);
        }
        IPage<WareInfoEntity> page = this.page(
                new Query<WareInfoEntity>().getPage(params),
//                new QueryWrapper<WareInfoEntity>()
                wareInfoEntityQueryWrapper
        );

        return new PageUtils(page);
    }

}
```

# P96、仓储服务-API-仓库管理查询库存&创建采购需求

## 商品库存接口：功能页面

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209390.png)

## 02、查询商品库存-接口文档地址

[02、查询商品库存](https://easydoc.net/s/78237135/ZUqEdvA4/hwXrEXBZ)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209391.png)

浏览器控制台：请求路径

```java
http://localhost:88/api/ware/waresku/list?t=1636899483747&page=1&limit=10&skuId=&wareId=
http://localhost:88/api/ware/waresku/list?t=1636899643856&page=1&limit=10&skuId=1&wareId=1
```

浏览器控制台：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209392.png)

## 对应的数据库表：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209393.png)

### 查询商品库存接口代码：

```java
package com.atguigu.gulimall.ware.service.impl;

import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Map;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.atguigu.common.utils.PageUtils;
import com.atguigu.common.utils.Query;

import com.atguigu.gulimall.ware.dao.WareSkuDao;
import com.atguigu.gulimall.ware.entity.WareSkuEntity;
import com.atguigu.gulimall.ware.service.WareSkuService;

@Service("wareSkuService")
public class WareSkuServiceImpl extends ServiceImpl<WareSkuDao, WareSkuEntity> implements WareSkuService {

    @Override
    public PageUtils queryPage(Map<String, Object> params) {
        /**
         * skuId: 1   sku_id
         * wareId: 1  仓库id
         */
        // ==>  Preparing: SELECT * FROM wms_ware_sku WHERE (sku_id = ? AND ware_id = ?)
        QueryWrapper<WareSkuEntity> queryWrapper = new QueryWrapper<>();
        String skuId = (String) params.get("skuId");
        if (!StringUtils.isEmpty(skuId)) {
            queryWrapper.eq("sku_id", skuId);
        }

        String wareId = (String) params.get("wareId");
        if (!StringUtils.isEmpty(wareId)) {
            queryWrapper.eq("ware_id", wareId);
        }

        IPage<WareSkuEntity> page = this.page(
                new Query<WareSkuEntity>().getPage(params),
//                new QueryWrapper<WareSkuEntity>()
                queryWrapper
        );

        return new PageUtils(page);
    }

}
```

## 采购需求-接口

```java
http://localhost:88/api/ware/purchasedetail/list?t=1636902667695&page=1&limit=10&key=&status=1&wareId=1
```

### 03、查询采购需求接口文档

[03、查询采购需求](https://easydoc.net/s/78237135/ZUqEdvA4/Ss4zsV7R)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209394.png)

涉及到的数据库表：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209395.png)

### 采购需求查询代码

```java
package com.atguigu.gulimall.ware.service.impl;

import com.baomidou.mybatisplus.core.conditions.Wrapper;
import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Service;

import java.util.Map;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.atguigu.common.utils.PageUtils;
import com.atguigu.common.utils.Query;

import com.atguigu.gulimall.ware.dao.PurchaseDetailDao;
import com.atguigu.gulimall.ware.entity.PurchaseDetailEntity;
import com.atguigu.gulimall.ware.service.PurchaseDetailService;

@Service("purchaseDetailService")
public class PurchaseDetailServiceImpl extends ServiceImpl<PurchaseDetailDao, PurchaseDetailEntity> implements PurchaseDetailService {

    @Override
    public PageUtils queryPage(Map<String, Object> params) {
        /**
         *  status: 0,//状态
         *  wareId: 1,//仓库id
         */
        //Request URL: http://localhost:88/api/ware/purchasedetail/list?t=1636903632642&page=1&limit=10&key=1&status=3&wareId=1
        //Preparing: SELECT * FROM wms_purchase_detail WHERE (( (purchase_id = ? OR sku_id = ?) ) AND status = ? AND ware_id = ?)
        //                                                    ==> Parameters: 1(String),    1(String),        3(String),      1(String)
        QueryWrapper<PurchaseDetailEntity> queryWrapper = new QueryWrapper<PurchaseDetailEntity>();
        String key = (String) params.get("key");
        if (!StringUtils.isEmpty(key)) {
            queryWrapper.and(w -> {
                w.eq("purchase_id", key).or().eq("sku_id", key);
            });
        }
        String status = (String) params.get("status");
        if (!StringUtils.isEmpty(status)) {
            queryWrapper.eq("status", status);
        }
        String wareId = (String) params.get("wareId");
        if (!StringUtils.isEmpty(wareId)) {
            queryWrapper.eq("ware_id", wareId);
        }

        IPage<PurchaseDetailEntity> page = this.page(
                new Query<PurchaseDetailEntity>().getPage(params),
//                new QueryWrapper<PurchaseDetailEntity>()
                queryWrapper
        );

        return new PageUtils(page);
    }
}
```

### 采购需求查询：结果图

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209396.png)

# P97、仓储服务-API-仓库管理-合并采购需求

功能介绍：[http://localhost:8001/#/ware-purchaseitem](http://localhost:8001/#/ware-purchaseitem)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209397.png)

## 采购简要流程：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209398.png)

## 05、查询未领取的采购单：接口文档地址

[05、查询未领取的采购单](https://easydoc.net/s/78237135/ZUqEdvA4/hI12DNrH)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209399.png)

合并采购单：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209400.png)

## 涉及到的数据库表：gulimall-wms

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209401.png)

PurchaseController：

```java
/**
     * 05、查询未领取的采购单：ware/purchase/unreceive/list
     * 合并整单时：查询采购单信息
     *
     * @param params
     * @return
     */
    @RequestMapping("/unreceive/list")
    //@RequiresPermissions("ware:purchase:list")
    public R unreceiveList(@RequestParam Map<String, Object> params) {
        PageUtils page = purchaseService.queryPageUnreceivePurchase(params);

        return R.ok().put("page", page);
    }
```

PurchaseServiceImpl：

```java
@Override
    public PageUtils queryPageUnreceivePurchase(Map<String, Object> params) {
        IPage<PurchaseEntity> page = this.page(
                new Query<PurchaseEntity>().getPage(params),
                new QueryWrapper<PurchaseEntity>().eq("status", 0).or().eq("status", 1)
        );

        return new PageUtils(page);
    }
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209402.png)

### 04、合并采购需求：

[04、合并采购需求](https://easydoc.net/s/78237135/ZUqEdvA4/cUlv9QvK)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209403.png)

PurchaseController：

```java
//04、合并采购需求：ware/purchase/merge
@PostMapping("/merge")
public R merge(@RequestBody MergeVo mergeVo) {
    purchaseService.mergePurchase(mergeVo);
    return R.ok();
}
```

PurchaseServiceImpl

```java
@Autowired
PurchaseDetailService detailService;

@Transactional
@Override
public void mergePurchase(MergeVo mergeVo) {
        Long purchaseId = mergeVo.getPurchaseId();
        if (purchaseId == null) {
            //如果purchaseId == null 没有id,就新建一个
            PurchaseEntity purchaseEntity = new PurchaseEntity();

            purchaseEntity.setStatus(WareConstant.PurchaseStatusEnum.CREATED.getCode());
            purchaseEntity.setCreateTime(new Date());
            purchaseEntity.setUpdateTime(new Date());
            this.save(purchaseEntity);
            purchaseId = purchaseEntity.getId();
        }
        List<Long> items = mergeVo.getItems();
        Long finalPurchaseId = purchaseId;

        List<PurchaseDetailEntity> collect = items.stream().map(i -> {
            PurchaseDetailEntity detailEntity = new PurchaseDetailEntity();
            detailEntity.setId(i);
            detailEntity.setPurchaseId(finalPurchaseId);
            detailEntity.setStatus(WareConstant.PurchaseDetailStatusEnum.ASSIGNED.getCode());

            return detailEntity;
        }).collect(Collectors.toList());
        //==>  Preparing: UPDATE wms_purchase_detail SET purchase_id=?, status=? WHERE id=?
        //==> Parameters: 5(Long), 1(Integer), 7(Long)
        detailService.updateBatchById(collect);

        PurchaseEntity purchaseEntity = new PurchaseEntity();
        purchaseEntity.setId(purchaseId);
        purchaseEntity.setCreateTime(new Date());
        this.updateById(purchaseEntity);
    }
```

MergeVo:

```java
package com.atguigu.gulimall.ware;

import lombok.Data;

import java.util.List;

/**
 * @Author Dali
 * @Date 2021/11/15 20:23
 * @Version 1.0
 * @Description
 */
@Data
public class MergeVo {
    private Long purchaseId;     //:1, //整单id
    private List<Long> items;     //:[1,2,3,4] //合并项集合
}
```

WareConstant：

```java
package com.atguigu.common.constant;

/**
 * @Author Dali
 * @Date 2021/11/15 20:32
 * @Version 1.0
 * @Description
 */
public class WareConstant {
    public enum PurchaseStatusEnum {
        CREATED(0, "新建"), ASSIGNED(1, "已分配"),
        RECEIVE(2, "已领取"), FINISH(3, "已完成"),
        HASHERROR(4, "有异常");

        private int code;
        private String msg;

        PurchaseStatusEnum(int code, String msg) {
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

    public enum PurchaseDetailStatusEnum {
        CREATED(0, "新建"), ASSIGNED(1, "已分配"),
        BUYING(2, "正在采购"), FINISH(3, "已完成"),
        HASHERROR(4, "采购失败");

        private int code;
        private String msg;

        PurchaseDetailStatusEnum(int code, String msg) {
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
}
```

application.yml，统一时间格式yyyy-MM-dd HH:mm:ss

```yaml
# 指定时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
```

# P98、仓储服务-API-仓库管理-领取采购单

## 采购流程：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209404.png)

## 06、领取采购单接口

[06、领取采购单](https://easydoc.net/doc/75716633/ZUqEdvA4/vXMBBgw1)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209405.png)

PurchaseController：

```java
		/**
     * 06、领取采购单: /ware/purchase/received
     *
     * @param ids
     * @return
     */
    @PostMapping("/received")
    public R received(@RequestBody List<Long> ids) {
        purchaseService.received(ids);
        return R.ok();
    }
```

PurchaseServiceImpl：

```java
		/**
     * @param ids 采购单id
     */
    @Override
    public void received(List<Long> ids) {
        //1、确认当前采购单是新建或者已分配状态
        List<PurchaseEntity> collect = ids.stream().map(id -> {
            //通过id查询采购信息
            PurchaseEntity byId = this.getById(id);
            return byId;
        }).filter(item -> {
            if (item.getStatus() == WareConstant.PurchaseStatusEnum.CREATED.getCode() ||
                    item.getStatus() == WareConstant.PurchaseStatusEnum.ASSIGNED.getCode()) {
                return true;
            }
            return false;
        }).map(item -> {
            item.setStatus(WareConstant.PurchaseStatusEnum.RECEIVE.getCode());
            item.setUpdateTime(new Date());
            return item;
        }).collect(Collectors.toList());
        //2、改变采购单的状态
        this.updateBatchById(collect);

        //3、改变采购项的状态
        collect.forEach((item) -> {
            List<PurchaseDetailEntity> entities = detailService.listDetailByPurchaseId(item.getId());

            List<PurchaseDetailEntity> detailEntities = entities.stream().map(entity -> {
                PurchaseDetailEntity entity1 = new PurchaseDetailEntity();
                entity1.setId(entity.getId());
                entity1.setStatus(WareConstant.PurchaseDetailStatusEnum.BUYING.getCode());
                return entity1;
            }).collect(Collectors.toList());
            detailService.updateBatchById(detailEntities);
        });
    }
```

### 使用Postman模拟接口发送请求：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209406.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209407.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209408.png)

# P99、仓储服务-API-仓库管理完成采购

## 07、完成采购：接口文档地址

[07、完成采购](https://easydoc.net/doc/75716633/ZUqEdvA4/cTQHGXbK)

```java
POST: /ware/purchase/done

请求参数：
{
   id: 123,//采购单id
   items: [{itemId:1,status:4,reason:""}]//完成/失败的需求详情
}

响应数据：

{
	"msg": "success",
	"code": 0
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209409.png)

## 功能介绍：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209410.png)

## 🙆🏼‍♂️对象格式|Postman参照（重要）封装格式

### 1、接口文档上的请求参数

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209411.png)

### 2、Java代码封装之后的

![图一：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209412.png)

图一：

![图二：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209413.png)

图二：

![PurchaseController：](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209414.png)

PurchaseController：

代码：PurchaseDoneVo

```java
package com.atguigu.gulimall.ware.vo;

import lombok.Data;

import javax.validation.constraints.NotNull;
import java.util.List;

/**
 * @Author Dali
 * @Date 2021/11/18 19:59
 * @Version 1.0
 * @Description: 07、完成采购-接口：/ware/purchase/done
 * <p>
 * 接口文档地址：https://easydoc.net/doc/75716633/ZUqEdvA4/cTQHGXbK
 */
@Data
public class PurchaseDoneVo {
    @NotNull
    private Long id;        //采购单id

    /**
     * items: [{itemId:1,status:4,reason:""}]//完成/失败的需求详情
     */
    private List<PurchaseItemDoneVo> items;
}
```

PurchaseItemDoneVo

```java
package com.atguigu.gulimall.ware.vo;

import lombok.Data;

/**
 * @Author Dali
 * @Date 2021/11/18 20:01
 * @Version 1.0
 * @Description
 */
@Data
public class PurchaseItemDoneVo {
    //itemId:1,status:4,reason:""
    private Long itemId;
    private Integer status;
    private String reason;
}
```

PurchaseController：

```java
    /**
     * 07、完成采购-接口: https://easydoc.net/doc/75716633/ZUqEdvA4/cTQHGXbK
     * <p>
     * ware/purchase/done
     *
     * @param doneVo
     * @return
     */
    @PostMapping("/done")
    public R finish(@RequestBody PurchaseDoneVo doneVo) {
        purchaseService.done(doneVo);
        return R.ok();
    }
```

### 3、postman中的格式

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209415.png)

[http://localhost:88/api/ware/purchase/done](http://localhost:88/api/ware/purchase/done)

status状态[0新建，1已分配，2正在采购，3已完成，4采购失败]

```json
{
    "id": 3,
    "items": [
        {
            "itemId": 7,
            "status": 3,
            "reason": ""
        },
        {
            "itemId": 8,
            "status": 4,
            "reason": "无货"
        }
    ]
}
```

## mybatix插件Generate new Statement | 批量生成@Param  别名

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209416.png)

### MyBatis-Plus SQL语句的正确写法：**是使用` `而不是单引号' '**

1、

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209417.png)

2、

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209418.png)

3、

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209420.png)

4、

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209421.png)

5、

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209422.png)

领取采购单：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209423.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209424.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209425.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209426.png)

状态[0新建，1已分配，2正在采购，**3已完成**，4采购失败]

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209427.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209428.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209429.png)

## 本期代码：

### mybatis分页代码

WareMybatisConfig.java

```java
package com.atguigu.gulimall.ware.config;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * @Author Dali
 * @Date 2021/11/18 22:27
 * @Version 1.0
 * @Description
 */
@EnableTransactionManagement    //开启事务
@MapperScan("com.atguigu.gulimall.ware.dao")
@Configuration
public class WareMybatisConfig {
    /**
     * mybatis的分页插件，可参照gulimall-product
     */
    //引入分页插件
    // 旧版
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        paginationInterceptor.setOverflow(true);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        paginationInterceptor.setLimit(1000);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

PurchaseController.java

```java
    /**
     * 07、完成采购-接口: https://easydoc.net/doc/75716633/ZUqEdvA4/cTQHGXbK
     * <p>
     * ware/purchase/done
     *
     * @param doneVo
     * @return
     */
    @PostMapping("/done")
    public R finish(@RequestBody PurchaseDoneVo doneVo) {
        purchaseService.done(doneVo);
        return R.ok();
    }
```

PurchaseService.java

```java
void done(PurchaseDoneVo doneVo);
```

PurchaseServiceImpl.java

```java
    @Autowired
    WareSkuService wareSkuService;

    @Transactional
    @Override
    public void done(PurchaseDoneVo doneVo) {
        //1、改变采购单状态
        Long id = doneVo.getId();

        //2、改变采购项的状态
        Boolean flag = true;
        //遍历所以的采购项
        List<PurchaseItemDoneVo> items = doneVo.getItems();
        List<PurchaseDetailEntity> updates = new ArrayList<>();
        for (PurchaseItemDoneVo item : items) {
            PurchaseDetailEntity detailEntity = new PurchaseDetailEntity();
            if (item.getStatus() == WareConstant.PurchaseDetailStatusEnum.HASHERROR.getCode()) {
                flag = false;
                //意思就是失败了：
                detailEntity.setStatus(item.getStatus());
            } else {
                //采购成功
                detailEntity.setStatus(WareConstant.PurchaseDetailStatusEnum.FINISH.getCode());
                //3、将成功采购的进行入库
                PurchaseDetailEntity entity = detailService.getById(item.getItemId());

                wareSkuService.addStock(entity.getSkuId(), entity.getWareId(), entity.getSkuNum());
            }
            detailEntity.setId(item.getItemId());
            updates.add(detailEntity);
        }
        //来一个批量更新操作
        detailService.updateBatchById(updates);

        //1、改变采购单状态
        PurchaseEntity purchaseEntity = new PurchaseEntity();
        purchaseEntity.setId(id);
        purchaseEntity.setStatus(flag ? WareConstant.PurchaseStatusEnum.FINISH.getCode() : WareConstant.PurchaseStatusEnum.HASHERROR.getCode());
        purchaseEntity.setUpdateTime(new Date());
        this.updateById(purchaseEntity);
    }
```

WareSkuService.java

```java
void addStock(Long skuId, Long wareId, Integer skuNum);
```

WareSkuServiceImpl.java

```java
    @Autowired
    WareSkuDao wareSkuDao;

    @Autowired
    ProductFeignService productFeignService;

    @Override
    public void addStock(Long skuId, Long wareId, Integer skuNum) {

        //1、判断如果没有这个库存记录，那么我们就新增
        List<WareSkuEntity> entities = wareSkuDao.selectList(new QueryWrapper<WareSkuEntity>().eq("sku_id", skuId).eq("ware_id", wareId));
        if (entities == null || entities.size() == 0) {
            WareSkuEntity skuEntity = new WareSkuEntity();
            skuEntity.setSkuId(skuId);
            skuEntity.setWareId(wareId);
            skuEntity.setStock(skuNum);//库存
            skuEntity.setStockLocked(0);
            //TODO 远程查询sku的名字（远程调用）,如果失败整个事务无需回滚
            //方式一：我们自己catch掉异常
            //TODO 方式二：还可以用什么办法让异常出现以后不回滚？？？ (高级篇见分晓)
            try {
                R info = productFeignService.info(skuId);
                Map<String, Object> data = (Map<String, Object>) info.get("skuInfo");
                if (info.getCode() == 0) {   //0:表示成功
                    skuEntity.setSkuName((String) data.get("skuName"));
                }
            } catch (Exception e) {

            }

            wareSkuDao.insert(skuEntity);
        } else {
            //2、如果有的话，我们才做更新操作
            wareSkuDao.addStock(skuId, wareId, skuNum);
        }

    }
```

PurchaseDoneVo.java

```java
package com.atguigu.gulimall.ware.vo;

import lombok.Data;

import javax.validation.constraints.NotNull;
import java.util.List;

/**
 * @Author Dali
 * @Date 2021/11/18 19:59
 * @Version 1.0
 * @Description: 07、完成采购-接口：/ware/purchase/done
 * <p>
 * 接口文档地址：https://easydoc.net/doc/75716633/ZUqEdvA4/cTQHGXbK
 */
@Data
public class PurchaseDoneVo {
    @NotNull
    private Long id;        //采购单id

    /**
     * items: [{itemId:1,status:4,reason:""}]//完成/失败的需求详情
     */
    private List<PurchaseItemDoneVo> items;
}
```

PurchaseItemDoneVo.java

```java
package com.atguigu.gulimall.ware.vo;

import lombok.Data;

/**
 * @Author Dali
 * @Date 2021/11/18 20:01
 * @Version 1.0
 * @Description
 */
@Data
public class PurchaseItemDoneVo {
    //itemId:1,status:4,reason:""
    private Long itemId;
    private Integer status;
    private String reason;
}
```

GulimallWareApplication.java

```java
package com.atguigu.gulimall.ware;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@EnableFeignClients
@EnableDiscoveryClient
@SpringBootApplication
public class GulimallWareApplication {

    public static void main(String[] args) {
        SpringApplication.run(GulimallWareApplication.class, args);
    }

}
```

WareSkuDao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.atguigu.gulimall.ware.dao.WareSkuDao">

    <!-- 可根据自己的需求，是否要使用 -->
    <resultMap type="com.atguigu.gulimall.ware.entity.WareSkuEntity" id="wareSkuMap">
        <result property="id" column="id"/>
        <result property="skuId" column="sku_id"/>
        <result property="wareId" column="ware_id"/>
        <result property="stock" column="stock"/>
        <result property="skuName" column="sku_name"/>
        <result property="stockLocked" column="stock_locked"/>
    </resultMap>
    <update id="addStock">
        update  `wms_ware_sku` set stock = stock+#{skuNum} where sku_id = #{skuId} and ware_id = #{wareId}
    </update>

</mapper>
```

# P100、商品服务-API-商品管理-SPU规格维护

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209430.png)

## P100勘误：抱歉！您访问的页面失联啦...

P100点击规格找不到页面，解决：在数据库gulimall_admin执行以下sql再刷新页面即可：【INSERT INTO sys_menu (menu_id, parent_id, name, url, perms, type, icon, order_num) VALUES (76, 37, 规格维护, product/attrupdate, , 2, log, 0);】    **（暂时先不加也可以）**

```sql
INSERT INTO sys_menu (menu_id, parent_id, name, url, perms, type, icon, order_num) VALUES (76, 37, 规格维护, product/attrupdate, , 2, log, 0);
```

看一下老师**/src/router/index.js** 在**mainRoutes-children**【】里面加上下面这部分代码就不会404了：

`{ path: /product-attrupdate, component: _import(modules/product/attrupdate), name: attr-update, meta: { title: 规格维护, isTab: true } }`

```html
{ path: /product-attrupdate, component: _import(modules/product/attrupdate), name: attr-update, meta: { title: 规格维护, isTab: true } }
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209431.png)

正常显示：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209432.png)

## 22、获取spu规格-接口

[22、获取spu规格](https://easydoc.net/doc/75716633/ZUqEdvA4/GhhJhkg7)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209433.png)

**不能回显的兄弟去规格属性菜单那里把多选变单选，不能回选在属性菜单把多选改成单选。规格参数回显不了的，你要么把数据全改为单选，要么全改为多选**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209434.png)

## 23、修改商品规格-接口

[23、修改商品规格](https://easydoc.net/doc/75716633/ZUqEdvA4/GhnJ0L85)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209435.png)

## 本期代码

AttrController.java

```java
    @Autowired
    private ProductAttrValueService productAttrValueService;

    /**
     * 涉及到的表：pms_product_attr_value
     * 接口：22、获取spu规格： product/attr/base/listforspu/{spuId}
     * 接口文档地址：https://easydoc.net/doc/75716633/ZUqEdvA4/GhhJhkg7
     *
     * @param spuId
     * @return
     */
    @GetMapping("/base/listforspu/{spuId}")
    public R baseAttrlistforspu(@PathVariable("spuId") Long spuId) {
        List<ProductAttrValueEntity> entities = productAttrValueService.baseAttrlistforspu(spuId);
        return R.ok().put("data", entities);
    }

    /**
     * 23、修改商品规格：product/attr/update/{spuId}
     * 接口地址：https://easydoc.net/doc/75716633/ZUqEdvA4/GhnJ0L85
     */
    @PostMapping("/update/{spuId}")
    public R updateSpuAttr(@PathVariable("spuId") Long spuId, @RequestBody List<ProductAttrValueEntity> entities) {
        productAttrValueService.updateSpuAttr(spuId, entities);

        return R.ok();
    }
```

ProductAttrValueService.java

```java
List<ProductAttrValueEntity> baseAttrlistforspu(Long spuId);

void updateSpuAttr(Long spuId, List<ProductAttrValueEntity> entities);
```

ProductAttrValueServiceImpl.java

```java
    @Override
    public List<ProductAttrValueEntity> baseAttrlistforspu(Long spuId) {
        List<ProductAttrValueEntity> entities = this.baseMapper.selectList(new QueryWrapper<ProductAttrValueEntity>().eq("spu_id", spuId));
        return entities;
    }

    @Transactional
    @Override
    public void updateSpuAttr(Long spuId, List<ProductAttrValueEntity> entities) {
        //1、先删除这个spuId对应的所有属性
        this.baseMapper.delete(new QueryWrapper<ProductAttrValueEntity>().eq("spu_id", spuId));

        //2、删除之后在批量插入
        List<ProductAttrValueEntity> collect = entities.stream().map(item -> {
            item.setSpuId(spuId);
            return item;
        }).collect(Collectors.toList());

        this.saveBatch(collect);
    }
```

# P101-分布式基础篇总结

[01_谷粒商城-项目简介&分布式基础概念](https://www.notion.so/01_-6cd501bbf35b49f3b137c30f098f8a10)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206062209436.png)