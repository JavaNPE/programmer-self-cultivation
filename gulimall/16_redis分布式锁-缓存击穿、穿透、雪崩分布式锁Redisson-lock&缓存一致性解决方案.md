# 16_redis分布式锁-缓存击穿、穿透、雪崩|分布式锁Redisson-lock&缓存一致性解决方案

# P151、 缓存缓存使用-本地缓存与分布式缓存

## 1、缓存的使用

为了系统性能的提升，我们一般都会将部分数据放入缓存中，加速访问。而db承担数据落盘工作。

巨量读的操作走缓存，写的操作才走数据库。

> 哪些数据适合放入缓存?
> 
- 即时性、数据一致性要求不高的
- 访问量大且更新频率不高的数据(读多，写少)

举例：电商类应用，商品分类，商品列表等适合缓存并加一个失效时间(根据数据更新频率来定)，后台如果发布一个商品，买家需要5分钟才能看到新的商品一般还是可以接受的。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906578.png)

伪代码：

```java
data = cache.load(id);//从缓存加载数据
if(data == null){
	data = db.load(id);//从数据库加载数据
	cache.put(id,data);//保存到cache中
	retum data;
}
```

**注意：**在开发中，凡是放入缓存中的数据我们都应该指定过期时间，使其可以在系统即使没有主动更新数据也能自动触发数据加载进缓存的流程。避免业务崩溃导致的数据永久不一致问题。

最简单的缓存方式就是Map集合，map是可以做本地缓存使用，但是用map的问题在于，内存，以及分布式情况下的数据不一致问题，不能持久化。单服务还好，多个相同服务，缓存就不一致了，所以还是需要外部的分布式缓存。

### 本地缓存

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906580.png)

### 分布式缓存-本地模式在分布式下的问题

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906581.png)

### 分布式缓存

缓存抽离，单独作为一个中间件，保证数据一致性问题。消息中间件保证数据一致性。

缓存中间件：redis，zSet可以用跳表来实现，面试常问哦

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906582.png)

# P152、 缓存使用-整合redis测试案例

# 整合redis步骤

1. **引入data-redis-starter**
2. **简单配置redis的host等信息**
3. **使用SpringBoot自动配置好的StringRedisTemplate来操作redis**
    1. *`redis-》(简单理解为一个map)Map;存放数据key,数据值value`*

虚拟机中安装redis，由于我们之前已经通过docker安装过了redis。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906584.png)

那么还需要我们将redis整合到我们的项目上。在gulimall-product服务中的pom文件中引入redis的pom依赖。

### 1、**引入data-redis-starter**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 2、application.yml，**简单配置redis的host等信息**

```yaml
redis:
    host: 192.168.56.10
    port: 6379
```

### 3、**使用SpringBoot自动配置好的StringRedisTemplate来操作redis**

GulimallProductApplicationTests

```java
@Autowired
StringRedisTemplate stringRedisTemplate;

/**
 * 测试redis
 */
@Test
public void stringRedisTemplate() {
	//key-> Hello  value-> World
	ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();

	// 保存数据操作
	ops.set("Hello", "World" + UUID.randomUUID().toString());

	// 查询操作
	String hello = ops.get("Hello");
	System.out.println("之前保存的数据是：" + hello);
}
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906585.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906586.png)

# P153、缓存-Redis缓存使用-改造三级分类业务

在gulimall-product中的CategoryServiceImpl实现类中使用redis做缓存。

【**注意】**：**给缓存中放json字符串，拿出的json字符串，还要逆转为能用的对象类型；【序列号与反序列化】**

1、加入缓存逻辑  get(key) | 注意：缓存中存的数据是json字符串

2、缓存中没有所需数据，那么就去查询数据库（从数据库中获取数据）

3、将从数据中查到的数据放入到缓存中去，注意：需要将查询出来的对象转成json然后在放在缓存中

**【注意】**记得逆转回来：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>

**【细节**】*`JSON.parseObject引入这种类型： parse0bject(String text, TypeReference<T> type, Feature... features )T`*

JSON的好处：其是跨语言，跨平台兼容性强。

CategoryServiceImpl

```java
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.TypeReference;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.util.StringUtils;

/**
 * SpringBoot中使用redis做缓存
 */
@Autowired
private StringRedisTemplate redisTemplate;

/**
 * P153、缓存-redis缓存使用-改造三级分类业务
 *
 * @return
 */
@Override
public Map<String, List<Catelog2Vo>> getCatalogJson() {
	// 注意：给缓存中放json字符串，拿出的json字符串，还要逆转为能用的对象类型；【序列号与反序列化】

	// 1、加入缓存逻辑  get(key) | 注意：缓存中存的数据是json字符串
	// JSON的好处：其是跨语言，跨平台兼容
	String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
	if (StringUtils.isEmpty(catalogJSON)) {
		// 2、缓存中没有所需数据，那么就去查询数据库（从数据库中获取数据）
		Map<String, List<Catelog2Vo>> catalogJsonFromDb = getCatalogJsonFromDb();
		// 3、将从数据中查到的数据放入到缓存中去，注意：需要将查询出来的对象转成json然后在放在缓存中
		String s = JSON.toJSONString(catalogJsonFromDb);
		redisTemplate.opsForValue().set("catalogJSON", s);
		return catalogJsonFromDb;
	}

	// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
	// JSON.parseObject引入这种类型： parse0bject(String text, TypeReference<T> type, Feature... features )T
	Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
	});
	return result;
}

/**
 * 查出所有的分类(仅业务逻辑优化)
 * 从数据库中查询并封装整个分类数据（未使用redis做缓存）
 *
 * @return
 */
