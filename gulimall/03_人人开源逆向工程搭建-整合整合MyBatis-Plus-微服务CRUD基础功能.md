# 03_人人开源逆向工程搭建-整合整合MyBatis-Plus-微服务CRUD基础功能



# 人人开源：代码生成器|人人项目-逆向工程

[人人开源](https://gitee.com/renrenio)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

把人人开源的逆向工程代码clone到我们电脑本地。

```bash
$ git clone https://gitee.com/renrenio/renren-generator.git
```

下载到桌面后，同样把里面的`.git`文件删除，然后移动到我们IDEA项目目录中，同样配置好pom.xml

```xml
<modules>
    <module>gulimall-coupon</module>
    <module>gulimall-member</module>
    <module>gulimall-order</module>
    <module>gulimall-product</module>
    <module>gulimall-ware</module>
    <module>renren-fast</module>
    <module>renren-generator</module>
</modules>
```

在maven中刷新一下，让项目名变粗体，稍等下面进度条完成。

修改application.yml

```bash
url: jdbc:mysql://192.168.56.10:3306/gulimall-pms?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
username: root
password: root

# 我们使用这一个
url: jdbc:mysql://**192.168.56.10**:3306/**gulimall_pms**?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
username: **root**
password: **root**
```

然后修改generator.properties（这里乱码的百度IDEA设置properties编码）

```bash
# 主目录
mainPath=com.atguigu
#包名
package=com.atguigu.gulimall
#模块名
moduleName=product
#作者
author=hh
#email
email=55333@qq.com
#表前缀(类名不会包含表前缀) # 我们的pms数据库中的表的前缀都pms
# 如果写了表前缀，每一张表对于的javaBean就不会添加前缀了
tablePrefix=pms_
```

运行RenrenApplication。启动成功的话我们浏览器访问：[http://localhost/](http://localhost/)

如果启动不成功，修改application中是port为801。访问：http://localhost:801/

在网页上下方点击每页显示50个（pms库中的表），以让全部都显示，然后点击全部，点击生成代码。下载了压缩包

[](http://localhost/sys/generator/code?tables=pms_attr,undo_log,pms_sku_images,pms_spu_info_desc,pms_product_attr_value,pms_comment_replay,pms_category_brand_relation,pms_category,pms_brand,pms_spu_info,pms_attr_group,pms_spu_images,pms_attr_attrgroup_relation,pms_spu_comment,pms_sku_sale_attr_value,pms_sku_info)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

解压压缩包，把`main`放到`gulimall-product`的同级目录下。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

## common: 公共部分

然后在项目上右击（在项目上右击很重要）new modules— maven—然后在name上输入`gulimall-common`。

在pom.xml中也自动添加了`<module>gulimall-common</module>`

在common项目的pom.xml中添加

```xml
<description>每一个微服务公共的依赖，bean, 工具类等</description>

<!-- mybatisPLUS-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.2</version>
</dependency>
<!--简化实体类，用@Data代替getset方法-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.8</version>
</dependency>
<!-- httpcomponent包。发送http请求 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpcore</artifactId>
    <version>4.4.13</version>
</dependency>
<dependency>
    <groupId>commons-lang</groupId>
    <artifactId>commons-lang</artifactId>
    <version>2.6</version>
</dependency>
```

我们把每个微服务里公共的类和依赖放到common里。

> tips: `shift+F6`修改项目名
此外，说下maven依赖的问题。
`<dependency>`代表本项目依赖，子项目也依赖
如果有个`<optional>`标签，代表本项目依赖，但是子项目不依赖
> 

然后在product项目中的pom.xml中加入下面内容，作为common的子项目

```xml
<dependency>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

复制

- renren-fast----utils包下的Query和`PageUtils`、`R`、`Constant`复制到common项目的`java/com.atguigu.common.utils`下。另外关于R的类，它继承了hashmap，你会发现map里的table数组是transient的，也就是不序列化的，但还好在它实现了Clonable接口重写了clone方法，该方法中会new新的数组作为序列号内容，所以hashmap可以用作序列化。但是序列号还是浅拷贝。在远程调用响应中，按理说应该自己序列化深拷贝后远程才能拿到，所以我得想法是应该自己序列化之后穿字节码(所以说json字符串)过去，但是视频里直接设置object就传输过去了，我比较奇怪为什么这种情况传输的不是浅拷贝？难道是mvc有这个自动序列化机制？之前读mvc源码没太注意，如果有人懂这个问题麻烦告知一下
- 把@RequiresPermissions这些注解掉，因为是shiro的
- 复制renren-fast中的xss包粘贴到common的com.atguigu.common目录下。
- 还复制了exception文件夹，对应的位置关系自己观察一下就行
- 注释掉product项目下类中的`//import org.apache.shiro.authz.annotation.RequiresPermissions;`，他是shiro的东西
- 注释renren-generator\src\main\resources\template/Controller中所有的@RequiresPermissions。`## import org.apache.shiro.authz.annotation.RequiresPermissions;`

总之什么报错就去`fast`里面找。重启逆向工程。重新在页面上得到压缩包。重新解压出来，不过只把里面的controller复制粘贴到product项目对应的目录就行。

**测试：**

测试与整合商品服务里的mybatisplus

[https://mp.baomidou.com/guide/quick-start.html#配置](https://mp.baomidou.com/guide/quick-start.html#%E9%85%8D%E7%BD%AE)

在common的pom.xml中导入

```xml
<!-- 数据库驱动 https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.17</version>
</dependency>
<!--tomcat里一般都带-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>
</dependency>
```

删掉common里xss/xssfiler和XssHttpServletRequestWrapper

## 谷粒商城-商品服务：gulimall-product：端口号10000 | 数据库：gulimall_pms

在product项目的resources目录下新建application.yml

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_pms
    driver-class-name: com.mysql.jdbc.Driver

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

> classpath 和 classpath* 区别：
classpath：只会到你的class路径中查找找文件;
classpath*：不仅包含class路径，还包括jar文件中(class路径)进行查找
classpath*的使用：当项目中有多个classpath路径，并同时加载多个classpath路径下（此种情况多数不会遇到）的文件，*就发挥了作用，如果不加*，则表示仅仅加载第一个classpath路径。
> 

然而执行后能通过，但是数据库中文显示乱码，所以我模仿逆向工程，把上面的配置url改为

```yaml
url: jdbc:mysql://192.168.56.10:3306/gulimall_pms?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
```

正常了。

# 整合整合MyBatis-Plus

[使用配置 | MyBatis-Plus](https://mp.baomidou.com/config/)

MyBatis-Plus：官网配置教程

然后在主启动类上加上注解@MapperScan()

```java
package com.atguigu.gulimall.product;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 1.整合MyBatis-Plus
 *      1.导入依赖：
 *      <dependency>
 *             <groupId>com.baomidou</groupId>
 *             <artifactId>mybatis-plus-boot-starter</artifactId>
 *             <version>3.3.2</version>
 *      </dependency>
 *      2.配置：参照MyBatis-Plus官网
 *          1.配置数据源
 *            1).导入数据库驱动
 *            2).在application.yml配置数据源相关信息
 *          2.配置MyBatis-Plus相关信息
 *            1).使用@MapperScan
 *            2).告诉MyBatis-Plus，sql映射文件位置在哪里
 */
**@MapperScan("com.atguigu.gulimall.product.dao")**
@SpringBootApplication
public class GulimallProductApplication {

    public static void main(String[] args) {
        SpringApplication.run(GulimallProductApplication.class, args);
    }

}
```

然后去测试，先通过下面方法给数据库添加内容

```java
package com.atguigu.gulimall.product;

import com.atguigu.gulimall.product.entity.BrandEntity;
import com.atguigu.gulimall.product.service.BrandService;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class GulimallProductApplicationTests {
    @Autowired
    BrandService brandService;

    @Test
    void contextLoads() {
        BrandEntity brandEntity = new BrandEntity();
//        brandEntity.setBrandId(6L);
//        brandEntity.setDescript("华为天下第一");
//        brandService.updateById(brandEntity);

        /* 
        brandEntity.setName("华为");
        brandService.save(brandEntity);
        System.out.println("保存成功.....");
        */

        List<BrandEntity> list = brandService.list(new QueryWrapper<BrandEntity>().eq("brand_id", 1L));
//        for (BrandEntity entity : list) {
//            System.out.println(entity);
//        }
        list.forEach((item)->{
            System.out.println(item);
        });
    }
}
```

在`gulimall_pms`数据库中`pms_brand`b表中就能看到新增数据了。

# 19、快速开发-逆向生成所有微服务基本CRUD代码

## 谷粒商城-优惠券服务：coupon 端口号：7000 | 对应的数据库：gulimall_sms

优惠券服务。重新打开**generator**逆向工程，修改`generator.properties`

```bash
# 主目录
mainPath=com.atguigu
#包名
package=com.atguigu.gulimall
#模块名
moduleName=coupon
#作者
autho=王冉昕
#email
email=55333@qq.com
#表前缀(类名不会包含表前缀) # 我们的pms数据库中的表的前缀都pms
# 如果写了表前缀，每一张表对于的javaBean就不会添加前缀了
tablePrefix=sms_

```

修改同级目录下application.yml文件中的数据库信息

```yaml
# mysql
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    #MySQL配置
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://**192.168.56.10**:3306/gulimall_sms?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

启动生成**RenrenApplication.java**，运行后去浏览器访问：[http://localhost/](http://localhost/)查看，同样让他一页全显示后选择全部后生成。生成后解压复制main文件夹到coupon项目对应目录下。

### gulimall-coupon：

让**coupon**也依赖于**common**，修改**pom.xml**

```xml
<dependency>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>

```

resources下src包先删除

添加application.yml

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_sms
    driver-class-name: com.mysql.jdbc.Driver

# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto

server:
  port: 7000
```

浏览器运行：[http://localhost:8080/coupon/coupon/list](http://localhost:8080/coupon/coupon/list)

运行**gulimallCouponApplication.java 服务**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

[https://www.notion.so](https://www.notion.so)

## 谷粒商城-会员服务：member：对应的数据库gulimall_ums | 端口号8000

修改：generator.properties文件

```powershell
# 主目录
mainPath=com.atguigu
#包名
package=com.atguigu.gulimall
#模块名
moduleName=member
#作者
author=hh
#email
email=55333@qq.com
#表前缀(类名不会包含表前缀) # 我们的pms数据库中的表的前缀都pms
# 如果写了表前缀，每一张表对于的javaBean就不会添加前缀了
tablePrefix=**ums_**

```

修改generator.properties同级目录下的application.yml文件

```yaml
# mysql
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    #MySQL配置
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.56.10:3306/**gulimall_ums**?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

重启`RenrenApplication.java`，然后同样去浏览器：[http://localhost/#generator.html](http://localhost/#generator.html)  获取压缩包解压到对应member项目目录（拷贝解压后的main文件夹到idea对应的目录）

### gulimall-member：

谷粒商城-会员服务：`gulimall-member`也导入依赖

```xml
<dependency>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>

```

同样新建application.yml文件：

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/gulimall_ums
    driver-class-name: com.mysql.jdbc.Driver

# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto
server:
  port: 8000
```

order端口是9000，product是10000，ware是11000。

以后比如order系统要复制多份，他的端口计算9001、9002。。。

重启web后，浏览器访问：`http://localhost:8000/member/growthchangehistory/list`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

## 谷粒商城-订单服务：gulimall-order：端口号9000 | 对应的数据库 gulimall_oms

修改人人代码代码生成器中的：`generator.properties` 文件

```yaml
#代码生成器，配置信息

# 主目录
mainPath=com.atguigu
#包名
package=com.atguigu.gulimall
#模块名
moduleName=order
#作者
author=hh
#email
email=55333@qq.com
#表前缀(类名不会包含表前缀) # 我们的pms数据库中的表的前缀都pms
# 如果写了表前缀，每一张表对于的javaBean就不会添加前缀了
tablePrefix=oms_

```

修改同级目录下的**`application.yml`**文件

```yaml
# mysql
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    #MySQL配置
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.56.10:3306/gulimall_oms?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

运行**`RenrenApplication.java`**重新生成后去下载解压后将main文件复制粘贴到gulimall-order工程idea工程对应的目录替换到原有的main文件。

### gulimall-order：项目中进行以下配置

新建：application.yml文件

```yaml
server:
  port: 9000

spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/**gulimall_oms**
    driver-class-name: com.mysql.jdbc.Driver

# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto
```

gulimall-order项目中pom.xml导入common通用配置包

```xml
<dependency>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>

```

启动gulimallOrderApplication.java

[http://localhost:9000/order/order/list](http://localhost:9000/order/order/list)

```json
	{"msg":"success","code":0,"page":{"totalCount":0,"pageSize":10,"totalPage":0,"currPage":1,"list":[]}}
```

[https://www.notion.so](https://www.notion.so)

## 谷粒商城-仓储服务：gulimall-ware | 端口号：11000 | 数据库：**gulimall_wms**

修改人人开源代码生成器：`generator.properties` 文件

```yaml
#代码生成器，配置信息

# 主目录
mainPath=com.atguigu
#包名
package=com.atguigu.gulimall
#模块名
moduleName=**ware**
#作者
author=hh
#email
email=55333@qq.com
#表前缀(类名不会包含表前缀) # 我们的pms数据库中的表的前缀都pms
# 如果写了表前缀，每一张表对于的javaBean就不会添加前缀了
tablePrefix=**wms_**

```

修改同级目录下的application.yml文件

```yaml
# mysql
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    #MySQL配置
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.56.10:3306/**gulimall_wms**?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

运行RenrenApplication.java，然后浏览器打开：[http://localhost/](http://localhost/) 重新生成后去下载解压放置，将解压之后的main文件夹拷贝到gulimall-ware工程下对应的目录，把原有的main文件替换即可。

### gulimall-ware：新建application.yml

```yaml
server:
  port: 11000
  

spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.56.10:3306/**gulimall_wms**
    driver-class-name: com.mysql.jdbc.Driver

# MapperScan
# sql映射文件位置
mybatis-plus:
  mapper-locations: classpath:/mapper/**/*.xml
  #主键自增
  global-config:
    db-config:
      id-type: auto
```

gulimall-ware修改pom.xml文件：添加公共类common依赖

```xml
<dependency>
    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall-common</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>

```

启动**gulimallWareApplication.java**

浏览器访问：http://localhost:11000/ware/wareinfo/list

只要msg：提示success就可以了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)