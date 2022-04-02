[TOC]



# Redis 特性

+   键值（key-value）型，value支持多种不同数据结构，功能丰富
+   单线程，每个命令具备原子性，且避免了多线程之间通信的额外代价（Redis 6.0以后的多线程仅仅是网络部分多线程，核心存储部分仍然是单线程）
+   低延迟，速度快（**基于内存**、IO多路复用、良好的编码）
+   支持数据持久化
+   支持主从集群、分片集群
+   支持多语言客户端



# key的结构

Redis的key允许有多个单词形成层级结构，多个单词之间用`:`隔开，格式如下：

```
项目名：业务名：类型：id

taobao:user:1
taobao:product:1
```

这个格式并非固定，可以根据自己的需求来删除或添加词条

如果 value 是一个java对象，则可以将对象序列化为JSON字符串后存储：

|        KEY        |                  VALUE                   |
| :---------------: | :--------------------------------------: |
| **taobao:user:1** | **{“id”: 1, “name”: ”Jack”, “age”: 21}** |



# Redis 简单操作

使用如下命令进入redis控制台

```powershell
redis-cli
```

使用如下命令进行写操作，如写入 key 为 name，value 为 andre 的字段

```powershell
set name andre
```

使用如下命令进行读操作，如获取 key 为 name 的字段

```powershell
get name
```

使用如下命令进行数据库的切换，如切换到初始的 db0

```powershell
select 0
```





# Redis 通用命令

## HELP [command]	查看指令的说明

## KEYS	查看符合模式的key

*代表多个字符，?代表一个字符

由于效率问题，不建议在生产环境中使用 `keys *`命令

```
KEYS pattern
```

## DEL	删除该key的记录，可删除多个

使用后返回操作成功记录的条数

```
DEL key [key ...]
```

删除多个时使用并列的写法，如

```powershell
del name age sex
```

## EXISTS	查看是否存在该key的记录

返回 1 即为存在，0 为不存在

可以使用并列写法查看多个

```
EXISTS key [key ...]
```

## EXPIRE	为该key设置有效期，到期时被删除

由于redis是存储在内存中的，因此为了避免内存占用过多有时会设置key的存活时间，让其过期自动删除

```
EXPIRE key seconds
```

## TLL	查看该key的存活时间

返回该key的有效时间

返回 -1 时说明该key是永久有效，不会被自动删除

```
TTL key
```





# Redis 数据结构

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220308145329220.png" alt="image-20220308145329220" style="zoom:80%;" />





## String 字符串

字符串类型，redis 中最简单的存储类型

根据字符串的格式不同，又可以分为3类：

+   string ：普通字符串

+   int ：整数类型。可以做自增、自减操作
+   float ：浮点类型，可以做自增、自减操作
+   

### String 常见命令

#### SET	添加或者修改已经存在的一个String类型的键值对

```
SET key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] [GET]
```

#### GET	根据 key 获取String类型的 value

```
GET key
```

#### MSET	批量添加多个String类型的键值对

```powershell
MSET key value [key value ...]

# 例如
mset name1 tom name2 bob name3 amy
```

#### MGET	根据多个key获取多个String类型的value

```powershell
MGET key [key ...]

# 例如
mget name1 name2 name3
```

#### INCR	让一个整型的key的值自增1

```
INCR key
```

#### INCRBY	让一个整型的key的值自增/自减并指定步长

```powershell
INCRBY key increment

# 例如，num 自增 2、让 age 自减 -1 
incrby num 2
incrby age -1
```

#### INCRBYFLOAT	让一个浮点类型的key的值自增并指定步长

```
INCRBYFLOAT key increment
```

#### SETNX	添加一个String类型的键值对，前提是这个key不存在，否则不执行

```powershell
SETNX key value

# 这是一个组合命令
setnx name jack
set name jack nx
```

#### SETEX	添加一个String类型的键值对，并且指定有效期

```powershell
SETEX key seconds value

# 这是一个组合命令
setex name 10 jack
set name jack ex 10
```



## Hash 字典

也称散列类型，其 value 是一个无序字典，类似于 java 中的 HashMap 结构

使用String类型将对象存储为JSON字符串时，当需要修改对象的某个字段时很不方便

使用Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220308161212390.png" alt="image-20220308161212390" style="zoom:80%;" />



### Hash 常见命令

#### HSET	添加或者更新Hash类型的 key 的 field 值

```
HSET key field value [field value ...]
```

#### HGET	获取一个Hash类型的 key 的 field 的值

```
HGET key field
```

#### HMSET	批量添加多个Hash类型的 key 的 field 的值

```
HMSET key field value [field value ...]
```

#### HMGET	批量获取多个Hash类型的 key 的 field 的值

```
HMGET key field [field ...]
```

#### HGETALL	获取一个Hash类型的 key 中所有的 field 和 value 的值

```
HGETALL key
```

#### HKEYS	获取一个Hash类型的 key 中所有的 field 的值

```
HKEYS key
```

#### HVALS	获取一个Hash类型的 key 中所有的 value 的值

```
HVALS key
```

#### HINCRBY	让一个Hash类型的 key 的字段值按照指定步长自增/自减

```
HINCRBY key field increment
```

#### HSETNX	添加一个Hash类型的 key 的 field 的值，前提是该 field 不存在，否则不执行

```
HSETNX key field value
```





## List 双向链表

与 java 中的 LinkedList 类似，可以看做是一个双向链表结构，既可以支持正向检索也可以支持反向检索

特征也与 LinkedList 类似：

+   有序
+   元素可以重复
+   插入和删除快
+   查询速度一般



### List 常见命令

#### LPUSH	向列表左侧插入一个或多个元素

```
LPUSH key element [element ...]
```

#### LPOP	移除并返回列表左侧的第一个元素

```
LPOP key [count]
```

#### RPUSH	向列表右侧插入一个或多个元素

```
RPUSH key element [element ...]
```

#### RPOP	移除并返回列表右侧的第一个元素

```
RPOP key [count]
```

#### LRANGE	返回列表中从0开始的 [start, end) 范围内的所有元素，从左向右输出

```
LRANGE key start stop
```

#### BLPOP 和 BRPOP	与 LPOP 和 RPOP 类似，只不过在没有元素时阻塞等待出现某个可用的元素，而不是直接返回 nil

这个命令还会返回阻塞等待的时长

```
BLPOP key [key ...] timeout
BRPOP key [key ...] timeout
```



### 使用List结构模拟栈

出口和入口同侧

### 使用List结构模拟队列

出口和入口不同侧

### 使用List结构模拟阻塞队列

出口和入口不同侧

出队时采用 blpop 或 brpop





## Set 集合

与 java 中的 HashSet 类似，其特征有：

+   无序
+   元素不可重复
+   查找快
+   支持交集、并集、差集等功能



### Set 常见命令

#### SADD	向set中添加一个或多个元素

```
SADD key member [member ...]
```

#### SREM	移除set中的指定元素

```
SREM key member [member ...]
```

#### SCARD	返回set中元素的个数

```
SCARD key
```

#### SISMEMBER	判断一个元素是否存在于set中

```
SISMEMBER key member
```

#### SMEMBERS	获取set中所有的元素

```
SMEMBERS key
```

#### SINTER	求多个set的交集

```
SINTER key [key ...]
```

#### SDIFF	求多个set的差集

```
SDIFF key [key ...]
```

#### SUNION	求多个set的并集

```
SUNION key [key ...]
```





## SortedSet 有序集合

与 java中的 TreeSet 类似，但底层数据结构差异很大。SortedSet中的每一个元素都带有一个 score 属性，可以基于 score 属性对元素排序，底层的实现是一个跳表+hash表

SortedSet具备下列特性：

+   可排序
+   元素不重复
+   查询速度快

因为 SortedSet的可排序特性，经常被用来实现排行榜这样的功能



### SortedSet 常见命令

#### ZADD	添加一个或多个元素到sorted set，如果已经存在则更新其 score 值

```
ZADD key [NX|XX] [GT|LT] [CH] [INCR] score member [score member ...]
```

#### ZREM	删除sorted set中一个或多个指定元素

```
ZREM key member [member ...]
```

#### ZSCORE	获取sorted set中指定元素的 score 值，如果元素不存在返回nil，因此可以用来判断是否存在元素

```
ZSCORE key member
```

#### ZRANK	获取sorted set中的指定元素的排名，从0开始，升序

```
ZRANK key member
```

#### ZREVRANK	获取sorted set中的指定元素的排名，从0开始，降序

```
ZREVRANK key member
```

#### ZCARD	获取sorted set中的元素个数

```
ZCARD key
```

#### ZCOUNT	统计 score 值在 [min, max] 范围内的所有元素的个数

```
ZCOUNT key min max
```

#### ZINCRBY	让sorted set中的指定元素自增，步长为指定的 increment 值

```
ZINCRBY key increment member
```

#### ZRANGE	按照 score 排序后，获取 [min, max] 范围内的元素，从0开始，升序

```
ZRANGE key min max [BYSCORE|BYLEX] [REV] [LIMIT offset count] [WITHSCORES]
```

#### ZREVRANGE	按照 score 排序后，获取 [min, max] 范围内的元素，从0开始，降序

```
ZREVRANGE key start stop [WITHSCORES]
```

#### ZRANGEBYSCORE	按照 score 排序后，获取 score 在 [min, max] 范围内的元素

```
ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
```

#### ZDIFF、ZINTER、ZUNION	求差集、交集、并集

```
ZDIFF numkeys key [key ...] [WITHSCORES]
ZINTER numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX] [WITHSCORES]
ZUNION numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX] [WITHSCORES]
```





## GEO 地理坐标

GEO 就是 Geolocation 的简写形式，代表地理坐标。Redis 在 3.2 版本中加入了对 GEO 的支持，允许存储地理坐标信息，来帮助我们根据经纬度来检索数据

### GEO 常见命令

#### GEOADD	添加一个地理空间信息，包含 经度（longitude）、维度（latitude）、值（number）

```powershell
GEOADD key [NX|XX] [CH] longitude latitude member [longitude latitude member ...]
```

#### GEODIST	计算指定的两个点之间的距离并返回

```powershell
GEODIST key member1 member2 [m|km|ft|mi]
```

#### GEOHASH	将指定 member 的坐标转为 Hash 字符串形式并返回

```powershell
GEOHASH key member [member ...]
```

#### GEOPOS	返回指定的 member 的坐标

```powershell
GEOPOS key member [member ...]
```

#### GEORADIUS	指定圆心、半径，找到该圆内包含的所有 member，并按照与圆心之间的距离排序后返回*（6.2以后已废弃）*

```powershell
GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count [ANY]] [ASC|DESC] [STORE key] [STOREDIST key]
```

#### GEOSEARCH	在指定范围内搜索 member，并按照与指定点之间的距离排序后返回。范围可以是圆形或者矩形*（6.2的新功能）*

```powershell
GEOSEARCH key [FROMMEMBER member] [FROMLONLAT longitude latitude] [BYRADIUS radius m|km|ft|mi] [BYBOX width height m|km|ft|mi] [ASC|DESC] [COUNT count [ANY]] [WITHCOORD] [WITHDIST] [WITHHASH]
```

#### GEOSEARCHSTORE	与 GEOSEARCH 功能一致，不过可以把结果存储到一个指定的key*（6.2的新功能）*

```powershell
GEOSEARCHSTORE destination source [FROMMEMBER member] [FROMLONLAT longitude latitude] [BYRADIUS radius m|km|ft|mi] [BYBOX width height m|km|ft|mi] [ASC|DESC] [COUNT count [ANY]] [WITHCOORD] [WITHDIST] [WITHHASH] [STOREDIST]
```



## BitMap 位图

Redis 中利用 String 类型数据结构实现 BitMap，因此最大上限是 512M，转换为 bit 是 2^32 个bit位

### BitMap 常见命令

#### SETBIT	向指定位置（offset）存入一个0或1

```powershell
SETBIT key offset value
```

#### GETBIT	获取指定位置（offset）的 bit 值

```powershell
GETBIT key offset
```

#### BITCOUNT	统计 BitMap 中值为1的 bit 位的数量

```powershell
BITCOUNT key [start end]
```

#### BITFIELD	操作（查询、修改、自增）BitMap 中 bit 数组中的指定位置（offset）的值

```powershell
BITFIELD key [GET type offset] [SET type offset value] [INCRBY type offset increment] [OVERFLOW WRAP|SAT|FAIL]

# 从第0位读取2位，无符号位
BITFIELD bm1 GET u2 0
```

#### BITFIELD_RO	获取 BitMap 中的 bit 数组，并以十进制形式返回

```powershell
BITFIELD_RO key ...options...
```

#### BITOP	将多个 BitMap 的结果做位运算（与、或、异或）

```powershell
BITOP operation destkey key [key ...]
```

#### BITPOS	查找 bit 数组中指定范围内第一个0或1出现的位置

```powershell
BITPOS key bit [start] [end]
```





# Redis 的 java客户端