//@Override
public Map<String, List<Catelog2Vo>> getCatalogJsonFromDb() {

	方法中的内容与上节课内容相同，仅仅将原来的getCatalogJson改成了getCatalogJsonFromDb，然后注释掉//@Override

}
```

# P154、Redis缓存使用-压力测试出的内存泄露及解决

我们测试上一节课使用redis作为缓存优化后的代码重启服务在进行压力测试，

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906587.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906588.png)

浏览器显示服务崩了，RedisException: io.netty.util.internal.OutOfDirectMemoryError:堆外内存溢出

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906589.png)

### 上线或进行压力测试的时候会产生堆外内存溢出：OutOfDirectMemoryError

堆外内存溢出产生的原因：

1、springboot2.0以后默认使用lettuce作为操作redis的客户端。它使用netty进行网络通信。

2、lettuce的bug导致netty堆外内存溢出，-Xmx300m; netty如果没有指定堆外内存，它就会默认使用-Xmx300m，可以通过-Dio.netty.maxDirectMemory进行设置。

### 解决堆外内存溢出方案：不能使用-Dio.netty.maxDirectMemory只去调大堆外内存

- 方案一、升级lettuce
- 方案二、切换jedis

### redisTemplate与lettuce和jedis之间的关系？

lettuce和jedis是操作redis的底层客户端，spring对他俩再次封装，就成了redisTemplate。

gulimall-product中的pom文件，暂时将lettuce排除，在引入jedis依赖（上线后会有另一种解决方案）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- 方案二、切换jedis-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

# 【面试】P155、 redis缓存-缓存使用-缓存击穿、穿透、雪崩——重要

## 高并发下缓存失效问题-缓存穿透

### 缓存穿透：

**指查询一个一定不存在的数据**，由于缓存是不命中，将去查询数据库，但是数据库也无此记录，我们没有将这次查询的null写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

### 缓存穿透导致的潜在风险：

利用不存在的数据进行攻击，数据库瞬时压力增大，最终导致崩溃

### 如何解决缓存穿透：设置缓存空数据

null结果缓存，并加入短暂过期时间

## 【面试】防止缓存穿透方式一：设置让其缓存空值

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906590.png)

---

## 高并发下缓存失效问题-缓存雪崩

### 缓存雪崩:

缓存雪崩是指在我们设置缓存时key采用了相同的过期时间，导致缓存在某一时刻同时失效，请求全部转发到DB，DB瞬时压力过重雪崩。**（缓存雪崩指的是大面积key同时失效）**

### 如何解决缓存雪崩:

原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

> 缓存雪崩解决办法之——加随机时间又容易引发的其他问题有：
> 

![缓存雪崩解决办法之——加随机时间又容易引发的其他问题有.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906591.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906592.png)

---

## 高并发下缓存失效问题-**缓存击穿**

### 缓存穿透：

缓存击穿简单理解：M416打土墙，一梭子扫过去，墙体不会被大透，但是，若是一梭子（高并发）瞄准一个点（热点key），这堵墙就会被打穿一个洞，伤到后面的人。**（缓存击穿指的是某个高频热点的key失效）**

- 对于一些设置了过期时间的key，如果这些key可能会在某些时间点被超高并发地访问，是一种非常"热点”的数据。
- 如果这个key在大量请求同时进来前正好失效，那么所有对这个key的数据查询都落到db，我们称为缓存击穿。

### 缓存击穿解决：加锁

大量并发只让一个去查，其他人等待，查到以后释放锁，其他人获取到锁，先查缓存，就会有数据，不用去db。

> 缓存击穿：大量并发进来同时查询一个正好过期的数据。解决方案：加锁。
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906593.png)

## 总结-解决方案：

### 1、空结果缓存：解决缓存穿透

### 2、设置过期时间（加随机值）： 解决缓存雪崩

加了一天的过期时间：

**`redisTemplate**.opsForValue().set(**"catalogJSON"**, s, 1, TimeUnit.***DAYS***);`

### 3、加锁：解决缓存击穿问题（如果锁加不好，又会出现很多问题）

# P156、 Redis缓存-缓存使用-（本地锁）加锁解决缓存击穿问题

## 分布式下如何加锁？

本地锁，只能锁住当前进程，所以我们需要分布式锁。

本地锁：synchronized，JUC(Lock)都称之为本地锁，只锁当前的进程；但是如果在分布式情况下，想要锁住所有，必须使用分布式锁。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906594.png)

- CategoryServiceImpl
  
    ```java
    /**
    	 * P153、缓存-redis缓存使用-改造三级分类业务
    	 * TODO 上线或进行压力测试的时候 会产生堆外内存溢出：OutOfDirectMemoryError
    	 * 堆外内存溢出产生的原因：
    	 * 		1、springboot2.0以后默认使用lettuce作为操作redis的客户端。它使用netty进行网络通信。
    	 * 		2、lettuce的bug导致netty堆外内存溢出，-Xmx300m; netty如果没有指定堆外内存，它就会默认使用-Xmx300m
    	 * 			可以通过-Dio.netty.maxDirectMemory进行设置
    	 * 解决方案：不能使用-Dio.netty.maxDirectMemory只去调大堆外内存
    	 * 		方案一、升级lettuce
    	 * 		方案二、切换jedis
    	 * redisTemplate与lettuce和jedis之间的关系？
    	 * 		lettuce和jedis是操作redis的底层客户端，spring对他俩再次封装，就成了redisTemplate
    	 *
    	 * @return
    	 */
    	@Override
    	public Map<String, List<Catelog2Vo>> getCatalogJson() {
    		// 注意：给缓存中放json字符串，拿出的json字符串，还要逆转为能用的对象类型；【序列号与反序列化】
    		/**
    		 * 1、空结果缓存：解决缓存穿透
    		 * 2、设置过期时间（加随机值）： 解决缓存雪崩
    		 * 3、加锁：解决缓存击穿问题（如果锁加不好，又会出现很多问题）
    		 */
    		// 1、加入缓存逻辑  get(key) | 注意：缓存中存的数据是json字符串
    		// JSON的好处：其是跨语言，跨平台兼容
    		String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
    		if (StringUtils.isEmpty(catalogJSON)) {
    			// 2、缓存中没有所需数据，那么就去查询数据库（从数据库中获取数据）
    			**System.out.println("缓存不命中...查询数据库...");**
    			Map<String, List<Catelog2Vo>> catalogJsonFromDb = getCatalogJsonFromDb();
    			// 3、将从数据中查到的数据放入到缓存中去，注意：需要将查询出来的对象转成json然后在放在缓存中
    			String s = JSON.toJSONString(catalogJsonFromDb);
    			redisTemplate.opsForValue().set("catalogJSON", s, 1, TimeUnit.DAYS);
    			return catalogJsonFromDb;
    		}
    		**System.out.println("缓存命中...直接返回...");**
    		// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
    		// JSON.parseObject引入这种类型： parse0bject(String text, TypeReference<T> type, Feature... features )T
    		Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
    		});
    		return result;
    	}
    
    	/**
    	 * 查出所有的分类(仅业务逻辑优化)
    	 * 从数据库中查询并封装整个分类数据（未使用redis做缓存）
    	 *
    	 * @return
    	 */
    	//@Override
    	public Map<String, List<Catelog2Vo>> getCatalogJsonFromDb() {
    
    		// 只要是同一把锁，就能锁住需要这个锁的所有线程
    		// 1、synchronized (this)： SpringBoot中所有的组件在容器中都是单例的。
    		// TODO 本地锁：synchronized，JUC(Lock)都称之为本地锁，只锁当前的进程；但是如果在分布式情况下，想要锁住所有，必须使用分布式锁
    		**synchronized (this) {**
    
    			**// 得到锁以后，我们应该再去缓存中确定一次，如果没有才需要继续查询
    			String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
    			if (!StringUtils.isEmpty(catalogJSON)) {
    				// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
    				// 如果返回不为null直接返回
    				Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
    				});
    				return result;
    			}**
    			**System.out.println("查询了数据库.....");**
    
    			/**
    			 * 1、将数据库的多次查询变成一次（P150、性能压测优化优化三级分类数据获取）
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
    	}
    ```
    

浏览器访问地址：h[ttp://localhost:10000/index/catalog.json](http://localhost:10000/index/catalog.json)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906595.png)

进行压力测试

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906596.png)

![查询了两遍数据库](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906597.png)

查询了两遍数据库

释放锁与写缓存不是原子操作导致其他拿到锁的线程检查缓存时缓存为空。

## 锁-时序问题

查缓存-》确认缓存没有-》查询数据库-》结果放入缓存

![黑色块为锁：这样的逻辑容易查询2遍数据库](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906598.png)

黑色块为锁：这样的逻辑容易查询2遍数据库

将结果放入缓存中是需要时间的，在这个时间间隔中，有可能下一个请求进来的时候上一个请求还没有将结果放入缓存中，结果又查了一次数据库。。。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906599.png)

刚同步块出来，放redis还是需要时间的；另外一个或多个拿到锁的在这段时间内在缓存拿不到数据，又会查数据库，是先释放锁，再缓存，还没来及缓存完，其他的又访问数据库。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906600.png)

修改之后的代码逻辑：查完数据库之后就将查询出来的数据放缓存种（保证在同一把本地锁的情况下，确保原子性操作）

- CategoryServiceImpl
  
    ```java
    @Override
    public Map<String, List<Catelog2Vo>> getCatalogJson() {
    	// 注意：给缓存中放json字符串，拿出的json字符串，还要逆转为能用的对象类型；【序列号与反序列化】
    	/**
    	 * 1、空结果缓存：解决缓存穿透
    	 * 2、设置过期时间（加随机值）： 解决缓存雪崩
    	 * 3、加锁：解决缓存击穿问题（如果锁加不好，又会出现很多问题）
    	 */
    	// 1、加入缓存逻辑  get(key) | 注意：缓存中存的数据是json字符串
    	// JSON的好处：其是跨语言，跨平台兼容
    	String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
    	if (StringUtils.isEmpty(catalogJSON)) {
    		// 2、缓存中没有所需数据，那么就去查询数据库（从数据库中获取数据）
    		System.out.println("缓存不命中...将要查询数据库...");
    		Map<String, List<Catelog2Vo>> catalogJsonFromDb = getCatalogJsonFromDb();
    
    		return catalogJsonFromDb;
    	}
    	System.out.println("缓存命中...直接返回...");
    	// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
    	// JSON.parseObject引入这种类型： parse0bject(String text, TypeReference<T> type, Feature... features )T
    	Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
    	});
    	return result;
    }
    
    /**
     * 查出所有的分类(仅业务逻辑优化)
     * 从数据库中查询并封装整个分类数据（未使用redis做缓存）
     *
     * @return
     */
    //@Override
    public Map<String, List<Catelog2Vo>> getCatalogJsonFromDb() {
    
    	// 只要是同一把锁，就能锁住需要这个锁的所有线程
    	// 1、synchronized (this)： SpringBoot中所有的组件在容器中都是单例的。
    	// TODO 本地锁：synchronized，JUC(Lock)都称之为本地锁，只锁当前的进程；但是如果在分布式情况下，想要锁住所有，必须使用分布式锁
    	synchronized (this) {
    
    		// 得到锁以后，我们应该再去缓存中确定一次，如果没有才需要继续查询
    		String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
    		if (!StringUtils.isEmpty(catalogJSON)) {
    			// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
    			// 如果返回不为null直接返回
    			Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
    			});
    			return result;
    		}
    		System.out.println("查询了数据库.....");
    
    		/**
    		 * 1、将数据库的多次查询变成一次（P150、性能压测优化优化三级分类数据获取）
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
    
    		// 3、将从数据中查到的数据放入到缓存中去，注意：需要将查询出来的对象转成json然后在放在缓存中
    		String s = JSON.toJSONString(parent_cid);
    		redisTemplate.opsForValue().set("catalogJSON", s, 1, TimeUnit.DAYS);
    		return parent_cid;
    	}
    }
    ```
    

记得清空redis中的缓存数据，然后使用JMeter对接口进行压测，同上：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906601.png)

压测结果：控制台只打印了一次**“查询了数据库...” 表名在锁的时序性问题上做了逻辑修改之后，保证了查询数据库操作的原子性。**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906602.png)

# P157、Redis缓存-缓存使用-本地锁在分布式下的问题

上一节内容，我们使用本地锁解决缓存穿透的问题，使用synchronized (this)锁的当前服务，单机版我们进行压力测试发现，控制台只打印了一次**“查询了数据库...” 表名在锁的时序性问题上做了逻辑修改之后，保证了查询数据库操作的原子性。**那么在分布式的情况下，使用本地锁会出现哪些问题？

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906603.png)

## IDEA模拟启动多个商品（同类）服务-集群部署

首先在idea中模拟部署很多商品服务，需要进行简单设置：`--server.port=10001`

![1](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906604.png)

1

![2](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906605.png)

2

![3](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906606.png)

3

![4](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906607.png)

4

![启动以上集群服务](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906608.png)

启动以上集群服务

JMeter设置：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906609.png)

![7](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906610.png)

7

压测之前先清空redis中的缓存数据：

![8](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906611.png)

8

4个gulimall-product商品服务各查询了一次数据库，说明使用本地锁：synchronized(this)只能锁住它自己那个this服务，不能锁住其他服务。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906612.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906613.png)

引入：**本地锁synchronized (this)只能锁住它自己的那个服务**，那么如果我们想**锁住所有的东西**，让所有的gulimall-product商品服务只查询一次数据库，然后将查询出来的数据放入到缓存中，我们可以使用**分布式锁来实现**。

# P158、【重要】缓存-分布式锁原理与使用

**分布式的基本原理：所有的服务都去共同的一个地方“占坑”只要能占到，就说拿到了锁，占不到就不能执行。**

## redis官网地址

[http://www.redis.cn/commands/set.html](http://www.redis.cn/commands/set.html)

## 分布式锁演进-基本原理

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906614.png)

我们可以同时去一个地方“占坑”，如果占到，就执行逻辑。否则就必须等待，直到释放锁。“占坑”可

以去redis，可以去数据库，可以去任何大家都能访问的地方。等待可以自旋的方式。

分布式锁一般使用的是redis中的`setnx`命令来实现。

[set 命令 -- Redis中国用户组（CRUG）](http://www.redis.cn/commands/set.html)

- `EX` *seconds* – 设置键key的过期时间，单位时秒
- `PX` *milliseconds* – 设置键key的过期时间，单位时毫秒
- `NX` – 只有键key不存在的时候才会设置key的值
- `XX` – 只有键key存在的时候才会设置key的值

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906615.png)

## XShell骚操作：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906617.png)

在撰写栏中输入：`docker exec -it redis redis-cli` ，然后选择发送全部会话（A）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906618.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906619.png)

`set lock hahah NX`   解释：`NX` – 只有键key不存在的时候才会设置key的值

![`NX` – 只有键key不存在的时候才会设置key的值](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906620.png)

`NX` – 只有键key不存在的时候才会设置key的值

结果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906621.png)

## 分布式锁演进-阶段一

### 分布式锁容易产生的问题：

1、setnx占好了位，业务代码异常或者程序在页面过程中宕机。没有执行删除锁逻辑，这就造成了死锁

### 解决死锁方案：

设置锁的自动过期，即使没有删除，会自动删除。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906622.png)

**`redisTemplate**.opsForValue().setIfAbsent(**"lock"**, **"111"**);`   表示只有key不存在的时候才会设置key的值。

其中`setIfAbsent` 对应的就是redis中的*`SETNX` 命令。*

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906623.png)

> 思考：如果在**占用redis锁**和**设置过期时间**中间突然断电，依旧会出现死锁的问题（一辈子的死锁）。
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906624.png)

代码：

```java
/**
 * 从数据库中查询并封装整个分类数据： 使用redis做分布式锁
 * P158、【重要】缓存-分布式锁原理与使用
 *
 * @return
 */