+   Jedis —— 以 redis 命令作为方法名称，学习成本低，简单实用。但是 Jedis 实例是线程不安全的，多线程环境下需要基于连接池来使用
+   Lettuce —— 官方默认兼容的客户端，基于Netty 实现，支持同步、异步和响应式编程方式，并且是线程安全的。支持 Redis 的哨兵模式、集群模式和管道模式。
+   Redisson —— 基于 Redis 实现的分布式、可伸缩的 Java 数据结构集合。包含了诸如 Map、Queue、Lock、Semaphore、AtomicLong 等强大功能。



使用 Spring Data Redis，其兼容了 Jedis 和 Lettuce



## Jedis

### 使用 Jedis

#### 引入依赖

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0</version>
</dependency>
```

#### 建立连接

```java
private Jedis jedis;

@BeforeEach
void setUp(){
    // 1.建立连接
    jedis=new Jedis("127.0.0.1",6379);
    // 2.设置密码
    jedis.auth("wanshubin0223");
    // 3.选择库
    jedis.select(1);
}
```

其中 `@BeforeEach` 是 JUnit 的注解

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220308193028062.png" alt="image-20220308193028062" style="zoom:80%;" />

#### 操作 Redis

Jedis 的方法名和 Redis 的命令相同

```java
@Test
void testString() {
    // 存入数据
    String result = jedis.set("name", "哈撒尅");
    System.out.println("redis = "+result);
    // 获取数据
    String name = jedis.get("name");
    System.out.println("name = "+name);

    jedis.hset("user:1", "name","jack");
    jedis.hset("user:1", "age","21");

    Map<String,String> user=jedis.hgetAll("user:1");
    System.out.println(user);
}
```

#### 关闭连接

```java
@AfterEach
void tearDown() {
    // 判断并关闭连接
    if (jedis!=null){
        jedis.close();
    }
}
```



### 使用 Jedis 线程池

由于 Jedis 是线程不安全的，因此可以使用线程池，在创建 Jedis 实例时调用 `getJedis()` 方法即可

```java
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;

    static {
        // 配置连接池
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        // 最大连接数
        jedisPoolConfig.setMaxTotal(8);
        // 最大空闲连接数
        jedisPoolConfig.setMaxIdle(8);
        // 最小空闲连接数
        jedisPoolConfig.setMinIdle(3);
        // 阻塞等待时长，当无可用连接时要等待多久，-1为无限制等待
        jedisPoolConfig.setMaxWaitMillis(1000);
        // 创建连接池对象
        jedisPool = new JedisPool(jedisPoolConfig,"127.0.0.1",6379,1000);
    }

    public static Jedis getJedis(){
        return jedisPool.getResource();
    }
}
```





## Spring Data Redis

### RedisTemplate 工具类

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220315105347010.png" alt="image-20220315105347010" style="zoom:80%;" />

### 使用 Spring Data Redis

#### 引入依赖

在 SpringBoot 项目中引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.6.3</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-pool2 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.11.1</version>
</dependency>
```

#### 配置 application.yml

```yaml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    # password: wanshubin0223
    # 配置默认使用lettuce，因为依赖中包含了lettuce，如果要使用jedis则需要引入jedis的依赖
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 2
        max-wait: 100ms
```

#### 使用时注入

```java
@SpringBootTest
class SpringDataRedisApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void testString() {
        // 写入string数据
        redisTemplate.opsForValue().set("name5", "pardon");
        // 获取string数据
        Object name = redisTemplate.opsForValue().get("name5");
        System.out.println("name =" + name);
    }
}
```



### Spring Data Redis 的序列化方式

RedisTemplate 可以接收任意 Object 作为值写入 Redis，只不过写入前会把 Object 序列化为字节形式，默认是采用 JDK 序列化。

如 `pardon` 得到的结果是 `\xAC\xED\x00\x05t\x00\x06pardon`

这种方式的缺点：

+   可读性差
+   内存占用较大



### 自定义 Spring Data Redis 的序列化方式

#### JSON 序列化器

编写配置，需要 `jackson-databind` 依赖，SpringBoot 自带，如果没有则需要引入

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        // 创建RedisTemplate对象
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        // 设置连接工厂
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        // 设置Key的序列化
        redisTemplate.setKeySerializer(RedisSerializer.string());
        redisTemplate.setHashKeySerializer(RedisSerializer.string());
        // 设置Value的序列化
        redisTemplate.setValueSerializer(jsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jsonRedisSerializer);
        //返回
        return redisTemplate;
    }
}
```

这种方式存在的问题是：为了在反序列化时知道对象的类型，JSON序列化器会将类的class类型写入json结果中，存入 Redis 中，会带来额外的内存开销。



#### String 序列化器

为了节省内存空间，通常并不会使用 JSON序列化器 来处理value，而是统一使用 String序列化器，要求只能存储 String 类型的 key 和 value，当需要存储 Java 对象时，**手动完成对象的序列化和反序列化**

Spring 默认提供了一个 StringRedisTemplate 类，它的 key 和 value 的序列化方式默认就是 String 方式。省去了我们自定义 RedisTemplate 的过程

##### 使用 fastjson 作为序列化和反序列化工具

###### 序列化 API

```java
package com.alibaba.fastjson;

public abstract class JSON {
    // 将Java对象序列化为JSON字符串，支持各种各种Java基本类型和JavaBean
    public static String toJSONString(Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，返回JSON字符串的utf-8 bytes
    public static byte[] toJSONBytes(Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，写入到Writer中
    public static void writeJSONString(Writer writer, Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，按UTF-8编码写入到OutputStream中
    public static final int writeJSONString(OutputStream os, Object object, SerializerFeature... features);
}
```

###### 反序列化 API

```java
package com.alibaba.fastjson;

public abstract class JSON {
    // 将JSON字符串反序列化为JavaBean
    public static <T> T parseObject(String jsonStr, Class<T> clazz, Feature... features);

    // 将JSON字符串反序列化为JavaBean
    public static <T> T parseObject(byte[] jsonBytes,  // UTF-8格式的JSON字符串
                                    Class<T> clazz, 
                                    Feature... features);

    // 将JSON字符串反序列化为泛型类型的JavaBean
    public static <T> T parseObject(String text, TypeReference<T> type, Feature... features);

    // 将JSON字符串反序列为JSONObject
    public static JSONObject parseObject(String text);
}
```

##### 使用 StringRedisTemplate 并手动序列化的方式读写

`StringRedisTemplate` 继承于 `RedisTemplate<String,String>` ，故其只能存放 `String` 类型的数据，所以要注意数据转换的问题，如 `Long` 类型不能转换为 `String`

```java
@SpringBootTest
class SpringDataRedisApplicationTests {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Test
    void testStringRedisTemplate() {
        stringRedisTemplate.opsForValue().set("name5", "chua");
        Object name=stringRedisTemplate.opsForValue().get("name5");
        System.out.println("name = " + name);

        // 创建对象
        User newUser=new User("chua",22);
        // 手动序列化
        String jsonUser = JSON.toJSONString(newUser);
        // 写入数据
        stringRedisTemplate.opsForValue().set("user:200", jsonUser);
        // 获取数据
        jsonUser = stringRedisTemplate.opsForValue().get("user:200");
        // 反序列化
        User getUser = JSON.parseObject(jsonUser, User.class);
        System.out.println("user = "+getUser);
    }
}
```



### RedisTemplate 操作 Hash 类型

```java
@SpringBootTest
class SpringDataRedisApplicationTests {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Test
    void testHash() {
        // 存入hash数据
        stringRedisTemplate.opsForHash().put("user:300", "name", "hola");
        stringRedisTemplate.opsForHash().put("user:300", "age", "21");
        // 取出hash数据
        Map<Object, Object> entries = stringRedisTemplate.opsForHash().entries("user:300");
        System.out.println("entries = "+entries);
    }
}
```





# 缓存更新策略

## 缓存更新策略的种类<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318131017420.png" alt="image-20220318131017420" style="zoom:80%;" />



## 主动更新策略

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318131354901.png" alt="image-20220318131354901" style="zoom:80%;" />

主动更新策略中，虽然手动更新的方式编码多，但人为控制更加可靠



## 操作缓存和数据库时需要考虑的三个问题

### 删除缓存还是更新缓存？

+   更新缓存 —— 每次更新数据库都更新缓存，无效写操作较多
+   删除缓存 —— 更新数据库时让缓存失效，查询时再更新缓存

故选择删除缓存的这种方式

### 如何保证缓存与数据库的操做的同时成功或失败？（原子性）

+   单体系统 —— 将缓存与数据库操作放在一个事务
+   分布式系统，利用TCC等分布式事务方案

### 先操作缓存还是先操作数据库？

+   先删除缓存，再操作数据库
+   先操作数据库，再删除缓存

由于先操作数据库，再删除缓存的方式出错的概率更低，因此选择这种方式



## 缓存更新策略的最佳实践方案

+   低一致性需求 —— 使用 Redis 自带的内存淘汰机制
+   高一致性需求 —— 主动更新，并以超时剔除兜底方案
    +   读操作
        +   缓存命中则直接返回
        +   缓存未命中则查询数据库，并写入缓存
    +   写操作
        +   先写数据库，然后再删除缓存
        +   要确保数据库与缓存操作的原子性





# 缓存穿透

缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，所有的请求都将会到达数据库，会给数据库带来很大的压力

## 缓存穿透的解决方案

### 缓存空对象（常用）

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318144626674.png" alt="image-20220318144626674" style="zoom:80%;" />

+   优点：实现简单，维护方便
+   缺点：
    +   额外的内存消耗
    +   可能造成短期的不一致



### 布隆过滤

布隆过滤器可以看做是存放二进制位的数组，将数据库的数据通过哈希算法得到哈希值再转为二进制位，存储在过滤器中，以此来作为对比内容。

布隆过滤不能够100%准确判断，如果判断不存在那数据必然不存在，当判断存在时数据则不一定存在

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318145127471.png" alt="image-20220318145127471" style="zoom:80%;" />

+   优点：内存占用较少，没有多余key
+   缺点：
    +   实现复杂
    +   存在误判可能



### 其他措施

+   增强 id 的复杂度，避免被猜测到 id 规律
+   做好数据的基础格式校验
+   加强用户权限校验
+   做好热点参数的限流



# 缓存雪崩

缓存雪崩是指在同一时段大量的缓存 key 同时失效或者 redis 服务宕机，导致大量请求到达数据库，带来巨大压力

## 缓存雪崩的解决方案

+   给不同 key 的 TTL 添加随机值
+   利用 Redis 集群提高服务的可用性
+   给缓存业务添加降级限流策略
+   给业务添加多级缓存



# 缓存击穿

缓存击穿问题也叫热点 key 问题，就是一个被高并发访问并且缓存重建业务较复杂的 key 突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击

## 缓存击穿的解决方案

### 互斥锁

只有获取到互斥锁成功的线程，才能查询数据库数据并重建缓存

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318154248096.png" alt="image-20220318154248096" style="zoom: 80%;" />

缺点是会有很多线程等待，性能较差

#### 案例

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318180032677.png" alt="image-20220318180032677" style="zoom:80%;" />



### 逻辑过期

不设置TTL，使缓存永不过期，在 value 中加入 expire 字段，来存储到期时间

当查询缓存时，如果逻辑时间过期，则尝试获取互斥锁，如果成功则开启新线程进行查询数据库重建缓存并重置逻辑时间的操作，在重建期间，各个线程返回过期数据

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318155035456.png" alt="image-20220318155035456" style="zoom:80%;" />

使用逻辑过期的方法不能保证一致性

#### 构造新的entity

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```

#### 案例

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318175958516.png" alt="image-20220318175958516" style="zoom:80%;" />





## 缓存击穿的解决方案对比

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318160038528.png" alt="image-20220318160038528" style="zoom:80%;" />







# 缓存工具封装

+   Java 对象序列化为 JSON 并存储在 String 类型的 key 中，并且可以设置 TTL 过期时间

+   Java 对象序列化为 JSON 并存储在 String 类型的 key 中，并且可以设置逻辑过期时间

+   根据指定的 key 查询缓存，并反序列化为指定类型，利用缓存空值的方式解决缓存击穿问题

+   根据指定的 key 查询缓存，并反序列化为指定类型，需要利用逻辑过期时间解决缓存击穿问题

添加的实体类

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```

工具类代码

```java
@Slf4j
@Component
public class CacheClient {
    public static final Long CACHE_NULL_TTL = 2L;

    private boolean tryLock(String key){
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    private void unlock(String key){
        stringRedisTemplate.delete(key);
    }

    private static final ExecutorService CACHE_REBUILD_EXECUTOR= Executors.newFixedThreadPool(10);


    @Resource
    private StringRedisTemplate stringRedisTemplate;

    
    
    
    // Java 对象序列化为 JSON 并存储在 String 类型的 key 中，并且可以设置 TTL 过期时间
    public void set(String key, Object value, Long time, TimeUnit timeUnit){
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value),time,timeUnit);
    }

    
    // Java 对象序列化为 JSON 并存储在 String 类型的 key 中，并且可以设置逻辑过期时间
    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit timeUnit){
        // 在存储数据时为数据加上 expire 字段
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(timeUnit.toSeconds(time)));
        // 写入redis
        stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(redisData));
    }
    

    // 根据指定的 key 查询缓存，并反序列化为指定类型，利用缓存空值的方式解决缓存击穿问题
    public <R,ID> R queryWithPassThrough(
            String keyPrefix, ID id, Class<R> type, Function<ID,R> dbFallback, Long time, TimeUnit timeUnit){
        // 1. 从Redis中查询缓存
        String key=keyPrefix + id;
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2. 判断是否存在
        if (StrUtil.isNotBlank(json)){
            // 2-1. 存在则直接返回
            return JSONUtil.toBean(json, type);
        }
        // TODO 判断命中的是否为空值
        if (json!=null){
            // 返回错误信息
            return null;
        }

        // 2-2. 不存在则根据id查询数据库
        R r=dbFallback.apply(id);
        // 2-2-1. 查询数据库不存在则返回404
        if (r==null){
            // TODO 将空值返回redis
            stringRedisTemplate.opsForValue().set(key, "",CACHE_NULL_TTL, TimeUnit.MINUTES);
            return null;
        }
        // 2-2-2. 查询数据库存在则返回数据并写入redis
        this.set(key,r,time, timeUnit);
        return r;
    }

    
    // 根据指定的 key 查询缓存，并反序列化为指定类型，需要利用逻辑过期时间解决缓存击穿问题
    public <R,ID> R queryWithLogicalExpire(String keyPrefix,ID id,Class<R> type,Function<ID,R> dbFallback, Long time, TimeUnit timeUnit){
        // 1. 从Redis中查询缓存
        String key=keyPrefix + id;
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2. 判断是否存在
        if (StrUtil.isBlank(json)){
            // 2-1. 存在则直接返回空
            return null;
        }

        // todo 3. 将json反序列化为对象
        RedisData redisData = JSONUtil.toBean(json, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
        LocalDateTime expireTime = redisData.getExpireTime();
        // todo 4. 判断缓存是否过期
        if (expireTime.isAfter(LocalDateTime.now())){
            // todo 4-1. 如果没过期则直接返回
            return r;
        }
        // todo 4-2. 如果已过期则进行重建
        // todo 4-2-1. 获取互斥锁
        String lockKey=LOCK_SHOP_KEY+id;
        boolean isLock = tryLock(lockKey);
        // todo 4-2-1-1. 获取锁成功，开启新线程执行重建
        if (isLock){
            CACHE_REBUILD_EXECUTOR.submit(()->{
                try {
                    // 查询数据库
                    R r1 = dbFallback.apply(id);
                    // 写入redis
                    this.setWithLogicalExpire(key,r1,time,timeUnit);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                } finally {
                    // 释放锁
                    unlock(lockKey);
                }
            });
        }
        // todo 4-2-1-2. 获取锁失败，返回过期的信息
        return r;
    }
}
```





# 全局ID生成器

全局ID生成器，是一种在分布式系统下用来生成全局唯一ID的工具，一般满足如下特性：

+   高可用
+   唯一性
+   递增性
+   高性能
+   安全性 —— 递增规律不能太明显



为了增加ID的安全性，可以不直接采用Redis自增的数值，而是拼接一些其他信息

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319123815589.png" alt="image-20220319123815589" style="zoom: 80%;" />



## 全局ID生成器的实现

```java
@Component
public class RedisIdWorker {

    // 初始时间戳，2022-1-1 00:00:00
    private static final long BEGIN_TIMESTAMP=1640995200L;
    // 序列号的位数
    private static final int COUNT_BITS =32;

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    // 传入业务前缀
    public Long nextId(String keyPrefix){
        // 生成时间戳
        LocalDateTime now= LocalDateTime.now();
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
        long timeStamp = nowSecond - BEGIN_TIMESTAMP;

        // 获取序列号，精确到天
        String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        // 自增生成序列号，采用 icr+前缀+当天日期 的模式拼接，可以保证自增长不会超过上限，并且便于根据日期统计
        long count = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" + date);

        // 使用位运算拼接并返回
        return timeStamp << COUNT_BITS | count;
    }

    public static void main(String[] args) {
        LocalDateTime time=LocalDateTime.of(2022, 1, 1, 0, 0,0);
        long second = time.toEpochSecond(ZoneOffset.UTC);
        System.out.println("second "+second);
    }
}
```



## 全局唯一ID策略对比

+   **UUID** —— JDK自带工具类生成，这种方式生成的16进制数值返回的结果是字符串，也不是单调递增的，所以不够符合要求
+   **Redis 自增** —— 能够满足各种特性，并且单调递增，数值的长度也不大，存储占用空间小
+   **snowflake 雪花算法** —— 内部维护机器ID，性能好于 Redis 自增的方式，但对于时钟依赖较高
+   **数据库自增** —— 可以看做用数据库中一张特定的表实现 Redis 自增，所以性能低于 Redis 自增方式



Redis 自增 ID 策略：

+   每日的 key 被自动地区分开来，方便统计订单量，并且保证了数值不会超限
+   ID 构造是 时间戳 + 计数器





# 分布式锁

满足分布式系统或集群模式下多进程可见并且互斥的锁，它有如下特性：

+   多进程可见
+   互斥
+   高可用
+   高性能

+   安全性



## 分布式锁的实现方式

常见的分布式锁有三种：**MySQL**、**Redis** 和 **Zookeeper**

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320162840755.png" alt="image-20220320162840755" style="zoom:80%;" />



##  Redis 实现分布式锁的过程

### 获取锁

+   使用 `SETNX` 方法确保只有一个进程获取到锁，并且使用 `EXPIRE` 方法设置 `过期时间TTL` 保证业务不会死锁

+   并且要保证这两个方法在一起具有原子性，所以使用 SET 加参数的方法 —— `SET lock thread1 EX 10 NX` 的命令进行操作

### 释放锁

+   使用 `DEL` 方法删除记录

+   超时时间到期自动释放锁

### 非阻塞

+   尝试一次，成功返回 `true`，失败返回 `false`



## 简单版本的分布式锁

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320164250163.png" alt="image-20220320164250163" style="zoom:80%;" />

### 实现

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320165001399.png" alt="image-20220320165001399" style="zoom:80%;" />

```java
public class SimpleRedisLock implements ILock{
    private StringRedisTemplate stringRedisTemplate;
    private String serviceName;
    private static final String KEY_PREFIX="lock:";

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String serviceName) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.serviceName = serviceName;
    }

    @Override
    public boolean tryLock(long timeoutSec) {
        // 获取线程标识，作为 value 记录
        long threadId = Thread.currentThread().getId();
        // 获取锁
        Boolean ifSuccess = stringRedisTemplate.opsForValue().setIfAbsent(KEY_PREFIX + serviceName, threadId+"", timeoutSec, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(ifSuccess);
    }

    @Override
    public void unlock() {
        // 释放锁
        stringRedisTemplate.delete(KEY_PREFIX+serviceName);
    }
}
```



### 简单版本分布式锁的问题

上面的实现存在着误删锁的问题，如下图，线程1的执行因为一些原因而阻塞，等到完成时释放锁，将不属于自己线程的锁删除了

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320172335617.png" alt="image-20220320172335617" style="zoom: 67%;" />

#### 解决方案

在获取锁时存入 线程标识（可以用UUID表示） ，在删除锁时判断这个锁是否属于本线程

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320172556920.png" alt="image-20220320172556920" style="zoom:67%;" />

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320172633941.png" alt="image-20220320172633941" style="zoom:80%;" />



## 使用 Lua 解决多条命令的原子性问题

在上面的方法中，判断锁和释放锁是两个步骤，这两步的共同执行不是原子操作，仍然可能发生异常

Redis 提供了 Lua 脚本功能，在一个脚本中编写多条 Redis 命令，确保多条命令执行时的原子性

### 基本命令

Redis 提供了调用函数，语法如下：

```java
redis.call('命令名称', 'key' ,'其他参数', ...)
```

列如先执行 set 再执行 get

```lua
redis.call('set','name','jack')
local name = redis.call('get','name')
return name
```

redis 执行脚本

```powershell
EVAL [script] [numkeys] [key ...] [arg ...]
# 例如，因为下面的脚本没有参数，所以参数个数是0
EVAL "return redis.call('set','name','jack')" 0
# 参数传递 key 和 value 形式，key 类型的参数放在 KEYS 数组中， value 类型的参数放在 ARGV 数组中
EVAL "return redis.call('set',KEYS[1],ARGV[1])" 1 name Rose
```

### 实现

#### 业务流程

释放锁的业务流程如下：

1.   获取锁中的线程标识
2.   判断是否与指定的标识（当前线程标识）一致
3.   如果一致则释放锁（删除）
4.   如果不一致则什么都不做

#### lua脚本

```lua
-- 锁的key
local key = KEYS[1]
-- 当前线程标识
local threadId = ARGV[1]

-- 获取锁中的线程标识
local id = redis.call('get',key)
-- 比较线程标识与锁中的标识是否一致
if(id == threadId) then
    -- 释放锁
    return redis.call('del',key)
end
return 0
```

#### 修改 unlock() 方法

RedisTemplate 提供了调用 Lua 脚本的API：

```java
public <T> T execute(RedisScript<T> script, List<K> keys, Object... args) {
    return this.scriptExecutor.execute(script, keys, args);
}
```

在 unlock() 方法中调用此API

```java
@Override
public void unlock() {
    String threadId = ID_PREFIX+Thread.currentThread().getId();
    // 调用lua脚本
    stringRedisTemplate.execute(UNLOCK_SCRIPT,
            Collections.singletonList(KEY_PREFIX+serviceName),
            threadId);
}
```





# Redisson

Redisson 是一个在 Redis 的基础上实现的 Java 驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的 java 常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现

总的来说，它是一个在 Redis 基础上实现的分布式工具的集合，其分布式锁功能如下：

Redisson：https://github.com/redisson/redisson/wiki/%E7%9B%AE%E5%BD%95

+   可重入锁 Reentrant Lock
+   公平锁 Fair Lock
+   联锁 MultiLock
+   红锁 RedLock
+   读写锁 ReadWriteLock
+   信号量 Semaphore
+   可过期性信号量 PermitExpirableSemaphore
+   闭锁 CountDownLatch



## 基于 SETNX 实现的分布式锁的问题

+   **不可重入** —— 同一个线程无法多次获取同一把锁
+   **不可重试** —— 获取锁只尝试一次就返回 false，没有重试机制
+   **超时释放** —— 锁超时释放虽然可以避免死锁，但如果是业务执行耗时较长，也会导致锁释放存在安全隐患
+   **主从一致性** —— 如果 Redis 提供了主从集群，主从同步存在延迟，当主机宕机时，如果从并同步主中的锁数据，则会出现锁失效



## Redisson 分布式锁原理

+   **可重入** —— 利用 hash 结构记录线程 id 和 重入次数
+   **可重试** —— 利用信号量 PubSub 功能实现等待、唤醒、获取锁失败的重试机制
+   **超时续约** —— 利用 watchDog，每隔一段时间（ releaseTime / 3），重置超时时间



## 使用 Redisson

### 引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.16.6</version>
</dependency>
```

### 配置 Redisson 客户端

```java
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redissonClient(){
        // 配置类
        Config config=new Config();
        // 添加redis地址，这里添加了单点的地址，也可以使用config.useClusterServers()添加集群地址
        config.useSingleServer().setAddress("redis://127.0.0.1:6379");
        // 创建客户端
        return Redisson.create(config);
    }
}
```

### 使用 Redisson 分布式锁

```java
@Resource
private RedissonClient redissonClient;

@Test
void testRedisson() throws InterruptedException {
    // 获取锁（可重入），指定锁的名称
    RLock lock = redissonClient.getLock("anylock");
    // 尝试获取锁，参数分别是：获取锁的最大等待时间（期间会重试），锁自动释放时间，时间单位
    boolean isLock = lock.tryLock(1, 10, TimeUnit.SECONDS);
    // 判断锁是否获取成功
    if (isLock){
        try {
            System.out.println("执行业务方法");
        } finally {
            // 释放锁
            lock.unlock();
        }
    }
}
```



## Redisson 可重入锁

### 原理

在记录锁时，记录下 线程ID 和 获取锁次数 ，这样就可以在重入时判断是否是同一个线程标识，如果是就将次数加1，在解锁时同样判断是否是同一个线程标识，如果是就将次数减一，当 获取锁次数 将其减为0时，说明已经没有其他业务使用锁了，在解锁时可以将记录删除，如果不为0则说明还有其他线程使用锁，重置锁的有效期

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321124621846.png" alt="image-20220321124621846" style="zoom:80%;" />

可以看到如下过程比较复杂，因此不再使用 Java 进行操作，而是使用 Lua 脚本

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321130049652.png" alt="image-20220321130049652"  />

其使用的脚本逻辑大致如下：

获取锁