public Map<String, List<Catelog2Vo>> getCatalogJsonFromDbWithRedisLock() {

	// 1、占分布式锁，去redis中占坑
	Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
	if (lock) {
		// 加锁成功......执行业务
		// 2、设置过期时间
		redisTemplate.expire("lock", 30, TimeUnit.SECONDS);
		Map<String, List<Catelog2Vo>> dataFromDb = getDataFromDb();
		redisTemplate.delete("lock");    // 记得删除锁
		return dataFromDb;
	} else {
		// 加锁失败（没有获取到锁）......休眠100毫秒然后进行重试。synchronized ()
		return getCatalogJsonFromDbWithRedisLock();    // 自旋的方式重试获取锁
	}
}
```

## 分布式锁演进-阶段二：解决死锁问题

### 问题：

1、setnx设置好，正要去设置过期时间，宕机。又死锁了。

### 解决方案

设置过期时间和占位必须是原子的。redis支持使用setnx ex命令

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906625.png)

```java
docker exec -it redis redis-cli
set lock 1111 EX 300 NX
ttl lock
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906626.png)

## 分布式锁演进-阶段三

### 问题：

1、删除锁直接删除？？？

如果由于业务时间很长，锁自己过期了，我们直接删除，有可能把别人正在持有的锁删除了。

### 解决：

占锁的时候，值指定为uuid，每个人匹配是自己的锁才删除。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906627.png)

```java
// 1、占分布式锁，去redis中占坑
//		Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
		// 确保设置过期时间,必须和加锁（占锁）是同步的原子的
		Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111", 300, TimeUnit.SECONDS);
		if (lock) {
			// 加锁成功......执行业务
			// 2、设置过期时间,必须和加锁（占锁）是同步的原子的
			// redisTemplate.expire("lock", 30, TimeUnit.SECONDS);
			Map<String, List<Catelog2Vo>> dataFromDb = getDataFromDb();
			redisTemplate.delete("lock");    // 记得删除锁
			return dataFromDb;
		} else {
			// 加锁失败（没有获取到锁）......休眠100毫秒然后进行重试。synchronized ()
			return getCatalogJsonFromDbWithRedisLock();    // 自旋的方式重试获取锁
		}
```

## 分布式锁演进-阶段四：

### 问题：

1、如果正好判断是当前值，正要删除锁的时候，锁已经过期，别人已经设置到了新的值。那么我们删除的是别人的锁

### 解决：

删除锁必须保证原子性。使用redis+Lua脚本完成。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906628.png)

```java
public Map<String, List<Catelog2Vo>> getCatalogJsonFromDbWithRedisLock() {

		// 1、占分布式锁，去redis中占坑
//		Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
		// 确保设置过期时间,必须和加锁（占锁）是同步的原子的
		String uuid = UUID.randomUUID().toString();
		Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid, 300, TimeUnit.SECONDS);
		if (lock) {
			// 加锁成功......执行业务
			// 2、设置过期时间,必须和加锁（占锁）是同步的原子的
			// redisTemplate.expire("lock", 30, TimeUnit.SECONDS);
			Map<String, List<Catelog2Vo>> dataFromDb = getDataFromDb();

			**// 获取值对比+对比成功删除=原子操作
			String lockValue = redisTemplate.opsForValue().get("lock");
			if (uuid.equals(lockValue)) {
				redisTemplate.delete("lock");    // 删除锁我自己的锁
			}**
			return dataFromDb;
		} else {
			// 加锁失败（没有获取到锁）......休眠100毫秒然后进行重试。synchronized ()
			return getCatalogJsonFromDbWithRedisLock();    // 自旋的方式重试获取锁
		}
	}
```

## 分布式锁演进-阶段五-最终形态

`String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";`