```lua
local key = KEYS[1];    -- 锁的key
local threadId = ARGV[1];   -- 线程的唯一标识
local releaseTime = ARGV[2];    -- 锁的自动释放时间

-- 判断锁是否存在
if(redis.call('exist',key)==0) then
    -- 不存在，获取锁
    redis.call('hset',key,threadId,'1');
    -- 设置有效期
    redis.call('expire',key,releaseTime);
    return 1;   -- 返回结果
end;

-- 锁已经存在，判断threadId是否是自己
if (redis.call('hexists',key,threadId)==1) then
    -- 获取锁，重入次数+1
    redis.call('hincrby',key,threadId,'1');
    -- 设置有效期
    redis.call('expire',key,releaseTime);
    return 1;   -- 返回结果
end

return 0;   -- 获取锁的不是自己，获取锁失败
```

释放锁

```lua
local key = KEYS[1];    -- 锁的key
local threadId = ARGV[1];   -- 线程的唯一标识
local releaseTime = ARGV[2];    -- 锁的自动释放时间

-- 判断当前锁是否还被自己持有
if (redis.call('hexists',key,threadId)==0) then
    return nil; -- 如果已经不是自己的则直接返回
end

-- 是自己的锁则重入次数减一
local count = redis.call('hincrby',key,threadId,-1);

-- 判断重入次数是否已经为0
if (count>0) then
    -- 大于0说明不能释放锁，重置有效期然后返回
    redis.call('expire',key,releaseTime);
    return nil;
else
    -- 等于0则说明可以释放锁，直接删除
    redis.call('del',key);
    return nil;
end
```



## Redisson 锁重试 和 WatchDog机制

只有在 `leaseTime` 为 -1 时才启用 watchdog ，如果自定义 `leaseTime`， 则不会启用 watchdog

![image-20220321142318507](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321142318507.png)



## Redisson  联锁 MultiLock

如下这种联锁的设计，使得当一个线程在每一个节点都获取锁，在检查锁时在每个节点都能够查询到已经获取锁时才认为是已经获取到了锁

联锁的缺点是运维成本较高，实现复杂

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321144030108.png" alt="image-20220321144030108" style="zoom:80%;" />

### 使用联锁

其中创建联锁时任意一个锁对象调用 `getMultiLock` 都可以

```java
@Resource
private RedissonClient redissonClient;
@Resource
private RedissonClient redissonClient2;
@Resource
private RedissonClient redissonClient3;

private RLock rLock;

@BeforeEach
void setUp() {
    RLock lock1 = redissonClient.getLock("order");
    RLock lock2 = redissonClient2.getLock("order");
    RLock lock3 = redissonClient3.getLock("order");

    // 创建联锁
    rLock=redissonClient.getMultiLock(lock1,lock2,lock3);
}
```





# Redis 消息队列

消息队列，就是存放消息的队列，最简单的消息队列模型包括3个角色：

+   消息队列：存储和管理消息，也被称为消息代理（Message Broker）
+   生产者：发送消息到消息队列
+   消费者：从消息队列获取消息并处理消息

其特性如下：

+   消息队列是在 JVM 以外的独立区域，不受 JVM 内存的限制
+   消息队列不仅仅做数据存储，还要确保数据安全，需要对数据做持久化，以保证数据不会因服务宕机或重启而丢失
+   消息队列在将消息投递给消费者后，要求消费者做对消息的确认，如果没有确认，那么该消息就会依然存在于消息队列，下一次消息队列还会将此消息投递给消费者处理，直到成功为止，确保消息至少被消费一次。



## Redis 消息队列的实现

### List 结构	基于 List 结构模拟消息队列

使用 Redis 的数据结构 list，利用 LPUSH 和 RPOP、或者 RPUSH 结合 LPOP 来实现。不过需要注意的是，当队列中没有消息时，RPOP 或 LPOP 操作会返回 null ，并不像 JVM的阻塞队列那样会阻塞并等待消息。

因此这里应该使用 **BRPOP** 或 **BLPOP** 来实现阻塞效果

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220322150057807.png" alt="image-20220322150057807" style="zoom:80%;" />

#### 优缺点

优点：

+   利用 Redis 存储，不受限于 JVM 内存上限
+   基于 Redis 的持久化机制，数据安全性有保证
+   可以满足消息有序性

缺点：

+   无法避免消息丢失
+   只支持单消费者



### PubSub	基本的点对点消息模型

消费者可以订阅一个或多个 channel ，生产者向对应 channel 发送消息后，所有订阅者都能收到相关消息

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220322152745343.png" alt="image-20220322152745343" style="zoom:80%;" />

#### 相关命令

+   SUBSRIBE [chanel ...]        订阅一个或多个频道
+   PUBLISH [channel] [msg]        向一个频道发送消息
+   PSUBSCRIBE [pattern ...]        订阅与 pattern 格式匹配的所有频道；`？`代表一个字符，`*` 代表0个或多个字符，`[ ]` 指定字符

#### 优缺点

优点：

+   采用发布订阅模型，支持多生产者、多消费者

缺点：

+   不支持数据持久化
+   无法避免消息丢失 —— 在发布消息时，如果该频道没有被任何人订阅，该消息将会直接丢失
+   消息堆积有上限，超出时数据丢失



### Stream	比较完善的消息队列模型

Stream 是在 Redis 5.0 引入的一种新的数据类型，它是支持数据持久化的，这种数据类型是专门为消息队列设计的，是 Redis 提供的最完善的消息队列

#### 基本命令

##### XADD 写入消息

```powershell

XADD [key] [NOMKSTREAM`] [MAXLEN|MINID [=|~] threshold [LIMIT count]`] [*|ID] [field value ...]
	# key    队列名称
	# NOMKSTREAM    如果队列不存在，是否自动创建队列，默认是自动创建，如果设置此参数则不自动创建
	# MAXLEN|MINID [=|~] threshold [LIMIT count]	设置消息队列的最大消息数量，如设置为1000，当达到这个数字时，仍然没有消费者来处理，就会将一些旧的消息剔除，一般不设置上限
	# *|ID	指定消息的唯一id，*代表由Redis自动生成。如果自行指定要遵循格式，格式是“时间戳-递增数字”，例如 “1644804662707-0”，建议采用*
	# field value	发送到队列中的消息，称为Entry。格式就是多个[key value]键值对
	
	
# 例如创建名为 users 的队列，并向其中发送一个消息，内容是：{name=jack，age=21}，并且使用Redis自动生成ID
XADD users * name jack age 21

```

##### XREAD 读取消息

```powershell

XREAD [COUNT count] [BLOCK milliseconds] [STREAMS key ...] [ID ...]
	# COUNT count	每次读取消息的最大数量
	# BLOCK  milliseconds	当没有消息时的阻塞时常，如果不设置则不阻塞直接返回数据或空，设置0为永久阻塞
	# STREAMS key	要从哪个队列读取消息，key为队列名
	# ID	起始id，只返回大于该id的消息	0：代表从第一个消息开始；$：代表从最新的消息开始
	
# 例如读取名为 s1 的队列，从最新消息开始每次读取1条消息，并且在队列为空时永久阻塞
XREAD COUNT 1 BLOCK 0 STREAMS s1 $

```

#### 基本用法

在业务开发中，可以循环调用 XREAD 阻塞方式来查询最新消息，从而实现持续监听队列的效果，逻辑如下：

```java
while(true){
	// 尝试读取队列中的消息，最多阻塞2秒
	Object msg = redis.execute("XREAD COUNT 1 BLOCK 2000 STREAMS users $");
	if(msg == null){
		continue;
	}
	//处理消息
	handleMessage(msg);
}
```

**指定 `起始ID` 为` $` 时，代表读取最新的消息，如果我们处理一条消息的过程中，又有超过1条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现漏读消息的问题**



#### 优缺点

优点：

+   消息可回溯 —— 消息读取后也不消失，永久的保存在队列中
+   一个消息可被多个消费者读取 —— 因为消息不会消失，因此可以重复读取
+   可以阻塞读取

缺点：

+   有消息漏读的风险



### 基于 Stream 的消费队列——消费者组

将多个消费者划分到一个组中，监听同一个队列，具备如下特点：

+   **消息分流** —— 队列中的消息会分流给组内的不同消费者，而不是重复消费，从而加快消息处理的速度

+   **消息标识** —— 消费者组会维护一个标识，记录最后一个被处理的消息，哪怕消费者宕机重启，还会从标识之后读取消息。确保每一个消息都会被消费
+   **消息确认** —— 消费者获取消息后，消息处于 pending 状态，并存入一个 pending-list。当处理完成后需要通过 XACK 来确认消息，标记消息为已处理，才会从 pending-list 移除

#### 常用命令

##### XGROUP CREATE	创建消费者组

```powershell

XGROUP CREATE [key] [groupName] [ID] [MKSTREAM`]
	# key	队列名称
	# groupName   消费者组名称
	# ID   起始ID标识，$代表队列中最后一个消息，创建组时不想消费原先队列的消息时使用此参数；0则代表队列中第一个消息，还需要重新消费队列中的消息时使用此参数
	# MKSTREAM	 队列不存在时自动创建队列
	
# 例如创建组名为g1，队列为s1，使用队列中已存在的消息的组
XGROUP CREATE s1 g1 0

```

##### XGROUP DESTROY	删除指定的消费者组

```powershell
XGROUP DESTORY [key] [groupName]
```

##### XGROUP CREATECONSUMER	给指定的消费者组添加消费者

一般不需要手动添加，当从消费者组读取消息时发现没有消费者会自动创建

```powershell
XGROUP CREATECONSUMER [key] [groupName] [consumerName]
```

##### XGROUP DELCONSUMER	删除消费者组中的指定消费者

```powershell
XGROUP DELCONSUMER [key] [groupName] [consumerName]
```

##### XREADGROUP	从消费者组读取消息

```powershell

XREADGROUP [GROUP group consumer] [COUNT count] [BLOCK milliseconds] [NOACK] [STREAMS key ...] [ID ...]
	# group   消费组名称
	# consumer   消费者名称，如果消费者不存在，会自动创建一个消费者
	# count   本次查询的最大数量
	# BLOCK milliseconds   当没有消息时最长等待时间
	# NOACK   无需手动ACK，获取到消息后自动确认，由于会出现确认问题，所以强烈不建议设置
	# STREAMS key   指定队列名称
	# ID   获取消息的起始id	>：从下一个未消费的消息开始；其它：根据指定id从pending-list中获取已消费但未确认的消息，列如0，是从pending-list中的第一个消息开始
	
# 例如消费者c1读取g1组中队列s1的消息，每次读1条，最多等待2000毫秒，从消费者组中尚未消费的消息开始读取
XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >

```

##### XACK	确认消息

```powershell
XACK [key] [group] [ID ...]

# 如确认g1组中队列s1，id为“1646339018049-0”、“1646339342815-0”和“1646339541426-0”的消息
XACK s1 g1 1646339018049-0 1646339342815-0 1646339541426-0
```

##### XPENDING	查看 pending-list

```powershell

XPENDING [key] [group] [[IDLE min-idle-time*] [start] [end] [count] [consumer*]]
	# key   队列名称
	# group   组名称
	# IDLE min-idle-time   空闲时间，获取在该时间以上的消息的时间，其他消息则不获取
	# start   获取消息的id的起始大小
	# end   获取消息的id的最大大小
	# count   获取消息的数量
	# consumer   获取指定消费者的pending-list

#例如获取group55组中的mystream队列的10条消息，所有范围的id都获取
XPENDING mystream group55 - + 10

```

#### 基本思路

```java
while(true){
	// 尝试监听队列，使用阻塞模式，最长等待2000秒
    Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >");
    if(msg == null){	// null说明没有消息，继续监听
        continue;
    }
    try{
        // 处理消息，完成后一定要ACK
        handleMessage(msg);
    } catch(Exception e){
        while(true){
            Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 STREAMS s1 0");
            if(msg == null){	// null说明没有异常消息，所有消息都已确认，结束循环
                break;
            }
            try{
                // 有异常消息，处理异常
                handleMessage(msg);
            } catch(Exception e){
                // 再次出现异常，记录日志，继续循环
                continue;
            }
        }
    }
}
```

#### 优缺点

优点：

+   消息可回溯 —— 不同组的消费者可以多次读取消息
+   可以多消费者争抢消息，加快消费速度 —— 同一组的多个消费者可以争抢
+   可以阻塞读取
+   没有消息漏读的风险
+   有消息确认机制，保证消息至少被消费一次



## 各种消息队列实现的对比

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220322182305175.png" alt="image-20220322182305175" style="zoom:80%;" />



# Feed 流

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324094428938.png" alt="image-20220324094428938" style="zoom:80%;" />

## Feed 流的两种模式

+   **TimeLine** —— 不做内容筛选，简单地按照内容发布时间排序，常用于好友或关注。例如朋友圈
    +   优点：信息全面，不会有缺失，实现也相对简单
    +   缺点：信息噪音较多，用户不一定感兴趣，内容获取效率低
+   **智能排序** —— 利用智能算法屏蔽掉违规的、用户不感兴趣的内容。推送用户感兴趣的信息来吸引用户
    +   优点：投喂用户感兴趣信息，用户粘度很高，容易沉迷
    +   缺点：如果算法不够精准，可能起到反作用



## Feed 流的实现方案

### 拉模式

也叫读扩散，接收者会将关注的人所发送的消息拉取到自己的收件箱中并排序

缺点是每次读取都需要从发件箱中拉取消息，耗时较多

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324095518011.png" alt="image-20220324095518011" style="zoom:80%;" />



### 推模式

也叫写扩散，发送者会将消息直接推送到他所有的关注者的收件箱中并排序，关注者就不需要每次再重新拉取消息了，所以这种方式延迟低

这种方式的缺点是占用内存较多

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324100003579.png" alt="image-20220324100003579" style="zoom:80%;" />

## 推拉结合模式

也叫读写混合，兼具推和拉两种模式的优点

发送者的关注者较少时，可以直接采用推模式将消息直接发送到每一个关注者的收件箱中；发送者的关注者多时，根据关注者的活跃程度进行分类，一般来说活跃度高的关注者的比例就低，因此对于这部分关注者采用推模式将消息发送到收件箱中，而对于活跃度低的关注者，则采用拉模式，发送者将消息发送到发件箱中，关注者查看时进行拉取

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324101815603.png" alt="image-20220324101815603" style="zoom:80%;" />

## 三种模式的对比

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324101855879.png" alt="image-20220324101855879" style="zoom:80%;" />





# HyperLogLog

简称 HLL，是从 Loglog 算法派生的概率算法，用于确定非常大的集合的基数，而不需要存储其所有值

Redis 中的 HLL 是基于 String 结构实现的，单个 HLL 的内存永远小于 16kb，内存占用非常低。作为代价，其测量结果是概率性的，有小于 0.81% 的误差，不过对于 UV 统计来说，这完全可以忽略

## 常用命令

### PFADD	插入元素

```
PFADD key element [element ...]
```

### PFCOUNT	统计，重复的元素不再另计数

```
PFCOUNT key [key ...]
```

### PFMERGE	合并多个 HLL

```
PFMERGE destkey sourcekey [sourcekey ...]
```





# 分布式缓存

## 单点 Redis 的问题和解决策略

+   数据丢失问题 —— 实现 Redis 数据持久化
+   并发能力问题 —— 搭建主从集群，实现读写分离
+   存储能力问题 —— 搭建分片集群，利用插槽机制实现动态扩容
+   故障恢复问题 —— 利用 Redis 哨兵，实现健康检测和自动恢复



## Redis 持久化

### RDB 持久化

RDB 全称 Redis Database Backup file（Redis 数据备份文件），也被叫做 Redis 数据快照。**简单来说就是把内存中的所有数据都记录到磁盘中**。当 Redis 实例故障重启后，从磁盘读取快照文件，恢复数据

快照文件称为 RDB 文件，默认保存在当前运行目录

#### 执行 RDB

+   可以使用 `save` 命令来执行 RDB，由 Redis 主进程来执行 RDB，会阻塞所有命令，一般只有在预知 Redis 要停机需要持久化时才使用此命令

+   使用 `bgsave` 命令，则可以在后台进行 RDB，此时将会开启子进程执行 RDB，避免主进程受到影响，推荐使用这种方式
+   Redis 主动停机时会自动执行一次 RDB



#### RDB 配置

RDB 默认开启，会在 Redis 手动停机前自动触发一次，可以通过配置调整 RDB 策略

```
# 900秒内，如果至少有1个key被修改，则执行bgsave，如果是save "" 则表示禁用RDB
save 900 1
save 300 10
save 60 10000
```

```
# 是否压缩，建议不开启，压缩也会消耗cpu，磁盘容量则是廉价资源，如果cpu资源充足
rdbcompression no

# RBD文件名称
dbfilename dump.rdb

# 文件保存的路径目录
dir ./
```



#### RDB 原理

 bgsave 开始时会 fork 主进程得到子进程，子进程共享主进程的内存数据。完成 fork 后读取内存数据并写入新的 RDB 文件中，然后用新的 RDB 文件替换旧的 RDB 文件

子进程只复制页表而不是直接复制内存，因此速度会比较快

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326111824752.png" alt="image-20220326111824752" style="zoom:80%;" />



为了避免当主进程在写数据时子进程读数据而产生的脏读，fork 采用了 **copy-on-write** 的技术：

+   当主进程执行读操作时，访问共享内存
+   当主进程执行写操作时，则会拷贝一份数据，进行写操作

假设一种极端情况，当子进程在读数据时，主进程修改了所有的数据，那么就意味着所有的数据都会被备份为副本，这时数据的存储空间将会翻倍，因此要给 Redis 预留一些空间

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326112442446.png" alt="image-20220326112442446" style="zoom:80%;" />



### AOF 持久化

AOF 全称为 Append Only File（追加文件）。Redis 处理的每一个写命令都会记录在 AOF 文件，可以看做是命令日志文件

因为是记录命令，AOF 文件会比 RDB 文件大得多。而且 AOF 会记录对同一个 key 的多次写操作，但只有最后一次写才有意义。通过执行 bgrewriteaof 命令，可以让 AOF 文件执行重写功能，用最少的命令达到相同效果

#### AOF 配置

AOF 默认是关闭的，需要修改配置文件来开启

```
# 是否开启AOF功能，默认是no
appendonly	yes
# AOF文件的名称
appendfilename "appendonly.aof"
```

```
# 表示每执行一次写命令，立即记录到AOF文件
appendsync always
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendsync everysec
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendsync no
```

三种模式的对比

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326134945322.png" alt="image-20220326134945322" style="zoom:80%;" />

```
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写
auto-aof-rewrite-min-size 64mb
```



### RDB 和 AOF 对比

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326120829218.png" alt="image-20220326120829218" style="zoom:80%;" />



## Redis 主从

单节点 Redis 的并发能力是有上限的，要进一步提高 Redis 的并发能力，就需要搭建主从集群，实现读写分离

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326171758452.png" alt="image-20220326171758452" style="zoom:80%;" />

### 配置主从

在从节点上使用 `slaveof <主节点IP> <主节点端口>` 命令来实现主从关系

还要配置各节点的密码 `masterauth <主节点访问密码>` ，所有节点都要配置，以防主从切换时某些节点无法访问



### 主从同步原理

#### 全量同步

+   slave 节点第一次连接 master 节点时，第一次同步是全量同步，将会复制所有数据
+   slave 节点断开时间太久，repl_baklog 中的 offset 已经被覆盖时会执行全量同步

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326195003954.png" alt="image-20220326195003954" style="zoom:80%;" />

master 判断 slave 是否是第一次数据同步时，会用到：

+   Replication Id：简称 replid，是数据集的标记，id 一直则说明是同一数据集。每个 master 都有唯一的 replid，slave 则会继承 master 节点的 replid
+   offset：偏移量，随着记录在 repl_baklog 中的数据增多而逐渐增大。slave 完成同步时也会记录当前同步的 offset。如果 slave 的 offset 小于 master 的 offset，说明 slave 数据落后于 master，需要更新

因此 slave 做数据同步，必须向 master 声明自己的 replication id 和 offset，master 才可以判断到底需要同步哪些数据

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326200214655.png" alt="image-20220326200214655" style="zoom:80%;" />

#### 全量同步的流程

+   slave 节点请求增量同步
+   master 节点判断 replid，发现不一致，拒绝增量同步
+   master 将完整内存数据生成 RDB，发送 RDB 到 slave
+   slave 清空本地数据，加载 master 的 RDB
+   master 将 RDB 期间的命令记录在 repl_baklog，并持续将 log 中的命令发送给 slave
+   slave 执行接收到的命令，保持与 master 直接的同步

#### 增量同步

+   如果 slave 节点断开又恢复，并且在 repl_baklog 中能找到 offset时，执行增量同步

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326201340320.png" alt="image-20220326201340320" style="zoom:80%;" />

repl_baklog 的数据结构类似于环，如下：

![image-20220326201822172](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326201822172.png)

它的大小是有上限的，写满后会覆盖最早的数据。如果 slave 断开时间过久，导致尚未备份的数据被覆盖，则无法基于 log 做增量同步，只能再次全量同步。



### 优化 Redis 主从集群

+   在 master 中配置 `repl-diskless-sync yes` 启用无磁盘复制，避免全量同步时的磁盘IO，数据能够通过网络直接发给 slave
+   Redis 单节点上的内存占用不要太大，减少 RDB 导致的过多磁盘IO
+   适当提高 repl_baklog 的大小，发现 slave 宕机时尽快实现故障恢复，尽可能避免全量同步
+   限制一个 master 上的 slave 节点数量，如果实在有太多的 slave，则可以采用 **主-从-从** 的链式结构，减少 master 压力，如下图：

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220326202740586.png" alt="image-20220326202740586" style="zoom:80%;" />

### 全量同步和增量同步的区别

+   全量同步：master 将完整内存数据生成 RDB，发送 RDB 到 slave。后续命令则记录在 repl_baklog，逐个发送给 slave
+   增量同步：slave 提交自己的 offset 到 master，master 获取 rep_baklog 中从 offset 之后的的命令给 slave





## Redis 哨兵

slave 节点宕机恢复后可以找 master 节点同步数据，而 master 节点宕机的问题则要靠 Redis 哨兵来解决

Redis 提供了哨兵（Sentinel）机制来实现主从集群的自动故障恢复，哨兵的结构和作用如下：

+   **监控：**Sentinel 会不断检查 master 和 slave 是否按预期工作
+   **自动故障恢复：**如果 master 故障，Sentinel 会将一个 slave 提升为 master。当故障实例恢复后也以新的 master 为主
+   **通知：**Sentinel 充当 Redis 客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给 Redis 的客户端

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220327110846780.png" alt="image-20220327110846780" style="zoom:80%;" />

### Sentinel 工作过程

#### 服务状态监控

Sentinel 基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送 ping 命令：

+   **主观下线：**如果某 sentinel 节点发现某实例未在规定时间响应，则认为该实例主观下线
+   **客观下线：**若超过指定数量（``quorum`）的 sentinel 都认为该实例主观下线，则该实例客观下线。`quorum` 值最好超过 Sentinel 实例数量的一半

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220327111603802.png" alt="image-20220327111603802" style="zoom:80%;" />



#### 选取新的 master

一旦发现 master 故障，sentinel 需要在 slave 中选择一个作为新的 master，选择依据是这样的：

+   首先会判断 slave 节点与 master 节点断开时间长短，如果超过指定值（`down-after-milliseconds * 10`）则会排除该 slave 节点
+   然后判断 slave 节点的 `slave-priority` 值，越小优先级越高，如果是 0 则永不参与选举
+   如果 `slave-priority` 一样，则判断 slave 节点的 **offset** 值，越大说明数据越新，优先级越高
+   最后判断 slave 节点的运行 id 大小，越小优先级越高



#### 故障转移

当选中了一个 slave 为新的 master 后，例如 slave1，故障转移的步骤如下：

+   sentinel 给备选的 slave1 节点发送 slaveof no one 命令，让该节点成为 master
+   sentinel 给所有其它 slave 发送 `slaveof <slave1Ip> <slave1Port>` 命令，让这些 slave 成为新 master 的从节点，开始从新的 master 上同步数据
+   最后，sentinel 将故障节点标记为 slave，当故障节点恢复后会自动成为新的 master 的 slave 节点

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220327113106347.png" alt="image-20220327113106347" style="zoom:80%;" />

### 搭建哨兵集群

在每个节点上配置 sentinel.conf

```
bind 0.0.0.0
protected-mode no
port 26379		# 各个sentinel的端口
sentinel announce-ip 192.168.150.101	# 声明本机的ip地址
sentinel monitor mymaster 192.168.150.101 7001 2	# mymaster为自定义的主节点名，随后设置主节点的ip和端口，并设置quorum值
sentinel auth-pass mymaster wanshubin0223	# 设置主节点的redis连接密码
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/opt/redis-6.2.6"	# 设置工作目录
```



### RedisTemplate 哨兵模式

在 Sentinel 集群监管下的 Redis 主从集群，其节点会因为自动故障转移而发生变化，Redis 的客户端必须感知这种变化，及时更新连接信息。Spring 的 RedisTemplate 底层利用 lettuce 实现了节点的感知和自动切换

引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.6.5</version>
</dependency>
```

配置 application.yml 的 sentinel 相关信息

```yaml
logging:
  level:
    io.lettuce.core: debug
  pattern:
    dateformat: MM-dd HH:mm:ss:SSS

spring:
  redis:
    sentinel:
      master: mymaster  # 指定master名称
      nodes:  # 指定redis-sentinel集群信息
        - 192.168.137.10:26379
        - 192.168.137.11:26379
        - 192.168.137.12:26379