- 代码
  
    ```java
    /**
     * 从数据库中查询并封装整个分类数据： 使用redis做分布式锁
     * P158、【重要】缓存-分布式锁原理与使用
     *
     * @return
     */
    public Map<String, List<Catelog2Vo>> getCatalogJsonFromDbWithRedisLock() {
    
    	// 1、占分布式锁，去redis中占坑
    //		Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
    	// 确保设置过期时间,必须和加锁（占锁）是同步的原子的
    	String uuid = UUID.randomUUID().toString();
    	Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid, 300, TimeUnit.SECONDS);
    	if (lock) {
    		System.out.println("获取分布式锁成功...");
    		// 加锁成功......执行业务
    		// 2、设置过期时间,必须和加锁（占锁）是同步的原子的
    		// redisTemplate.expire("lock", 30, TimeUnit.SECONDS);
    		Map<String, List<Catelog2Vo>> dataFromDb;
    		try {
    			dataFromDb = getDataFromDb();
    		} finally {
    			// 获取值对比+对比成功删除=原子操作  lua脚本
    			String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
    			// 删除锁
    			Long lock1 = redisTemplate.execute(new DefaultRedisScript<Long>(script, Long.class), Arrays.asList("lock"), uuid);
    
    		}
    
    /*			// 获取值对比+对比成功删除=原子操作
    		String lockValue = redisTemplate.opsForValue().get("lock");
    		if (uuid.equals(lockValue)) {
    			redisTemplate.delete("lock");    // 删除锁我自己的锁
    		}*/
    		return dataFromDb;
    	} else {
    		// 加锁失败（没有获取到锁）......休眠100毫秒然后进行重试。synchronized ()
    		System.out.println("获取分布式锁失败...等待重试");
    		try {
    			Thread.sleep(200);
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		}
    		return getCatalogJsonFromDbWithRedisLock();    // 自旋的方式重试获取锁
    	}
    }
    ```
    

### redis分布式锁的核心

**保证加锁【占位+过期时间】和删除锁【判断+删除】的原子性**。更难的事情，锁的自动续期

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906629.png)

## JMeter测试：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906630.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906631.png)

redis中删除缓存

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906632.png)

启动以下服务：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906633.png)

**然后开始压力测试：记得关闭本地防火墙**

只有这一个服务查询了数据库。。。其他服务成功被锁。避免高并发下大量访问数据库，造成压力。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906634.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906635.png)

- 抽取出来的方法代码：getDataFromDb
  
    ```java
    private Map<String, List<Catelog2Vo>> getDataFromDb() {
    		String catalogJSON = redisTemplate.opsForValue().get("catalogJSON");
    		if (!StringUtils.isEmpty(catalogJSON)) {
    			// 逆转：转成我们指定的对象类型 Map<String, List<Catelog2Vo>>
    			// 如果返回不为null直接返回
    			Map<String, List<Catelog2Vo>> result = JSON.parseObject(catalogJSON, new TypeReference<Map<String, List<Catelog2Vo>>>() {
    			});
    			return result;
    		}
    		System.out.println("查询了数据库.....");
    
    		/**
    		 * 1、将数据库的多次查询变成一次（P150、性能压测优化优化三级分类数据获取）
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
    
    		// 3、将从数据中查到的数据放入到缓存中去，注意：需要将查询出来的对象转成json然后在放在缓存中
    		String s = JSON.toJSONString(parent_cid);
    		redisTemplate.opsForValue().set("catalogJSON", s, 1, TimeUnit.DAYS);
    		return parent_cid;
    	}
    ```
    
- 原来的getCatalogJsonFromDb方法改名getCatalogJsonFromDbWithLocalLock()
  
    ```java
    /**
     * 查出所有的分类(仅业务逻辑优化)
     * 从数据库中查询并封装整个分类数据：使用本地锁synchronized (this)的方式（未使用redis做缓存）
     *
     * @return
     */
    //@Override
    public Map<String, List<Catelog2Vo>> getCatalogJsonFromDbWithLocalLock() {
    
    	// 只要是同一把锁，就能锁住需要这个锁的所有线程
    	// 1、synchronized (this)： SpringBoot中所有的组件在容器中都是单例的。
    	// TODO 本地锁：synchronized，JUC(Lock)都称之为本地锁，只锁当前的进程；但是如果在分布式情况下，想要锁住所有，必须使用分布式锁
    	synchronized (this) {
    
    		// 得到锁以后，我们应该再去缓存中确定一次，如果没有才需要继续查询
    		return getDataFromDb();
    	}
    }
    ```
    

# P159、 缓存-分布式锁- Redisson简介&整合

## redis分布式锁官网地址：