```

配置主从读写分离

```java
@Bean
public LettuceClientConfigurationBuilderCustomizer clientConfigurationBuilderCustomizer(){
    return clientConfigurationBuilder -> clientConfigurationBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

ReadFrom 是配置 Redis 的读取策略，是一个枚举，其选择如下：

+   MASTER：从主节点读取
+   MASTER_PREFERRED：优先从 master 节点读取，master 不可用才读取 replica
+   REPLICA：从 slave（replica）节点读取
+   REPLICA_PREFERRED：优先从 slave（replica）节点读取，所有的 slave 都不可用才读取 master



## Redis 分片集群

主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：

+   海量数据存储问题
+   高并发写的问题

使用分片集群可以解决上述问题，分片集群特征：

+   集群中有多个 master，每个 master 保存不同数据
+   每个 master 都可以有多个 slave 节点
+   master 之间通过 ping 监测彼此健康状态
+   客户端请求可以访问集群任意节点，最终都会被转发到正确节点

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220328142950188.png" alt="image-20220328142950188" style="zoom:80%;" />



### 配置分片集群

修改每个节点的 redis.conf

```sh
port 6379
# 开启集群功能
cluster-enabled yes
# 集群的配置文件名称，不需要我们创建，由redis自己维护
cluster-config-file /opt/redis-6.2.6/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /opt/redis-6.2.6
# 绑定地址
bind 0.0.0.0
# 让redis后台运行
daemonize yes
# 注册的实例ip
replica-announce-ip 192.168.137.10
# 保护模式
protected-mode no
# 数据库数量
databases 1
# 日志
logfile /opt/redis-6.2.6/run.log
```

管理集群

Redis 5.0以后，将集群管理集成到了 redis-cli 中，格式如下

```sh
redis-cli --cluster create --cluster-replicas 1 192.168.137.10:6379 192.168.137.11:6379 192.168.137.12:6379 192.168.137.20:6379 192.168.137.21:6379 192.168.137.22:6379
```

命令说明：

- `redis-cli --cluster`：代表集群操作命令
- `create`：代表是创建集群
- `--replicas 1`或者`--cluster-replicas 1` ：指定集群中每个master的副本个数为1，此时`节点总数 ÷ (replicas + 1)` 得到的就是master的数量。因此节点列表中的**前n个**就是master，其它节点都是slave节点，随机分配到不同master

进入集群

```sh
redis-cli -c -a wanshubin0223
```

查看集群节点

```sh
cluster nodes
```



### 散列插槽

由于 Redis 的主节点会出现宕机的情况，或者是集群伸缩导致的节点的增加或删除，如果一个节点被删除或宕机，上面的数据就会丢失，但如果数据和插槽绑定，当节点宕机时，可以将该节点对应的插槽转移到可用节点上去；集群扩容时也可以将插槽进行转移。这样一来，数据跟随插槽移动，可以保证永远都能找到数据的位置

Redis 会把每一个 master 节点映射到 0 ~ 16383 共 16384 个插槽上（hash slot），查看集群信息时就能看到。

数据 key 不是与节点绑定，而是与插槽绑定。redis 会根据 key 的有效部分计算插槽值，分两种情况：

+   key 中包含 “{}” ，且 “{}” 中至少包含一个字符，”{}“ 中的部分是有效部分
+   key 中不包含 ”{}“ ，整个 key 都是有效部分

例如：key 是 num，那么就根据 num 计算，如果是 {itcast}num ，则根据 itcast 计算。计算方式是利用 CRC16 算法得到一个 hash 值，然后对 16384 取余，得到的结果就是 slot 值

#### 将同一类数据固定的保存在同一个 Redis 实例

例如商品中，所有的手机、所有的空调、所有的电视分别都放在同一个节点上，以减少重定向的性能损耗

方法是让这一类数据使用相同的有效部分，例如 key 都以 {typeId} 为前缀



### 集群伸缩

使用 `redis-cli --cluster help` 命令查看 redis 集群相关命令的文档

#### 添加节点

例如添加一个新节点 192.168.137.20:6379，并报告已存在的节点 192.168.137.10:6379

```sh
redis-cli -a wanshubin0223 --cluster add-node 192.168.137.20:6379 192.168.137.10:6379
```

#### 移动插槽

可以将某个节点的插槽移动到另一节点

例如将位于 192.168.137.10:6379 的插槽 0-2999 移动到节点 192.168.137.12:6379 上

```sh
redis-cli -a wanshubin0223 --cluster reshard 192.168.137.12:6379

>>> Perforning cluster Check (using node 192.168.137.12:6379): 
M: 3d0b55a4b7bf151f9e358e878a00f27cd015571b 192.168.137.12:6379
   slots: [8192-12287](4096 slots) naster
M: Cc463054da6e4b711d8e03a444f86c598242f9f6 192.168.137.13:6379
   slots: [12288-16383](4096 slots) master
M: 461076b9b32d4fa8b4500beddf954a51b7028c88 192.168.137.10:6379
   slots: [0-4095](4096 slots) naster
M: 84ba2300584c4d10ca3284b53a587d30cc2e56e4 192.168.137.11:6379
   slots: [ 4096-8191] (4096 slots) master
   
-> How many slots do you want to nove (fron 1 to 16384)? 3000
-> What is the receiving node ID? 3d0b55a4b7bf151f9e358e878a00f27cd015571b

Please enter all the source node IDs.
   Type 'all' to use all the nodes as source nodes for the hash slots.
   Type 'done' once you entered all the source nodes IDs.
-> Source node #1: 461076b9b32d4fa8b4500beddf954a51b7028c88
-> Source node #2: done

-> Do you want to proceed with the proposed reshard plan (yes/no)? yes
```



### 故障转移

当集群中有一个 master 宕机时，会经历三个阶段：

+   该实例与其他实例失去连接
+   疑似宕机
+   确定下线，自动提升一个 slave 为新的 master

#### 数据迁移

有时节点需要升级，要用到手动故障转移

在从节点上执行 `cluster failover` 命令可以手动让集群中的某个 master 宕机，切换到执行 `cluster failover` 命令的这个 slave 节点，实现无感知的数据迁移，流程如下：

让该 slave 替换这个 master

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220328172337848.png" alt="image-20220328172337848"  />

手动的 Failover 支持三种不同模式：

+   缺省：默认的流程，如上图
+   force：省略了对 offset 的一致性校验
+   takeover：直接执行第5步，忽略数据一致性、忽略 master 状态和其它 master 的意见



### RedisTemplate 访问分片集群

RedisTemplate 底层同样基于 lettuce 实现了分片集群的支持，而使用的步骤与哨兵模式基本一致：

1.   引入 redis 的 starter 依赖
2.   配置分片集群地址
3.   配置读写分离

与哨兵模式相比，只有分片集群的配置方式略有差异：

引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.6.5</version>
</dependency>
```

配置 application.yml 的 cluster 相关信息

```yaml
logging:
  level:
    io.lettuce.core: debug
  pattern:
    dateformat: MM-dd HH:mm:ss:SSS

spring:
  redis:
    password: wanshubin0223
    cluster:
      nodes:
        - 192.168.137.10:6379
        - 192.168.137.11:6379
        - 192.168.137.12:6379
        - 192.168.137.13:6379
```

配置主从读写分离

```java
@Bean
public LettuceClientConfigurationBuilderCustomizer clientConfigurationBuilderCustomizer(){
    return clientConfigurationBuilder -> clientConfigurationBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

ReadFrom 是配置 Redis 的读取策略，是一个枚举，其选择如下：

+   MASTER：从主节点读取
+   MASTER_PREFERRED：优先从 master 节点读取，master 不可用才读取 replica
+   REPLICA：从 slave（replica）节点读取
+   REPLICA_PREFERRED：优先从 slave（replica）节点读取，所有的 slave 都不可用才读取 master





# 多级缓存

## 传统缓存

传统的缓存策略一般是请求到达 Tomcat 后，先查询 Redis，如果未命中则查询数据库，存在一些问题：

 <img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220328190851186.png" alt="image-20220328190851186" style="zoom:80%;" />

+   请求要经过 Tomcat 处理，Tomcat 的性能成为整个系统的瓶颈
+   Redis 缓存失效时，会对数据库产生冲击



## 多级缓存方案

多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻 Tomcat 压力，提升服务性能

用作缓存的 Nginx 是业务 Nginx，需要部署为集群，再有专门的 Nginx 用来做反向代理

![image-20220328191822336](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220328191822336.png)

### JVM 进程缓存

#### 分布式缓存 和 本地进程缓存

分布式缓存，例如 Redis：

+   优点：存储容量更大、可靠性更好、可以在集群间共享
+   缺点：访问缓存有网络开销
+   场景：缓存数据量较大、可靠性要求较高、需要在集群间共享

本地进程缓存，例如 HashMap，GuavaCache：

+   优点：读取本地内存，没有网络开销，速度更快
+   缺点：存储容量有限、可靠性较低、无法共享
+   场景：性能要求较高，缓存数据量较小



#### Caffeine

咖啡因，是一个基于 Java8 开发的，提供了近乎最佳命中率的高性能的本地缓存库，目前 Spring 内部的缓存使用的就是 Caffeine

```java
@Test
void testBasicOps() {
    // 创建缓存对象
    Cache<String, String> cache = Caffeine.newBuilder().build();

    // 存数据
    cache.put("name", "bmw");

    // 取数据，不存在则返回null
    String name = cache.getIfPresent("name");
    System.out.println("name = " + name);

    // 使用 get() 方法取数据，不存在则去数据库查询，使用lambda表达式更为方便
    String defaultNAME = cache.get("defaultNAME", key -> {
        // 这里可以去数据库根据 key查询value
        return "benz";
    });
    System.out.println("defaultNAME = " + defaultNAME);
}
```



##### Caffeine 缓存驱逐策略

在默认情况下，当一个缓存元素过期的时候，Caffeine 不会自动立即将其清理和驱逐。而是在一次读或写操作后，或者再空闲时间完成对失效数据的驱逐。

三种缓存策略如下：

+   基于容量：设置缓存的数量上限

```java
Cache<String, String> cache = Caffeine.newBuilder()
		.maximumSize(1)		// 设置缓存大小上限为1
		.build();
```

+   基于时间：设置缓存的有效时间

```java
Cache<String, String> cache = Caffeine.newBuilder()
		.expireAfterWrite(Duration.ofSeconds(10))	// 设置缓存有效期为 10 秒
		.build();
```

+   基于引用：设置缓存为软引用或弱引用，利用GC来回收缓存数据。性能较差，不建议使用



## OpenResty

OpenResty 是一个基于 Nginx 的高性能 Web 平台，用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。有如下特点：

+   具备 Nginx 的完整功能
+   基于 Lua 语言进行扩展，集成了大量精良的 Lua 库、第三方模块
+   允许使用 Lua 自定义业务逻辑、自定义库



### OpenResty 返回数据

在 lua 脚本中使用 `ngx.say()` 方法来返回 Json 数据：

```lua
-- 获取路径参数
local id = ngx.var[1]
-- 返回结果
ngx.say('{"id":'..id..',"name":"SALSA AIR","title":"RIMOWA 210寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":199900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```



### OpenResty 获取请求参数

OpenResty 提供了各种 API 用来获取不同类型的请求参数：

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331161921531.png" alt="image-20220331161921531"  />



### 发送 Http 请求

nginx 提供了内部 API 用于发送 http 请求：

```nginx
# get方式
local resp = ngx.location.capture("/path",{
	method = ngx.HTTP_GET,		-- 请求方式
	args = {a=1,b=2}		-- get方式传参
})

# post方式
local resp = ngx.location.capture("/path",{
	method = ngx.HTTP_GET,		-- 请求方式
	body = "c=3&d=4"		-- post方式传参
})
```

返回的相应内容包括：

+   resp.status —— 响应状态码
+   resp.header —— 响应头，是一个 table
+   resp.body —— 响应体，就是响应数据

**注意：以上的 `/path` 是路径，并不包含 IP 和 端口。这个请求会被 nginx 内部的 server 监听并处理**

我们希望这个请求发送到 Tomcat 服务器，所以还需要编写一个 server 来对这个路径做反向代理：

```nginx
location /path {
	# 这里是tomcat的ip和java服务端口，需要确保windows防火墙处于关闭状态
	proxy_pass http://192.168.137.2:8081;
}
```



#### 封装 Http 查询的函数

可以把http查询的请求封装为一个函数，放到 OpenResty 函数库汇总，方便后期使用。

1.   在/usr/local/openresty/lualib 目录下创建 common.lua 文件：

```sh
touch /usr/local/openresty/lualib/common.lua
```

2.   在 common.lua 中封装 http 查询的函数

```lua
-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
    local resp = ngx.location.capture(path,{
        method = ngx.HTTP_GET,
        args = params,
    })
    if not resp then
        -- 记录错误信息，返回404
        ngx.log(ngx.ERR, "http not found, path: ", path , ", args: ", args)
        ngx.exit(404)
    end
    return resp.body
end
-- 将方法导出
local _M = {  
    read_http = read_http
}  
return _M
```

3.   nginx.conf 中添加模块配置

```nginx
#lua 模块
lua_package_path "/usr/local/openresty/lualib/?.lua;;";
#c模块     
lua_package_cpath "/usr/local/openresty/lualib/?.so;;";  
```



#### JSON 结果处理

OpenResty 提供了一个 cjson 的模块用来处理 JSON 的序列化和反序列化

官方地址：https://github.com/openresty/lua-cjson/

+   引入 cjson 模块

```lua
local cjson = require “cjson”
```

+   序列化

```lua
local obj = {
	name = 'jack',
	age = 21
}
local json = cjson.encode(obj)
```

+   反序列化

```lua
local json = '{"name": "jack", "age": 21}'
-- 反序列化
local obj = cjson.decode(json);
print(obj.name)
```



### OpenResty 的 Redis 模块

OpenResty 提供了操作 Redis 的模块，只要引入该模块就能直接使用

+   引入 Redis 模块，并初始化 Redis 对象

```nginx
-- 导入redis
local redis =require('resty.redis')
-- 初始化redis
local red=redis:new()
-- 设置redis超时时间，三个参数分别为：建立连接的超时时间、发送请求的超时时间、响应结果的超时时间
red:set_timeouts(1000,1000,1000)
```

+   封装函数，用来释放 Redis 连接、将连接放回连接池

```nginx
-- 关闭redis连接的工具方法、将连接放回连接池
local function close_redis(red)
    local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
    local pool_size = 100 -- 连接池大小
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end
```

```nginx
-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = ", key)
    end
    -- 得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end
```

+   将方法导出

```nginx
local _M = {  
    read_redis = read_redis
}  
return _M
```



### OpenResty 本地缓存

OpenResty 为 Nginx 提供了 shard dict 功能，可以在 nginx 的多个 worker 之间共享数据，实现缓存功能

+   开启共享字典，在 nginx.conf 的 http 下添加配置：

```nginx
# 共享字典，也就是本地缓存，名称叫做：item_cache，大小105M
lua_shared_dict item_cache 150m;
```

+   操作共享字典：

```lua
-- 获取本地缓存对象
local item_cache = ngx.shared.item_cache
-- 存储，指定key、value、过期时间、单位（秒），默认为0表示永不过期
item_cache:set('key','value',1000)
-- 读取
local val = item_cache:get('key')
```



## Redis 冷启动与缓存预热

+   **冷启动：**服务刚刚启动时，Redis 中并没有缓存，如果所有商品数据都在第一次查询时添加缓存，可能会给数据库带来很大压力
+   **缓存预热：**在实际开发中，我们可以利用大数据统计用户访问的热点数据，在项目启动时将这些热点数据提前查询并保存到 Redis 中

在数据量较少的情况下，可以在启动时将所有数据都放入缓存中

### 缓存预热

引入 Redis 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.6.5</version>
</dependency>
```

配置 application.properties，由于 yml 文件可能会产生远程访问被拒绝的问题，所以改用 properties 配置，使用 yml 配置时要为密码加上单引号

```properties
spring.redis.host=192.168.137.10
```

编写初始化类

```java
@Component
public class RedisHandler implements InitializingBean {
// InitializingBean，会在Bean创建完成、Autowired完成之后执行afterPropertiesSet()方法
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Autowired
    private IItemService itemService;

    @Autowired
    private IItemStockService iItemStockService;

    // json处理工具
    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化缓存
        // 1.查询商品信息
        List<Item> itemList = itemService.list();
        // 2.放入缓存
        for (Item item:itemList){
            // 将item序列化为json
            String json = MAPPER.writeValueAsString(item);
            // 存入redis
            stringRedisTemplate.opsForValue().set("item:id:"+item.getId(), json);
        }
        // 3.查询库存信息
        List<ItemStock> stockList = iItemStockService.list();
        // 4.放入缓存
        for (ItemStock itemStock:stockList){
            String json = MAPPER.writeValueAsString(itemStock);
            stringRedisTemplate.opsForValue().set("item:stock:id:"+itemStock.getId(),json);
        }
    }
}
```





# 案例

## 基于 Redis 实现共享 session 登录

发送短信验证码

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319144452691.png" alt="image-20220319144452691" style="zoom:80%;" />

前端携带 token 并在每次请求时都携带 token

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319144557748.png" alt="image-20220319144557748" style="zoom:80%;" />

使用登录拦截器

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319144803426.png" alt="image-20220319144803426" style="zoom:80%;" />





## 解决缓存穿透

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319145012832.png" alt="image-20220319145012832" style="zoom:80%;" />



## 解决缓存击穿

### 使用互斥锁解决

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318180032677.png" alt="image-20220318180032677" style="zoom:80%;" />



### 设置逻辑过期解决

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220318175958516.png" alt="image-20220318175958516" style="zoom:80%;" />



## 实现优惠券秒杀的下单功能

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319143940139.png" alt="image-20220319143940139" style="zoom:80%;" />



### 解决超卖问题

解决超卖问题的办法就是加锁，悲观锁和乐观锁是两种锁的思想

悲观锁使线程串行执行，因此保证了安全性，但是性能较低

乐观锁并不加锁，只是在更新时判断数据有没有被其他线程修改过

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319155052028.png" alt="image-20220319155052028" style="zoom:80%;" />

#### 使用乐观锁解决超卖问题

乐观锁的关键是判断之前得到的数据是否有被修改过，常见的方式有两种

+   版本号法 —— 给数据加上版本，在并发时基于版本号判断是否被修改过
+   CAS 方案 —— 直接用数据作为判断依据，可能会存在成功率低的问题，也存在 ABA 问题，解决 CAS 成功率低的问题可以将数据分为若干份，为每份数据加锁，这样分段锁的措施可以提高成功率



##### CAS 的特殊使用

在写数据进行再次判断时，判断条件不必是前后数值一致，只需库存大于0即可放行

```java
boolean ifSuccess = seckillVoucherService.update()
        .setSql("stock=stock-1")
        .eq("voucher_id", voucherId).gt("stock", 0).update();
```



### 一人一单的需求

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319164018742.png" alt="image-20220319164018742" style="zoom:80%;" />

为防止用户快速访问而造成的计数问题，采用悲观锁解决，将一人一单校验、判断库存、扣减库存和创建订单的操作封装在一个方法中

```java
@Transactional
public Result createVoucherOrder(Long voucherId) {
    // todo 一人一单
    // todo 查询订单
    Long userId = UserHolder.getUser().getId();

    // 使用 synchronized 锁用户ID，仅当同一个用户访问时才会上锁
    // 使用 intern() 会返回字符串对象的规范表示，使用时将会在字符串常量池中寻找值相同的对象
    synchronized (userId.toString().intern()){
        Integer count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        // todo 判断是否存在
        if (count>0){
            return Result.fail("您已经购买过了");
        }

        // 5.扣减库存
        boolean ifSuccess = seckillVoucherService.update()
                .setSql("stock=stock-1")
                .eq("voucher_id", voucherId).gt("stock", 0).update();
        if (!ifSuccess){
            //扣减库存失败
            return Result.fail("商品已经售完！");
        }

        // 6.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        // 订单id
        Long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 用户id
        voucherOrder.setUserId(userId);
        // 代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        // 7.返回订单id
        return Result.ok(orderId);
    }
}
```



#### 一人一单的并发安全问题

通过加锁可以解决在单机情况下的一人一单安全问题，但是在集群模式下就不行了

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220319171017969.png" alt="image-20220319171017969" style="zoom:80%;" />



## 基于 Redis 实现分布式锁

### 初级版本

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320164250163.png" alt="image-20220320164250163" style="zoom:80%;" />

#### 实现

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220320165001399.png" alt="image-20220320165001399" style="zoom:80%;" />



```java
public class SimpleRedisLock implements ILock{
    private StringRedisTemplate stringRedisTemplate;
    private String serviceName;
    private static final String KEY_PREFIX="lock:";

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String serviceName) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.serviceName = serviceName;
    }

    @Override
    public boolean tryLock(long timeoutSec) {
        // 获取线程标识，作为 value 记录
        long threadId = Thread.currentThread().getId();
        // 获取锁
        Boolean ifSuccess = stringRedisTemplate.opsForValue().setIfAbsent(KEY_PREFIX + serviceName, threadId+"", timeoutSec, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(ifSuccess);
    }

    @Override
    public void unlock() {
        // 释放锁
        stringRedisTemplate.delete(KEY_PREFIX+serviceName);
    }
}
```



## Redis 优化秒杀业务

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321185520075.png" alt="image-20220321185520075" style="zoom:80%;" />

使用如下的架构来优化秒杀，用户只需要和 Redis 进行交互，后端的 Tomcat 异步地从 Redis 的阻塞队列中进行操作

![image-20220321183041052](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321183041052.png)

在 Redis 中存储商品库存以及用户集合来保证库存判断和一人一单的需求

这种架构缩短也业务流程，提高了业务的并发，还减轻了数据库的压力

![image-20220321185301343](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220321185301343.png)

java方法

```java
@Service
public class VoucherOrderServiceImpl extends ServiceImpl<VoucherOrderMapper, VoucherOrder> implements IVoucherOrderService {