[Distributed locks with Redis - Redis](https://redis.io/topics/distlock)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906636.png)

## Redisson中文文档地址

[Table of Content · redisson/redisson Wiki](https://github.com/redisson/redisson/wiki/Table-of-Content)

## Redisson的原生核心依赖，maven仓库地址

[](https://mvnrepository.com/search?q=redisson)

## Redisson配置方法

[2. 配置方法 · redisson/redisson Wiki](https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95)

## Redisson第三方框架整合

[14. 第三方框架整合 · redisson/redisson Wiki](https://github.com/redisson/redisson/wiki/14.-%E7%AC%AC%E4%B8%89%E6%96%B9%E6%A1%86%E6%9E%B6%E6%95%B4%E5%90%88)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906637.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906638.png)

## 整合redisson作为分布式锁等功能框架步骤

```
7、整合redisson作为分布式锁等功能框架
      1）、引入redisson依赖（暂时引入的是原生的依赖包，还需要配置；想省事引入redisson-starter）
<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
<dependency>
<groupId>org.redisson</groupId>
<artifactId>redisson</artifactId>
<version>3.12.0</version>
</dependency>
      2)、配置redisson(参考官方文档：https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95)

```

在gulimall-product模块中的pom文件中引入以下redisson依赖包

```xml
<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
<!-- 以后使用redisson作为所有分布式锁，分布式对象等功能框架 -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.12.0</version>
</dependency>
```

gulimall.product.config：

```java
package com.atguigu.gulimall.product.config;

import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.config.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.io.IOException;

/**
 * @Author Dali
 * @Date 2022/1/2 22:57
 * @Version 1.0
 * @Description: Redisson程序化的配置方法是通过构建Config对象实例来实现的
 * 官网地址：https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95
 *
 * 2.6. 单Redis节点模式
 * 程序化配置方法：
 * 		// 默认连接地址 127.0.0.1:6379
 * 		RedissonClient redisson = Redisson.create();
 * 		Config config = new Config();
 * 		config.useSingleServer().setAddress("myredisserver:6379");
 * 		RedissonClient redisson = Redisson.create(config);
 */
@Configuration
public class MyRedissonConfig {

	/**
	 * 所有对Redisson的使用都是通过RedissonClient对象
	 * @return
	 * @throws IOException
	 */
	@Bean(destroyMethod = "shutdown")
	public RedissonClient redisson() throws IOException {

		// 1、创建配置
		// 若报错：Redis url should start with redis:// or rediss:// (for SSL connection)
		Config config = new Config();
		// 注意：如果redis设置了密码，在这里要加一个setPassword方法
		config.useSingleServer().setAddress**("redis://192.168.56.10:6379")**;

		// 2、根据Config创建出RedissonClient实例
		RedissonClient redissonClient = Redisson.create(config);
		return redissonClient;
	}
}
```

单元测试代码：

```java
@Autowired
RedissonClient redissonClient;

/**
 * 测试Redisson
 */
@Test
public void redisson() {
	System.out.println(redissonClient);
}
```

单元测试Redisson结果：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906639.png)

# P160、 缓存分布式锁- Redisson-lock锁测试

[8. 分布式锁和同步器 · redisson/redisson Wiki](https://github.com/redisson/redisson/wiki/8.-%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%92%8C%E5%90%8C%E6%AD%A5%E5%99%A8)

所有的锁都应该设置成可重入锁，避免死锁问题。

[8. 分布式锁和同步器 · redisson/redisson Wiki · GitHub](https://www.notion.so/8-redisson-redisson-Wiki-GitHub-5034066856c041d1a53890affda8344f)

基于Redis的Redisson分布式可重入锁`[RLock](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RLock.html)` Java对象实现了`java.util.concurrent.locks.Lock`接口。同时还提供了[异步（Async）](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RLockAsync.html)、[反射式（Reactive）](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RLockReactive.html)和[RxJava2标准](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RLockRx.html)的接口。

```java
RLock lock = redisson.getLock("anyLock");
// 最常见的使用方法
lock.lock();
```

代码：IndexController

```java
@Autowired
RedissonClient redisson;

@ResponseBody
@GetMapping("/hello")
public String hello() {
	// 基于Redis的Redisson分布式可重入锁RLock
	// Java对象实现了java.util.concurrent.locks.Lock接口。
	// 1、获取一把锁，只要锁的名字一样，就是同一把锁
	RLock lock = redisson.getLock("my-lock");

	// 2、加锁
	lock.lock();	// 阻塞式等待。 默认加的锁都是30s的时间
	//1)、锁的自动续期，如果业务超长，运行期间自动给锁续上新的30s。不用担心业务时间长，锁自动过期被删掉
	//2)、加锁的业务只要运行完成，就不会给当前锁续期，即使不手动解锁， 锁默认在30s以后自动删除。
	try {
		System.out.println("加锁成功，执行业务..." + Thread.currentThread().getId());
		Thread.sleep(30000);
	} catch (InterruptedException e) {
		e.printStackTrace();
	} finally {
		// 3、解锁	，加锁解锁代码没有运行，redisson会不会出现死锁
		System.out.println("释放锁..." + Thread.currentThread().getId());
		lock.unlock();
	}
	return "hello";
}
```

浏览器中打开2个窗口，快速依次访问以下链接：[http://localhost:10000/hello](http://localhost:10000/hello)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906640.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906641.png)

分别启动`GulimallProductApplication [devtools]：10000`和`GulimallProductApplication - 10001 [devtools] :10001/`服务，在浏览器中分别依次快速访问[http://localhost:10000/hello](http://localhost:10000/hello)和 [http://localhost:10001/hello](http://localhost:10001/hello) 地址，然后强制`GulimallProductApplication [devtools]：10000`服务宕机，看`GulimallProductApplication - 10001 [devtools] :10001`

服务会不会响应（会不会出现死锁的情况），结果即使10000服务执行到一半，服务down掉了，过了30s，10001服务成功抢占锁，加锁成功，并执行业务代码。是因为有看门狗。

如果负责储存这个分布式锁的Redisson节点宕机以后，而且这个锁正好处于锁住的状态时，这个锁会出现锁死的状态。为了避免这种情况的发生，**Redisson内部提供了一个监控锁的看门狗，它的作用是在Redisson实例被关闭前，不断的延长锁的有效期。**默认情况下，看门狗的检查锁的超时时间是30秒钟，也可以通过修改[Config.lockWatchdogTimeout](https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95#lockwatchdogtimeout%E7%9B%91%E6%8E%A7%E9%94%81%E7%9A%84%E7%9C%8B%E9%97%A8%E7%8B%97%E8%B6%85%E6%97%B6%E5%8D%95%E4%BD%8D%E6%AF%AB%E7%A7%92)来另行指定。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906642.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906643.png)

![为了避免死锁，**Redisson内部提供了一个监控锁的看门狗，它的作用是在Redisson实例被关闭前，不断的延长锁的有效期。**](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906644.png)

为了避免死锁，**Redisson内部提供了一个监控锁的看门狗，它的作用是在Redisson实例被关闭前，不断的延长锁的有效期。**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906645.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906646.png)

# P161、 缓存分布式锁- Redisson-lock看门狗原理-redisson如何解决死锁

```lua
@ResponseBody
@GetMapping("/hello")
publicString hello() {
//基于Redis的Redisson分布式可重入锁RLock
// Java对象实现了java.util.concurrent.locks.Lock接口。
// 1、获取一把锁，只要锁的名字一样，就是同一把锁
RLock lock =redisson.getLock("my-lock");

// 2、加锁
//    lock.lock();   //阻塞式等待。 默认加的锁都是30s的时间
//1)、锁的自动续期，如果业务超长，运行期间自动给锁续上新的30s。不用担心业务时间长，锁自动过期被删掉
//2)、加锁的业务只要运行完成，就不会给当前锁续期，即使不手动解锁，锁默认在30s以后自动删除。

lock.lock(10, TimeUnit.SECONDS);// 10秒过后自动解锁，自动解锁时间一定要大于业务的执行时间。
//问题：lock.lock(10, TimeUnit.SECONDS);在锁时间到了以后，不会自动续期。
//1、如果我们传递了锁的超时时间，就发送给redis执行脚本，进行占锁，默认超时就是我们指定的时间
//2、如果我们未指定锁的超时时间，就使用30 * 1000【LockWatchdogTimeout看门狗的默认时间】;
//只要占锁成功，就会启动一个定时任务[重新给锁设置过期时间，新的过期时间就是看门狗的默认时间],每隔10s都会自动再次续期，续成30秒
//        internalLockLeaseTime [看门狗时间] / 3, 10s

//Redisson-lock最佳实战
//1)、lock. lock(30, TimeUnit . SECONDS);省掉了整个续期操作。手动解锁
try{
   System.out.println("加锁成功，执行业务..."+ Thread.currentThread().getId());
   Thread.sleep(30000);
}catch(InterruptedException e) {
   e.printStackTrace();
}finally{
// 3、解锁，加锁解锁代码没有运行，redisson会不会出现死锁
System.out.println("释放锁..."+ Thread.currentThread().getId());
   lock.unlock();
}
return"hello";
}
```

# P162、缓存分布式锁-Redisson-读写 锁测试

### **8.5. 读写锁（ReadWriteLock）**

基于Redis的Redisson分布式可重入读写锁`[RReadWriteLock](http://static.javadoc.io/org.redisson/redisson/3.4.3/org/redisson/api/RReadWriteLock.html)` Java对象实现了`java.util.concurrent.locks.ReadWriteLock`接口。其中读锁和写锁都继承了[RLock](https://github.com/redisson/redisson/wiki/8.-%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%92%8C%E5%90%8C%E6%AD%A5%E5%99%A8#81-%E5%8F%AF%E9%87%8D%E5%85%A5%E9%94%81reentrant-lock)接口。

分布式可重入读写锁允许同时有多个读锁和一个写锁处于加锁状态。

```java
RReadWriteLock rwlock = redisson.getReadWriteLock("anyRWLock");
// 最常见的使用方法
rwlock.readLock().lock();
// 或
rwlock.writeLock().lock();
```

大家都知道，如果负责储存这个分布式锁的Redis节点宕机以后，而且这个锁正好处于锁住的状态时，这个锁会出现锁死的状态。为了避免这种情况的发生，Redisson内部提供了一个监控锁的看门狗，它的作用是在Redisson实例被关闭前，不断的延长锁的有效期。默认情况下，看门狗的检查锁的超时时间是30秒钟，也可以通过修改[Config.lockWatchdogTimeout](https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95#lockwatchdogtimeout%E7%9B%91%E6%8E%A7%E9%94%81%E7%9A%84%E7%9C%8B%E9%97%A8%E7%8B%97%E8%B6%85%E6%97%B6%E5%8D%95%E4%BD%8D%E6%AF%AB%E7%A7%92)来另行指定。

另外Redisson还通过加锁的方法提供了`leaseTime`的参数来指定加锁的时间。超过这个时间后锁便自动解开了。

```java
// 10秒钟以后自动解锁
// 无需调用unlock方法手动解锁
rwlock.readLock().lock(10, TimeUnit.SECONDS);
// 或
rwlock.writeLock().lock(10, TimeUnit.SECONDS);

// 尝试加锁，最多等待100秒，上锁以后10秒自动解锁
boolean res = rwlock.readLock().tryLock(100, 10, TimeUnit.SECONDS);
// 或
boolean res = rwlock.writeLock().tryLock(100, 10, TimeUnit.SECONDS);
...
lock.unlock();
```

## 代码：

IndexController：

```java
@Autowired
StringRedisTemplate redisTemplate;

/**
 * 读写锁：保证一定能读到最新数据, 修改期间，写锁是一个排他锁(互斥锁)。读锁是一个共享锁；
 * 写锁没释放，读就必须等待。
 * 测试读写锁：写锁
 *
 * @return
 */
@GetMapping("/write")
@ResponseBody
public String writeValue() {
	RReadWriteLock lock = redisson.getReadWriteLock("rw-lock");
	String s = "";
	// 获取写锁
	RLock rLock = lock.writeLock();
	try {
		// 读写锁的用法：1、改数据加写锁，读数据加读锁
		rLock.lock();
		s = UUID.randomUUID().toString();
		redisTemplate.opsForValue().set("writeValue", s);
		Thread.sleep(30000);
	} catch (InterruptedException e) {
		e.printStackTrace();
	} finally {
		rLock.unlock();
	}
	return s;
}

/**
 * 测试读写锁：读锁
 *
 * @return
 */
@GetMapping("/read")
@ResponseBody
public String readValue() {
	RReadWriteLock lock = redisson.getReadWriteLock("rw-lock");
	String s = "";
	// 获取读锁
	RLock rLock = lock.readLock();
	rLock.lock();
	try {
		s = UUID.randomUUID().toString();
		s = redisTemplate.opsForValue().get("writeValue");
		Thread.sleep(30000);
	} catch (InterruptedException e) {
		e.printStackTrace();
	} finally {
		rLock.unlock();
	}
	return s;
}
```

启动这俩服务：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906647.png)

在Redis中手动添加key：writeValue   value：11111

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906648.png)

浏览器输入：[http://localhost:10000/read](http://localhost:10000/read)测试读锁

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906649.png)

读写锁：保证一定能读到最新数据,修改期间，写锁是一个排他锁(互斥锁)。读锁是一个共享锁；

写锁没释放，读锁就必须等待。

[localhost:10000/write](http://localhost:10000/write) 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906650.png)

# P163、 缓存-分布式锁- Redisson-读写锁补充

读写锁：保证一定能读到最新数据，修改期间，写锁是一个排他锁(互斥锁，独享锁)。读锁是一个共享锁；

写锁没释放，读就必须等待。

如果是先写（锁）——>在读（锁）：读的时候就必须等待写锁释放

读+读： 相当于无锁，并发读，只会在redis中记录好，所有当前的读锁。他们都会同时加锁成功

写+读： 等待写锁释放

写+写： 阻塞方式

读+写： 有读锁，写也需要等待。会不会写等待（会）？

> 总结：只要有写锁的存在，都必须等待。
> 
- IndexController代码
  
    ```java
    @Autowired
    StringRedisTemplate redisTemplate;
    
    /**
    	 * 读写锁：保证一定能读到最新数据, 修改期间，写锁是一个排他锁(互斥锁，独享锁)。读锁是一个共享锁；
    	 * 写锁没释放，读就必须等待。
    	 *
    	 * 如果是先写（锁）——>在读（锁）： 读的时候就必须等待写锁释放
    	 * 读 ——> 读： 相当于无锁，并发读，只会在redis中记录好，所有当前的读锁。他们都会同时加锁成功
    	 * 写 ——> 读： 等待写锁释放
    	 * 写 ——> 写： 阻塞方式
    	 * 读 ——> 写： 有读锁，写也需要等待。会不会写等待（会）？
    	 * 总结：只要有写锁的存在，都必须等待。
    	 * 测试读写锁：写锁
    	 *
    	 * @return
    	 */
    	@GetMapping("/write")
    	@ResponseBody
    	public String writeValue() {
    		RReadWriteLock lock = redisson.getReadWriteLock("rw-lock");
    		String s = "";
    		// 获取写锁
    		RLock rLock = lock.writeLock();
    		try {
    			// 读写锁的用法：1、改数据加写锁，读数据加读锁
    			rLock.lock();
    			System.out.println("写锁加锁成功..." + Thread.currentThread().getId());
    			s = UUID.randomUUID().toString();
    			Thread.sleep(30000);
    			redisTemplate.opsForValue().set("writeValue", s);
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		} finally {
    			rLock.unlock();
    			System.out.println("写锁释放" + Thread.currentThread().getId());
    		}
    		return s;
    	}
    
    	/**
    	 * 测试读写锁：读锁
    	 *
    	 * @return
    	 */
    	@GetMapping("/read")
    	@ResponseBody
    	public String readValue() {
    		RReadWriteLock lock = redisson.getReadWriteLock("rw-lock");
    		String s = "";
    		// 获取读锁
    		RLock rLock = lock.readLock();
    		rLock.lock();
    		try {
    			System.out.println("读锁加锁成功" + Thread.currentThread().getId());
    			s = redisTemplate.opsForValue().get("writeValue");
    			Thread.sleep(30000);
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		} finally {
    			rLock.unlock();
    			System.out.println("读锁释放" + Thread.currentThread().getId());
    		}
    		return s;
    	}
    ```
    

读读可以，读写互斥，写写互斥，先读后写，会导致数据不一致。

![先读后写](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906651.png)

先读后写

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906652.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906653.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906654.png)

并发操作：*`读 ——> 读： 相当于无锁，并发读，只会在redis中记录好，所有当前的读锁。他们都会同时加锁成功`*

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906655.png)

# P165、 缓存分布式锁Redisson-信号量测试【与P64顺序反了】

### **[8.6. 信号量（Semaphore）](https://github.com/redisson/redisson/wiki/8.-%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%92%8C%E5%90%8C%E6%AD%A5%E5%99%A8#86-%E4%BF%A1%E5%8F%B7%E9%87%8Fsemaphore)**

基于Redis的Redisson的分布式信号量（[Semaphore](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RSemaphore.html)）Java对象`RSemaphore`采用了与`java.util.concurrent.Semaphore`相似的接口和用法。同时还提供了[异步（Async）](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RSemaphoreAsync.html)、[反射式（Reactive）](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RSemaphoreReactive.html)和[RxJava2标准](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RSemaphoreRx.html)的接口。

```java
RSemaphore semaphore = redisson.getSemaphore("semaphore");
semaphore.acquire();
//或
semaphore.acquireAsync();
semaphore.acquire(23);
semaphore.tryAcquire();
//或
semaphore.tryAcquireAsync();
semaphore.tryAcquire(23, TimeUnit.SECONDS);
//或
semaphore.tryAcquireAsync(23, TimeUnit.SECONDS);
semaphore.release(10);
semaphore.release();
//或
semaphore.releaseAsync();
```

- IndexController代码
  
    ```java
    /**
     * 信号量（也可以用作分布式限流）：https://github.com/redisson/redisson/wiki/8.6. 信号量（Semaphore）
     * 车库停车
     * 3车位
     *
     * @return
     */
    @GetMapping("/park")
    @ResponseBody
    public String park() throws InterruptedException {
    	// 分布式信号量的名字，只要名字相同都是同一个信号量
    	RSemaphore park = redisson.getSemaphore("park");
    	// 获取一个信号（也可以理解为获取一个值，或占一个车位）
    //			park.acquire();	//阻塞式等待
    	boolean b = park.tryAcquire();
    	if (b) {
    		// 执行业务
    	} else {
    		// 提示错误信息
    		return "error";
    	}
    	return "ok=>" + b;
    }
    
    /**
     * 车开走的方法
     *
     * @return
     */
    @GetMapping("/go")
    @ResponseBody
    public String go() {
    	RSemaphore park = redisson.getSemaphore("park");
    	// 释放一个车位
    	park.release();
    	return "ok";
    }
    ```
    

设置初始值：value=3

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906656.png)

执行停车操作：[http://localhost:10000/park](http://localhost:10000/park) 执行三次停车操作，value变为0，此时在执行停车操作就会转圈圈一直等待。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906657.png)

指导执行[http://localhost:10000/go](http://localhost:10000/go)车开走的方法，value值会自增1，释放车位

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906658.png)

# P164、缓存分布式锁- Redisson-闭锁测试（JUC）

[8. 分布式锁和同步器 · redisson/redisson Wiki](https://github.com/redisson/redisson/wiki/8.-%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%92%8C%E5%90%8C%E6%AD%A5%E5%99%A8#88-%E9%97%AD%E9%94%81countdownlatch)

## **8.8. 闭锁（CountDownLatch）JUC**

基于Redisson的Redisson分布式闭锁（[CountDownLatch](http://static.javadoc.io/org.redisson/redisson/3.10.0/org/redisson/api/RCountDownLatch.html)）Java对象`RCountDownLatch`采用了与`java.util.concurrent.CountDownLatch`相似的接口和用法。

```java
RCountDownLatch latch = redisson.getCountDownLatch("anyCountDownLatch");
latch.trySetCount(1);
latch.await();

// 在其他线程或其他JVM里
RCountDownLatch latch = redisson.getCountDownLatch("anyCountDownLatch");
latch.countDown();
```

- IndexController代码
  
    ```java
    /**
     * 8.8. 分布式：闭锁（CountDownLatch）
     * 举例：放假锁门，1班没人了，2班没人了.....共五个班
     * 5个班级的人全部都走完了，我们才可以锁大门。理解成打团，人数必须够才能开团。
     */
    @GetMapping("/lockDoor")
    @ResponseBody
    public String lockDoor() throws InterruptedException {
    	RCountDownLatch door = redisson.getCountDownLatch("door");
    	// 设置成5个班
    	door.trySetCount(5);
    	// 等待闭锁都完成
    	door.await();
    	return "放假了...";
    }
    
    @GetMapping("/gogogo/{id}")
    @ResponseBody
    public String gogogo(@PathVariable("id") Long id) {
    	RCountDownLatch door = redisson.getCountDownLatch("door");
    	// 计数减一：走一个就减掉一个
    	door.countDown();
    	return id + "班的人都走了...";
    }
    ```
    

设置key=door，value=5（表示五个班级）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906659.png)

[localhost:10000/gogogo/1](http://localhost:10000/gogogo/1)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906660.png)

[localhost:10000/gogogo/2](http://localhost:10000/gogogo/2)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906661.png)

[localhost:10000/lockDoor](http://localhost:10000/lockDoor)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906662.png)

# P166、缓存分布式锁-缓存一致性解决

redis中的底层的所有锁都保证了原子性，底层方法都是lua脚本。

## 缓存数据一致性-双写模式

写完数据库紧接着写缓存。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906663.png)

由于卡顿等原因，导致写缓存2在最前，写缓存1在后面就出现了不一致；

脏数据问题：这是暂时性的脏数据问题，但是在数据稳定，缓存过期以后，又能得到最新的正确数据——读到的最新数据有延迟：最终一致性。

## 缓存数据一致性-失效模式

写完数据库就删缓存。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906664.png)

### 我们系统的一致性解决方案：

1、缓存的所有数据都有过期时间，数据过期下一次查询触发主动更新

2、读写数据的时候，加上分布式的读写锁。经常写，经常读。

## 缓存数据一致性-解决方案

无论是双写模式还是失效模式，都会导致缓存的不一致问题。即多个实例同时更新会出事。怎么办？

1、如果是用户纬度数据（订单数据、用户数据），这种并发几率非常小，不用考虑这个问题，缓存数据加上过期时间，每隔一段时间触发读的主动更新即可。

2、如果是菜单，商品介绍等基础数据，也可以去使用[canal订阅](https://www.notion.so/16_-redis-Redisson-lock-9a0f5fa52a8545be9cba8de1531a5300)binlog的方式。

3、缓存数据+过期时间也足够解决大部分业务对于缓存的要求。

4、通过加锁保证并发读写，写写的时候按顺序排好队。读读无所谓。所以适合使用读写锁。（业务不关心
脏数据，允许临时脏数据可忽略）；

### 缓存一致性总结：

- 我们能放入缓存的数据本就不应该是实时性、一致性要求超高的。所以缓存数据的时候加上过期时间，保证每天拿到当前最新数据即可。
- 我们不应该过度设计，增加系统的复杂性
- 遇到实时性、一致性要求高的数据，就应该查数据库，即使慢点

## 缓存数据一致性-解决-Canal（阿里开源中间件）

canal：可以理解成MySQL的一个从库，MySQL更新了，canal就会跟着更新呢

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906665.png)

# P167、分布式缓存-SpringCache简介

每次都那样写缓存太麻烦了，spring从3.1开始定义了Cache、CacheManager接口来统一不同的缓存技术。并支持使用JCache(JSR-107)注解简化我们的开发

Cache接口的实现包括RedisCache、EhCacheCache、ConcurrentMapCache等

每次调用需要缓存功能的方法时，spring会检查检查指定参数的指定的目标方法是否已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。

使用Spring缓存抽象时我们需要关注以下两点：

1、确定方法需要缓存以及他们的缓存策略

2、从缓存中读取之前缓存存储的数据

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906666.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906667.png)

## Spring官网关于Cache相关链接

[Integration](https://docs.spring.io/spring-framework/docs/5.2.19.RELEASE/spring-framework-reference/integration.html#cache)

# P168、缓存SpringCache-整合&体验@Cacheable（整合步骤）

8、整合SpringCache简化开发

1、引入依赖
spring-boot-starter-cache、spring-boot-starter-data-redis
2、写配置

(1)、自动配置了哪些

CacheAutoConfiguration会导入RedisCacheConfiguration;

自动配好了缓存管理器RedisCacheManager

     (2)、配置使用redis作为缓存spring.cache.type=redis
    
     (3)、测试使用缓存：几个常用注解
    
      @Cacheable:Triggers cache population.:触发将数据保存到缓存的操作
    
      @CacheEvict:Triggers cache eviction.:触发将数据从缓存删除的操作
    
      @CachePut:Updates the cache without interfering with the method execution.:不影响方法执行更新缓存
    
      @Caching:Regroups multiple cache operations to be applied on a method.:组合以上多个操作
    
      @CacheConfig:Shares some common cache-related settings at class-level.:在类级别共享缓存的相同配置

1、开启缓存功能@EnableCaching

2、只需要使用注解就能完成缓存操作

引入Cache的pom依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

因为我们使用redis做缓存，故须引入redis的依赖（之前已经引入过，不用再次引入，仅作标记）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

启动类上添加：**@EnableCaching 缓存注解**

```java
**@EnableCaching**
@EnableFeignClients(basePackages = "com.atguigu.gulimall.product.feign")
@EnableDiscoveryClient
@MapperScan("com.atguigu.gulimall.product.dao")
@SpringBootApplication
public class GulimallProductApplication {

    public static void main(String[] args) {
        SpringApplication.run(GulimallProductApplication.class, args);
    }

}
```

新建application.properties文件中添加

```java
spring.cache.type=redis
#spring.cache.cache-names=qq,
```

测试：IndexController

```java
@GetMapping({"/", "/index.html"})
public String indexPage(Model model) {

	System.out.println(""+Thread.currentThread().getId());
	//TODO 1、查出所有的一级分类
	List<CategoryEntity> categoryEntities = categoryService.getLevel1Categorys();

	// 视图解析器进行拼串
	// 源码中含有默认后缀：classpath:/templates/
	// 源码中含有默认后缀：.html    然后进行拼串

	model.addAttribute("categorys", categoryEntities);
	return "index";         //所以我们只需返回一个index即可
}
```

CategoryServiceImpl

```java
// 我们使用springCache的时候，每一个需要缓存的数据我们都来指定要放到的那个名字的缓存。【缓存的分区（推荐按照业务类型分）】
@Cacheable({"category"})	//这个注解代表当前方法的结果需要缓存，如果缓存中有，方法就不用调用。如果缓存中没有，会调用方法，最终将方法的结果放入缓存。
@Override
public List<CategoryEntity> getLevel1Categorys() {
	System.out.println("getLevel1Categorys......");
	long l = System.currentTimeMillis();
	//selectList查询集合
	List<CategoryEntity> categoryEntities = baseMapper.selectList(new QueryWrapper<CategoryEntity>().eq("parent_cid", 0));

	return categoryEntities;
}
```

我们清空redis中的缓存，然后执行一边方法，发现缓存中正常存入缓存数据，控制台也在之前没有缓存的时候正常调用方法查询数据库，控制台打印	System.out.println("getLevel1Categorys......");

将数据库中的数据放入缓存，在第二次查询的时候，发现缓存中有数据，就不会执行查询数据库的方法体。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906668.png)

# P169、 缓存SpringCache- @Cacheable细节设置

```java
packagecom.atguigu.gulimall.product;

importorg.mybatis.spring.annotation.MapperScan;
importorg.springframework.boot.SpringApplication;
importorg.springframework.boot.autoconfigure.SpringBootApplication;
importorg.springframework.cache.annotation.EnableCaching;
importorg.springframework.cloud.client.discovery.EnableDiscoveryClient;
importorg.springframework.cloud.openfeign.EnableFeignClients;

/**
 * 1.整合MyBatis-Plus
 *      1.导入依赖：
*<dependency>
 *<groupId>com.baomidou</groupId>
 *<artifactId>mybatis-plus-boot-starter</artifactId>
 *<version>3.3.2</version>
 *</dependency>
 *      2.配置：参照MyBatis-Plus官网
*          1.配置数据源
*            1).导入数据库驱动
*            2).在application.yml配置数据源相关信息
*          2.配置MyBatis-Plus相关信息
*            1).使用@MapperScan
 *            2).告诉MyBatis-Plus，sql映射文件位置在哪里
*
 * 2、逻辑删除
* 1)、配置全局的逻辑删除规则(省略)
 * 2)、配置逻辑删除的组件Bean (省略)
 * 3)、给Bean加上逻辑删除注解@TableLogic
 *
 *
 * 3、JSR303
 *      1)、给Bean添加校验注解: javax. val idation. constraints,并定义自己的message提示
*      2)、开启校验功能@Valid
 *效果:校验错误以后会有默认的响应;
 *      3)、给校验的bean后紧跟一个BindingResult,就可以获取到校验的结果
*      4)、分组校验(用来完成多场景的复杂校验)
 *          1）、@NotBlank(message = "品牌名必须提交", groups = {AddGroup.class, UpdateGroup.class})
 *给校验注解标注什么情况需要进行校验
*          2）、在controller中添加注解@Validated({AddGroup.class}
 *          3)、默认没有指定分组的校验在解@NotBlank,在分组校验情况@Validated({AddGroup.class}下不生效，只会在@Validated生效;
 * 4、统一的异常处理
*@ControllerAdvice
*      1)、编写异常处理类，使用@ControllerAdvice。
*      2)、使用@ExceptionHandler标注方法可以处理的异常。
*
 * 5)、自定义校验
*    1)、编写一个自定义的校验注解
*    2)、编写一个自定义的校验器ConstraintValidator
 *    3)、关联自定义的校验器和自定义的校验注解
*@Documented
*@Constraint(validatedBy= {ListValueConstraintValidator.class})【可以指定多个不同的校验器，适配不同类型的校验】
*@Target({METHOD,FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
 *@Retention(RUNTIME)
*
 * 5、模板引擎
*      1)、thymeleaf-starter:关闭缓存（为了能够实时显示数据）
*      2)、静态资源都放在static文件夹下就可以按照路径直接访问
*      3)、页面放在templates下，直接访问
*              SpringBoot,访问项目的时候，默认会找index
 *      4)、如果页面修改之后不想重启服务器，而且还要实时更新页面内容怎么办？
*          1、引入dev-tools
 *          2)、修改元贝面controller shift f9重新自动编详下贝面，如果是代码配置，还是推荐重启服务
*
 * 6、整合redis
 *      1)、引入data-redis-starter
 *      2)、简单配置redis的host等信息
*      3)、使用SpringBoot自动配置好的StringRedisTemplate来操作redis
 *      redis-》(简单理解为一个map)Map;存放数据key,数据值value
 *
 * 7、整合redisson作为分布式锁等功能框架
*      1）、引入redisson依赖（暂时引入的是原生的依赖包，还需要配置；想省事引入redisson-starter）
*<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
*<dependency>
 *<groupId>org.redisson</groupId>
 *<artifactId>redisson</artifactId>
 *<version>3.12.0</version>
 *</dependency>
 *      2)、配置redisson(参考官方文档：https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95)
 * 8、整合SpringCache简化开发
*      1、引入依赖
*          spring-boot-starter-cache、spring-boot-starter-data-redis
*       2、写配置
*          (1)、自动配置了哪些
*              CacheAutoConfiguration会导入RedisCacheConfiguration;
 *自动配好了缓存管理器RedisCacheManager
 *          (2)、配置使用redis作为缓存spring.cache.type=redis
 *          (3)、测试使用缓存：几个常用注解
*@Cacheable:Triggers cache population.:触发将数据保存到缓存的操作
*@CacheEvict:Triggers cache eviction.:触发将数据从缓存删除的操作
*@CachePut:Updates the cache without interfering with the method execution.:不影响方法执行更新缓存
*@Caching:Regroups multiple cache operations to be applied on a method.:组合以上多个操作
*@CacheConfig:Shares some common cache-related settings at class-level.:在类级别共享缓存的相同配置
*              1、开启缓存功能@EnableCaching
 *              2、只需要使用注解就能完成缓存操作
*
 */
@EnableCaching
@EnableFeignClients(basePackages ="com.atguigu.gulimall.product.feign")
@EnableDiscoveryClient
@MapperScan("com.atguigu.gulimall.product.dao")
@SpringBootApplication
public classGulimallProductApplication {

public static voidmain(String[] args) {
        SpringApplication.run(GulimallProductApplication.class, args);
    }

}

```

# P170、缓存- SpringCache-自定义缓存配置



## 原理:

CacheAutoConfiguration -> RedisCacheConfiguration 自动配置了RedisCacheManager- >初始化所有的缓存->每个缓存决定使用什么配置

->如果redisCacheConfiguration有就用已有的，没有就用默认配置

->想改缓存的配置，只需要给容器中放一个RedisCacheConfiguration即 可

->就会应用到当前RedisCacheManager管理的所有缓存分区中

## 代码

- 新建MyCacheConfig
  
    ```java
    package com.atguigu.gulimall.product.config;
    
    import com.alibaba.fastjson.support.spring.GenericFastJsonRedisSerializer;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.autoconfigure.cache.CacheProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.cache.annotation.EnableCaching;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.data.redis.cache.RedisCacheConfiguration;
    import org.springframework.data.redis.serializer.RedisSerializationContext;
    import org.springframework.data.redis.serializer.StringRedisSerializer;
    
    /**
     * @Author Dali
     * @Date 2022/3/19 9:37
     * @Version 1.0
     * @Description: 自己配置redisCacheConfiguration
     * @Configuration
     * @EnableCaching：开启缓存注解
     * @EnableConfigurationProperties ： 开启属性的绑定功能
     */
    @Configuration
    @EnableCaching
    @EnableConfigurationProperties(CacheProperties.class)
    public class MyCacheConfig {
    
    	// 方式一
    	@Autowired
    	CacheProperties cacheProperties;
    
    	@Bean
    	RedisCacheConfiguration redisCacheConfiguration(CacheProperties cacheProperties) { // 方式二
    
    		RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig();
    //		config = config.entryTtl()
    		config = config.serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()));
    		config = config.serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericFastJsonRedisSerializer()));
    
    		CacheProperties.Redis redisProperties = cacheProperties.getRedis();
    		// 将配置文件中的所有配置都生效
    		if (redisProperties.getTimeToLive() != null) {
    			config = config.entryTtl(redisProperties.getTimeToLive());
    		}
    		if (redisProperties.getKeyPrefix() != null) {
    			config = config.prefixKeysWith(redisProperties.getKeyPrefix());
    		}
    		if (!redisProperties.isCacheNullValues()) {
    			config = config.disableCachingNullValues();
    		}
    		if (!redisProperties.isUseKeyPrefix()) {
    			config = config.disableKeyPrefix();
    		}
    		return config;
    	}
    }
    ```
    

记得清空缓存浏览器访问：

[](http://localhost:10000/)

TTL使用了我们自定义的配置，value值也为json格式的了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906669.png)

针对spring-cache以下注解可以解决缓存穿透问题：

```java
#是否缓存空值，可以防止缓存穿透
spring.cache.redis.cache-null-values=true
```

```json
spring.cache.type=redis
#spring.cache.cache-names=qq,  ttl存活时间已毫秒为单位
spring.cache.redis.time-to-live=3600000
#如果指定了前缀就用我们指定的前缀，如果没有就默认使用缓存的名字作为前缀
spring.cache.redis.key-prefix=CACHE_
spring.cache.redis.use-key-prefix=true
#是否缓存空置，可以防止缓存穿透
spring.cache.redis.cache-null-values=true
```

清空缓存再次gulimamll-product重启服务，浏览器访问[http://localhost:10000/](http://localhost:10000/)  查看缓存

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/202206030906671.png)

# 171、 缓存- SpringCache- @CacheEvict 缓存失效模式

## 启动服务：

1. 启动虚拟主机，连接虚拟机，连接名vagrant/vagrant  地址：192.168.56.10
2. 启动nacos
3. 启动前端页面：`npm run dev`  [人人快速开发平台](http://localhost:8001/#/login)
4. 启动后端服务：（访问gulimall.com的时候记得关闭防火墙）

GulimallGatewayApplication :88/
GulimallProductApplication [devtools]
RenrenApplication :8080/

## 需要给CacheEvict注解的key加单引号，保证SpEL语法正确

`@CacheEvict(value = "category", key = "'getLevel1Categorys'")`

## @CacheEvict 缓存失效模式：

1、同时进行多种缓存操作：可以使用 @Caching进行组装缓存操作逻辑

2、指定删除某个分区下的所有数据：   @CacheEvict(value = "category", allEntries = true)，对缓存进行分区，方便后期批量对同属性缓存进行操作

3、存储同一类型的数据，都可以指定成同一个分区。分区名默认就是缓存的前缀。

CategoryServiceImpl.java

```java
// 需要给CacheEvict注解的key加单引号，保证SpEL语法正确, 但是如果有多个缓存我们想要在更新的时候同时清空这两组缓存，需要使用@Caching()注解
//	@CacheEvict(value = "category", key = "'getLevel1Categorys'")
/*	@Caching(evict = {
			@CacheEvict(value = "category", key = "'getLevel1Categorys'"),
			@CacheEvict(value = "category", key = "'getCatalogJson'")
	})*/
	//          category分区，里面的所有数据（true）
	@CacheEvict(value = "category", allEntries = true)	//失效模式
//	@CachePut	//双写模式使用这个注解
```

```java
@Cacheable(value = "category", key = "#root.methodName")
@Override
public Map<String, List<Catelog2Vo>> getCatalogJson() {
	System.out.println("查询类数据库......");
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

。。。。。。
//	@Override
public Map<String, List<Catelog2Vo>> getCatalogJson2() {

```

# P172、缓存-SpringCache原理与不足



## 使用SpringCache缓存的不足：

### 1、读模式：

1. 缓存穿透：查询一个null数据，解决：我们可以通过配置文件的方式让其可以缓存空数据
    1. 比如：`spring.cache.redis.cache-null-values=true` 
1. 缓存击穿：大量并发进来同时查询一个正好过期的数据。解决：加锁；但是**SpringCaChe**默认是无锁的，我们可以通过`@Cacheable(value = {"category"}, key = "#root.method.name", sync = true) 来解决`击穿问题
2. 缓存雪崩：当量key同时过期。解决：加随机时间。加上过期时间。
    1. 配置文件设置过期时间：`spring.cache.redis.time-to-live=3600000` 

### 2、写模式：（缓存与数据库一致）

1. 读写加锁
2. 引入Canal，感知MySQL的更新去更新数据库
3. 都多写多，直接去数据库中查询就行

## 总结：

常规数据（读多写少，及时性，一致性要求不高的数据）完全可以使用SpringCache，写模式（只要缓存的数据有过期时间就足够了）