    @Resource
    private ISeckillVoucherService seckillVoucherService;

    @Resource
    private RedisIdWorker redisIdWorker;

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Resource
    private RedissonClient redissonClient;

    private static final DefaultRedisScript<Long> SECKILL_SCRIPT;
    private BlockingQueue<VoucherOrder> orderTaskQueue =new ArrayBlockingQueue<>(1024*1024);
    private static final ExecutorService EXECUTOR_SERVICE= Executors.newSingleThreadExecutor();
    static {
        SECKILL_SCRIPT=new DefaultRedisScript<>();
        SECKILL_SCRIPT.setLocation(new ClassPathResource("seckill.lua"));
        SECKILL_SCRIPT.setResultType(Long.class);
    }

    // 这个注解的意思是在当前类初始化完毕后开始执行
    @PostConstruct
    private void init(){
        EXECUTOR_SERVICE.submit(new VoucherOrderHandler());
    }

    // 数据库读写异步任务
    private class VoucherOrderHandler implements Runnable{

        @Override
        public void run() {
            // while(true)保持监听
            while (true){
                try {
                    // 1.获取队列中的订单信息
                    VoucherOrder order = orderTaskQueue.take();
                    // 2.创建订单
                    createVoucherOrder(order);
                } catch (InterruptedException e) {
                    log.error("处理订单异常：",e);
                }
            }
        }
    }

    // 创建订单方法
    public void createVoucherOrder(VoucherOrder voucherOrder) throws InterruptedException {
        Long userId = voucherOrder.getUserId();
        Long voucherId = voucherOrder.getVoucherId();

        // 使用 redisson
        // 创建锁对象
        RLock lock = redissonClient.getLock("lock:order:" + userId);

        // 尝试获取锁
        boolean ifLock = lock.tryLock(1L,TimeUnit.SECONDS);
        // 判断是否获取到了锁
        if (!ifLock){
            log.error("不允许重复下单！");
            return;
        }

        try {
            Integer count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
            // todo 判断是否存在
            if (count > 0) {
                log.error("不允许重复下单！");
                return;
            }

            // 5.扣减库存
            boolean ifSuccess = seckillVoucherService.update()
                    .setSql("stock=stock-1")
                    .eq("voucher_id", voucherId).gt("stock", 0).update();
            if (!ifSuccess) {
                //扣减库存失败
                log.error("商品已经售完");
                return;
            }

            // 6.保存订单
            save(voucherOrder);
            log.debug(voucherOrder.toString());
        } finally {
            // 释放锁
            lock.unlock();
        }
    }
    
	// 主体方法
    @Override
    public Result seckillVoucher(Long voucherId){
        Long userId = UserHolder.getUser().getId();
        // 1.执行lua脚本
        Long result = stringRedisTemplate.execute(SECKILL_SCRIPT, Collections.EMPTY_LIST, voucherId.toString(), userId.toString());
        // 2.判断结果是否为0
        int rez = result.intValue();
        if (rez!=0){
            // 2.1.不为0，则该用户已经购买过
            return Result.fail(rez==1?"库存不足":"不能重复购买！");
        }
        // 2.2.为0则加入阻塞队列
        // 2.2.1.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        // 订单id
        Long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 用户id
        voucherOrder.setUserId(userId);
        // 代金券id
        voucherOrder.setVoucherId(voucherId);

        // 2.3.创建阻塞队列
        orderTaskQueue.add(voucherOrder);

        // 3.返回订单id
        return Result.ok(orderId);
    }
}
```

lua脚本

```lua
-- 1.参数列表
-- 1.1.优惠券id
local voucherId = ARGV[1]
-- 1.2.用户id
local userId = ARGV[2]

-- 2.数据key
-- 2.1.库存key
local stockKey = 'seckill:stock:' .. voucherId
-- 2.2.订单key
local orderKey = 'seckill:order:' .. voucherId

-- 3.脚本业务
-- 3.1.判断库存是否充足 get stockKey
if (tonumber(redis.call('get',stockKey))<=0) then
    -- 3.2.库存不足返回1
    return 1;
end

-- 3.2.判断用户是否下单
if (redis.call('sismember',orderKey,userId) == 1) then
    -- 3.3.存在，说明用户重复下单
    return 2;
end

-- 3.4.扣库存
redis.call('incrby',stockKey,-1);

-- 3.5.下单，把userId添加到set中
redis.call('sadd',orderKey,userId);

return 0;
```



## 使用 Redis 消息队列实现异步秒杀

![image-20220322183355764](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220322183355764.png)

改进后的 Redis 与 数据库 异步的方式存在着一个问题：数据库将 task 从阻塞队列取出后如果出现错误，那么这个 task 将会永远丢失

可以使用消息队列来解决这个问题

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220322144850558.png" alt="image-20220322144850558" style="zoom:80%;" />

使用基于Stream的消费组的消息队列来实现，基本思路如下

```java
while(true){
	// 尝试监听队列，使用阻塞模式，最长等待2000秒
    Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >");
    if(msg == null){	// null说明没有消息，继续监听
        continue;
    }
    try{
        // 处理消息，完成后一定要ACK
        handleMessage(msg);
    } catch(Exception e){
        while(true){
            Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 STREAMS s1 0");
            if(msg == null){	// null说明没有异常消息，所有消息都已确认，结束循环
                break;
            }
            try{
                // 有异常消息，处理异常
                handleMessage(msg);
            } catch(Exception e){
                // 再次出现异常，记录日志，继续循环
                continue;
            }
        }
    }
}
```



## 发布图片与文字

在上传图片时，请求专用的接口将图片上传到服务器，然后将其地址返回，在页面根据地址显示图片，再提交时将图片地址和文字一并提交

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220323095021131.png" alt="image-20220323095021131" style="zoom:80%;" />



## 点赞功能

![image-20220323103448891](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220323103448891.png)



## 点赞用户排行榜

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220323173413772.png" alt="image-20220323173413772" style="zoom:80%;" />

从对比图来看，使用 SortedSet 类型完成此功能最合适





## 关注与取关

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220323184859502.png" alt="image-20220323184859502" style="zoom:80%;" />





## 共同关注

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220323192903396.png" alt="image-20220323192903396" style="zoom:80%;" />

使用集合运算可以求交集来解决这个问题，每次关注其他用户的时候将其他用户添加到自己关注的集合





## 基于推模式实现关注推送

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324102426876.png" alt="image-20220324102426876" style="zoom:80%;" />

虽然 List 和 SortedSet 都能够进行排序和分页，但是考虑到会新增添数据，所以不能使用传统的按角标分页，需要记录下每次查询的最后一个角标，下一次从这里开始查询，而 SortedSet 恰好支持这样的特性，List 则不支持

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324103047966.png" alt="image-20220324103047966" style="zoom:80%;" />





## 附近商户功能的实现

位置相关的需求可以借用 Redis 的 GEO 数据类型来实现

SpringDataRedis 的 2.3.9 版本并不支持 Redis 6.2 提供的 GEOSEARCH 命令，因此我们需要提示其版本，修改自己的 POM 文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
        </exclusion>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework.data/spring-data-redis -->
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>2.6.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.lettuce/lettuce-core -->
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <version>6.1.6.RELEASE</version>
</dependency>
```

改造方法

```java
@Override
public Result queryShopByType(Integer typeId, Integer current, Double x, Double y) {
    // 判断是否需要根据坐标查询
    if (x==null|y==null){
        // 不需要坐标查询，按数据库查询
        Page<Shop> page = query()
                .eq("type_id", typeId)
                .page(new Page<>(current, SystemConstants.DEFAULT_PAGE_SIZE));
    }
    // 计算分页参数
    int from = (current -1 )*SystemConstants.DEFAULT_PAGE_SIZE;
    int end = current * SystemConstants.DEFAULT_PAGE_SIZE;

    // 查询redis，按照距离排序、分页。结果：shopId、distance     GEOSEARCH key BYLONLAT x y BYRADIUS 10 WITHDISTANCE
    String key= SHOP_GEO_KEY+typeId;
    GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate.opsForGeo()
            .search(
                    key,
                    GeoReference.fromCoordinate(new Point(x, y)),
                    new Distance(5000),
                    RedisGeoCommands.GeoSearchCommandArgs.newGeoSearchArgs().includeDistance().limit(end)
            );

    // 解析出id
    if (results==null){
        return Result.ok();
    }
    List<GeoResult<RedisGeoCommands.GeoLocation<String>>> contentList = results.getContent();
    if (contentList.size()<=from){
        // 没有下一页了，结束
        return Result.ok(Collections.EMPTY_LIST);
    }
    // 截取从 from 到 end 的部分
    List<Long> ids = new ArrayList<>(contentList.size());
    Map<String,Distance> distanceMap = new HashMap<>(contentList.size());
    contentList.stream().skip(from).forEach(result->{
        // 获取店铺id
        String shopIdStr = result.getContent().getName();
        ids.add(Long.valueOf(shopIdStr));
        // 获取距离
        Distance distance = result.getDistance();
        distanceMap.put(shopIdStr, distance);
    });
    // 根据id查询店铺shop
    String idStr = StrUtil.join(",", ids);
    List<Shop> shopList = query().in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list();

    for (Shop shop:shopList){
        shop.setDistance(distanceMap.get(shop.getId().toString()).getValue());
    }

    // 返回
    return Result.ok(shopList);
}
```





## 用户签到

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324202702144.png" alt="image-20220324202702144" style="zoom:80%;" />

可以使用 BitMap 这种数据结构，将一串二进制数字与实际事物形成关联，这样就可以用极少的数据量来记录信息了

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324195521499.png" alt="image-20220324195521499" style="zoom:80%;" />

java实现

```java
@Override
public Result sign() {
    // 获取当前登录的用户
    Long userId = UserHolder.getUser().getId();

    // 获取日期
    LocalDateTime dateTime = LocalDateTime.now();
    // 拼接key
    String keySuffix = dateTime.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 获取今日是本月的第几天
    int dayOfMonth = dateTime.getDayOfMonth();
    // 设置签到状态   SETBIT key offset 1
    stringRedisTemplate.opsForValue().setBit(key, dayOfMonth-1, true);
    return Result.ok();
}
```





## 签到统计

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220324210131447.png" alt="image-20220324210131447" style="zoom:80%;" />

java实现

```java
@Override
public Result signCount() {
    // 获取当前登录的用户
    Long userId = UserHolder.getUser().getId();
    // 获取日期
    LocalDateTime dateTime = LocalDateTime.now();
    // 拼接key
    String keySuffix = dateTime.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 获取今日是本月的第几天
    int dayOfMonth = dateTime.getDayOfMonth();
    // 获取本月截止今天的签到记录，返回的是一个十进制数字    BITFIELD sign:5:202203 GET u14 0
    List<Long> result = stringRedisTemplate.opsForValue().bitField(
            key,
            BitFieldSubCommands.create()
                    .get(BitFieldSubCommands.BitFieldType.unsigned(dayOfMonth))
                    .valueAt(0)
    );

    if (result==null||result.isEmpty()){
        return Result.ok(0);
    }
    Long num = result.get(0);
    if (num==null || num ==0){
        return Result.ok(0);
    }
    int count =0;
    // 循环遍历
    while (true){
        // 让这个数字与1做与运算，得到数字的最后一个bit位
        // 判断这个bit位是否为0
        if ((num & 1)==0){
            // 如果为0，说明未签到，结束
            break;
        }else {
            // 如果不为0，说明已签到，计数器+1
            count++;
        }
        // 把数字右移一位，继续下一个bit位
        num=num>>>1;
    }
    return Result.ok(count);
}
```





## UV统计

+   **UV**：全称 Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。一天内同一个用户多次访问该网站，只记录一次
+   **PV**：全称 Page View，也叫页面访问量或点击量，用户每访问网站的一个页面，记录一次 PV，用户多次打开页面，则记录多次 PV 。往往用来衡量网站的流量



UV 统计在服务端做会比较麻烦，因为要判断该用户是否已经统计过了，需要将统计过的用户信息保存。但是如果每个访问的用户都保存在 Redis 中，数据量会非常恐怖

java实现如下

```java
@Test
void testHyperLogLog() {
    String[] values = new String[1000];
    int j=0;
    for (int i=0;i<1000000;i++){
        j = i%1000;
        values[j] = "user_"+i;
        if (j ==999){
            stringRedisTemplate.opsForHyperLogLog().add("hl2", values);
        }
    }
    Long count = stringRedisTemplate.opsForHyperLogLog().size("hl2");
    System.out.println("count = "+count);
}
```



## 实现商品的查询的本地进程缓存

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220330192859672.png" alt="image-20220330192859672" style="zoom:80%;" />

定义缓存

```java
@Configuration
public class CaffeineConfig {

    @Bean
    public Cache<Long, Item> itemCache(){
        return Caffeine.newBuilder()
                .initialCapacity(100)
                .maximumSize(10_000)
                .build();
    }

    @Bean
    public Cache<Long, ItemStock> stockCache(){
        return Caffeine.newBuilder()
                .initialCapacity(100)
                .maximumSize(10_000)
                .build();
    }
}
```

使用 get 方法，利用其查询缓存未命中则执行数据库查询操作的特性

```java
@GetMapping("/{id}")
public Item findById(@PathVariable("id") Long id){
    return itemCache.get(id,thisId-> itemService.query()
            .ne("status", 3).eq("id", thisId)
            .one());
}

@GetMapping("/stock/{id}")
public ItemStock findStockById(@PathVariable("id") Long id){
    return stockCache.get(id, thisId->stockService.getById(thisId));
}
```



## 使用 OpenResty 返回静态数据

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331153023803.png" alt="image-20220331153023803" style="zoom:80%;" />

实现需求的步骤如下：

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331153332586.png" alt="image-20220331153332586" style="zoom:80%;" />

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331154448879.png" alt="image-20220331154448879" style="zoom:80%;" />



## 使用 OpenResty 接收参数

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331162153938.png" alt="image-20220331162153938" style="zoom:80%;" />

修改 OpenResty 的 nginx.conf：

```nginx
# 波浪线左右都要有空格，使用正则表达式来匹配参数
location ~ /api/item/(\d+) {
                # 默认的相应类型
                default_type application/json;
                # 响应结果由lua/item.lua文件来决定
                content_by_lua_file lua/item.lua;
}

```

编写 lua 脚本

```lua
-- 获取路径参数
local id = ngx.var[1]
-- 返回结果
ngx.say('{"id":'..id..',"name":"SALSA AIR","title":"RIMOWA 210寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":199900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```





## OpenResty 查询 Tomcat

![image-20220331165521659](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331165521659.png)

把http查询的请求封装为一个函数，放到 OpenResty 函数库汇总，方便后期使用。

在/usr/local/openresty/lualib 目录下创建 common.lua 文件：

```sh
touch /usr/local/openresty/lualib/common.lua
```

在 common.lua 中封装 http 查询的函数

```lua
-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
    local resp = ngx.location.capture(path,{
        method = ngx.HTTP_GET,
        args = params,
    })
    if not resp then
        -- 记录错误信息，返回404
        ngx.log(ngx.ERR, "http not found, path: ", path , ", args: ", args)
        ngx.exit(404)
    end
    return resp.body
end
-- 将方法导出
local _M = {  
    read_http = read_http
}  
return _M
```

nginx.conf 中添加模块配置

```nginx
#lua 模块
lua_package_path "/usr/local/openresty/lualib/?.lua;;";
#c模块     
lua_package_cpath "/usr/local/openresty/lualib/?.so;;";  
```

编写 lua 脚本，查询到商品和库存信息，并将这两部分数据组装，需要用到 JSON 处理函数库：

```lua
-- 导入commmon函数库
local common = require('common')
local read_http = common.read_http
-- 导入cjson库
local cjson = require('cjson')

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_http("/item/" .. id, nil)

-- 查询库存信息
local stockJSON = read_http("/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)

--组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为JSON，返回结果
ngx.say(cjson.encode(item))
```



## OpenResty 访问 Tomcat 集群

使用 nginx 的 `request_uri` 算法，对请求路径做哈希运算，可以保证相同请求路径永远都会向同一个服务器发出请求，这样就能保证较高的缓存命中率

![image-20220401124204780](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220401124204780.png)

配置 nginx.conf

1.   配置集群

```nginx
upstream tomcate-cluster {
    hash $request_uri;
    server 192.168.137.2:8081;
    server 192.168.137.2:8082;
}
```

2.   配置路径转发到集群

```nginx
location /item {
	proxy_pass http://tomcat-cluster*;*
}  
```



## OpenResty 使用 Redis 缓存

![image-20220401213313769](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220401213313769.png)

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220401210544514.png" alt="image-20220401210544514" style="zoom:80%;" />

修改后的 item.lua

```lua
-- 导入commmon函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 导入cjson库
local cjson = require('cjson')

-- 封装查询函数
function read_data(key,path,params)
    -- 查询redis
    local resp=read_redis("127.0.0.1",6379,key)
    -- 判断查询结果
    if not resp then
        ngx.log(ngx.INFO, "redis查询失败，尝试查询http，key：", key)
        -- redis查询失败，去查询http
        resp=read_http(path,params)
    end
    return resp
end

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_data("item:id:"..id,"/item/" .. id, nil)

-- 查询库存信息
local stockJSON = read_data("item:stock:id:"..id,"/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)

--组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为JSON，返回结果
ngx.say(cjson.encode(item))
```



## OpenResty 使用本地缓存

![image-20220401215514697](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220401215514697.png)
