# Springboot redis

> 其实东西就那么多,但是说的很全面,为什么这样技术选型,花费了很多时间,现把主要内容总结下
>
> 总算是找到`Bean not found`报错的问题啦,命令可以运行,主要是我们使用jedis,使用默认就不会报错

## Lettuce >> Jedis

主流的使用Java操作redis的第三方库:**Lettuce,Jedis,Redisson.**

**Spring Data Redis 通过提供统一的模板去调用它们的API,**我们在上层编写代码时,是感受不到差异的.

- 对于下层，Spring Data Redis 提供了统一的操作模板（后文中，我们会看到是 RedisTemplate 类），封装了 Jedis、Lettuce 的 API 操作，访问 Redis 数据。所以，**实际上，Spring Data Redis 内置真正访问的实际是 Jedis、Lettuce 等 API 操作**。
- 对于上层，开发者学习如何使用 Spring Data Redis 即可，而无需关心 Jedis、Lettuce 的 API 操作。甚至，未来如果我们想将 Redis 访问从 Jedis 迁移成 Lettuce 来，无需做任何的变动。
- 目前，Spring Data Redis 暂时只支持 Jedis、Lettuce 的内部封装，而 Redisson 是由 [redisson-spring-data](https://github.com/redisson/redisson/tree/master/redisson-spring-data) 来提供。

### 使用Jedis

> 这一步是非必须的,视实际情况而定.
>
> springboot现在可以客户端类型啦,有jedis和Lettuce可选,芋艿的方式可能过时啦

由于Spring优先使用Lettuce作为redis客户端,所以想要使用jedis还要作一番配置,主要是`application.yml`

```yaml
spring:  
# 对应 RedisProperties 类  
	redis:    
		host: 127.0.0.1
        port: 6379
        password: # Redis 服务器密码，默认为空。生产中，一定要设置 Redis 密码！
        database: 0 # Redis 数据库号，默认为 0 。
        timeout: 400 # Redis 连接超时时间，单位：毫秒。
        client-type:jedis
        # 对应 RedisProperties.Jedis 内部类
        jedis:
        	pool:
            	max-active: 8 # 连接池最大连接数，默认为 8 。使用负数表示没有限制。
                max-idle: 8 # 默认连接数最小空闲的连接数，默认为 8 。使用负数表示没有限制。
                min-idle: 0 # 默认连接池最小空闲的连接数，默认为 0 。允许设置 0 和 正数。
                max-wait: -1 # 连接池最大阻塞等待时间，单位：毫秒。默认为 -1 ，表示不限制
```

## 序列化方式

> 其实这里有两种处理方式:
>
> 1. `key-value`都采用字符串的序列化方式,使用时我们自己完成序列化过程,例如:使用**fastjson**等工具库,存入时将对象转化为json字符串存入;取出时,将json字符串解析成对象
> 2. 采用上述方案,主要使用的是`StringRedisTemplate`这个模板,但是在注入时会报错,使用时没有问题
> 3. 使用自定义的`RedisTemplate<String,Object>`,自己配置序列化方式,但是有个问题就是:取出的对象都是Object,需要强制类型转化.
>
> 第一种方式简单明了,但是每次都要写序列化和反序列化;
>
> 第二种方式使用方便,但是写代码不好看

### 字符序列化 + 手动转化

```Java
private final static String KEY_PATTERN = "user:%d";

private final StringRedisTemplate stringRedisTemplate;
private final ValueOperations<String, String> valueOperations;

@SuppressWarnings("SpringJavaInjectionPointsAutowiringInspection")
public UserCacheDAO(StringRedisTemplate stringRedisTemplate) {
    // 实际是能注入的,但是老是报找不到,应该是源码中没有Component修饰
    // 但是在实际是被加载到spring容器的
    this.stringRedisTemplate = stringRedisTemplate;
    this.valueOperations = stringRedisTemplate.opsForValue();
}

public UserCacheObject get(Integer id) {
    String key = generateKey(id);
    String value = valueOperations.get(key);
    return JSONUtil.parseObject(value, UserCacheObject.class);
}

public void set(UserCacheObject userCacheObject) {
    String key = generateKey(userCacheObject.getId());
    String value = JSONUtil.toJsonString(userCacheObject);
    valueOperations.set(key, value, 60, TimeUnit.SECONDS);
}
```

使用的StringRedisTemplate,这里自动注入会报错,但是可以正常运行,只能手动压制啦

### 自定义RedisTemplate

springboot默认的json序列化方式是`GenericJackson2JsonRedisSerializer`,不指定对象类型的化,则使用传入对象的类全名，作为默认类型（Default Typing）。

那么，胖友可能会问题，什么是**默认类型（Default Typing）**呢？我们来思考下，在将一个对象序列化成一个字符串，怎么保证字符串反序列化成对象的类型呢？Jackson 通过 Default Typing ，会在字符串多冗余一个类型，这样反序列化就知道具体的类型了。

```json
{
       "@class": "cn.iocoder.springboot.labs.lab10.springdatarediswithjedis.cacheobject.UserCacheObject",
       "id": 1,
       "name": "芋道源码",
       "gender": 1
   }
```

上次代码合并时,就遇到过这样的问题,芋艿的解决方案是改源码,但是好像并不需要

```java
@Configuration
public class RedisConfiguration {

    //从RedisAutoConfiguration复制模板过来
    //修改成自己的
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //为了平时开发方便,使用<String,Object>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        //连接Redis工厂
        template.setConnectionFactory(redisConnectionFactory);

        //序列化操作 Json序列化
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        //转译
        ObjectMapper objectMapper = new ObjectMapper();
        //方便的方法允许更改底层VisibilityCheckers的配置，以更改自动检测哪些属性的详细信息
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
//        替换掉过期的方法
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance ,
                ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
//        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        // String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        //把所有的properties Set进去
        template.afterPropertiesSet();

        return template;
    }

}
```

修改一下Redis的配置类,主要使用`RedisTemplate<String,Object>`这个模板类

## 使用说明

> 我们主要通过**RedisTemplate**这个模板类和Redis交互,对于这个类要有一定的了解
>
> 主要是:键,值,哈希键,哈希值的序列化方式,Lua脚本执行器,Redis各种数据结构的操作类
>
> 为什么要指定序列化方式呢,redis内置的各种数据结构,很多是为了查询效率,数据的实际存储时字符串(字节数组),所以当我们存入了Java对象这样的数据时,就有了序列化和反序列化的过程,为了开发方便,一般不使用默认配置.
>
> 主要配置的就是序列化的方式

[`org.springframework.data.redis.core.RedisTemplate`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/RedisTemplate.java) 类，从类名上，我们就明明白白知道，提供 Redis 操作模板 API 。核心属性如下：

```java
// RedisTemplate.java
// 艿艿省略了一些不重要的属性。

// <1> 序列化相关属性
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer keySerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer valueSerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer hashKeySerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer hashValueSerializer = null;
private RedisSerializer<String> stringSerializer = RedisSerializer.string();

// <2> Lua 脚本执行器
private @Nullable ScriptExecutor<K> scriptExecutor;

// <3> 常见数据结构操作类
// cache singleton objects (where possible)
private @Nullable ValueOperations<K, V> valueOps;
private @Nullable ListOperations<K, V> listOps;
private @Nullable SetOperations<K, V> setOps;
private @Nullable ZSetOperations<K, V> zSetOps;
private @Nullable GeoOperations<K, V> geoOps;
private @Nullable HyperLogLogOperations<K, V> hllOps;
```

+ <1>处，看到了四个序列化相关的属性，用于 KEY 和 VALUE 的序列化。

- 例如说，我们在使用 POJO 对象存储到 Redis 中，一般情况下，会使用 JSON 方式序列化成字符串，存储到 Redis 中。详细的，我们在 [「3. 序列化」](https://www.iocoder.cn/Spring-Boot/Redis/?vip#) 小节中来说明。
- 在上文中，我们看到了 [`org.springframework.data.redis.core.StringRedisTemplate`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/StringRedisTemplate.java) 类，它继承 RedisTemplate 类，使用 [`org.springframework.data.redis.serializer.StringRedisSerializer`](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/serializer/StringRedisSerializer.java) 字符串序列化方式。直接点开 StringRedisSerializer 源码，看下它的构造方法，瞬间明明白白。

- `<2>` 处，Lua 脚本执行器，提供 [Redis scripting](http://redis.cn/commands.html#scripting) API 操作。
- <3>处，Redis 常见数据结构操作类。
  - [ValueOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/ValueOperations.java) 类，提供 [Redis String](http://redis.cn/commands.html#string) API 操作。
  - [ListOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/ListOperations.java) 类，提供 [Redis List](http://redis.cn/commands.html#list) API 操作。
  - [SetOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/SetOperations.java) 类，提供 [Redis Set](http://redis.cn/commands.html#set) API 操作。
  - [ZSetOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/ZSetOperations.java) 类，提供 [Redis ZSet(Sorted Set)](http://redis.cn/commands.html#sorted_set) API 操作。
  - [GeoOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/GeoOperations.java) 类，提供 [Redis Geo](http://redis.cn/commands.html#geo) API 操作。
  - [HyperLogLogOperations](https://github.com/spring-projects/spring-data-redis/blob/master/src/main/java/org/springframework/data/redis/core/HyperLogLogOperations.java) 类，提供 [Redis HyperLogLog](http://redis.cn/commands.html#hyperloglog) API 操作。

那么 Pub/Sub、Transaction、Pipeline、Keys、Cluster、Connection 等相关的 API 操作呢？它在 RedisTemplate 自身提供，因为它们不属于具体每一种数据结构，所以没有封装在对应的 Operations 类中.

## 工程实践

### Cache Object

在我们使用数据库时，我们会创建 `dataobject` 包，存放 DO（Data Object）数据库实体对象。

那么同理，我们缓存对象，怎么进行对应呢？对于复杂的缓存对象，我们创建了 `cacheobject` 包，和 `dataobject` 包同层。如：

```
service # 业务逻辑层
dao # 数据库访问层
dataobject # DO
cacheobject # 缓存对象
```

并且所有的 Cache Object 对象使用 CacheObject 结尾，例如说 UserCacheObject、ProductCacheObject 。

###  数据访问层

在我们访问数据库时，我们会创建 `dao` 包，存放每个 DO 对应的 Dao 对应。那么对于每一个 CacheObject 类，我们也会创建一个其对应的 Dao 类。例如说，UserCacheObject 对应 UserCacheObjectDao 类。示例代码如下：

```java
@Repository
public class UserCacheDao {

    private static final String KEY_PATTERN = "user:%d"; // user:用户编号 <1>

    @Resource(name = "redisTemplate")
    @SuppressWarnings("SpringJavaInjectionPointsAutowiringInspection")
    private ValueOperations<String, String> operations; // <2>

    private static String buildKey(Integer id) { // <3>
        return String.format(KEY_PATTERN, id);
    }

    public UserCacheObject get(Integer id) {
        String key = buildKey(id);
        String value = operations.get(key);
        return JSONUtil.parseObject(value, UserCacheObject.class);
    }

    public void set(Integer id, UserCacheObject object) {
        String key = buildKey(id);
        String value = JSONUtil.toJSONString(object);
        operations.set(key, value);
    }

}
```

- `<1>` 处，通过静态变量，声明 KEY 的前缀，并且使用冒号作为间隔
- `<3>` 处，声明 `KEY_PATTERN` 对应的 KEY 拼接方法，避免散落在每个方法中。
- `<2>` 处，通过 `@Resource` 注入指定名字的 RedisTemplate 对应的 Operations 对象，这样明确每个 KEY 的类型。
- 剩余的，就是每个方法封装对应的操作。

可能会有胖友问，为什么不支持将 RedisTemplate 直接 Service 业务层调用呢？如果这样，我们业务代码里，就容易混杂着很多 Redis 访问代码的细节，导致很脏乱。我们试着把 RedisTemplate 想象成 Spring JDBCTemplate ，我们一定会声明对应的 Dao 类，访问数据库。所以，同理落。

那么还有一个问题，UserCacheDao 放在哪个包下？目前的想法是，将 `dao` 包下拆成 `mysql`、`redis` 包。这样，MySQL 相关的 Dao 放在 `mysql` 包下，Redis 相关的 Dao 放在 `redis` 。

##  序列化

在 [「3. 序列化」](https://www.iocoder.cn/Spring-Boot/Redis/?vip#) 小节中，我们仔细翻看了每个序列化方式，暂时没有一个能够完美的契合我们的需求，所以我们直接使用最简单的 **StringRedisSerializer** 作为序列化实现类。而真正的序列化，我们在各个 Dao 类里，自己**手动**来调用。

例如说，在 UserCacheDao 示例中，已经看到了这么做了。这里还有一个细化点，虽然我们是自己**手动**序列化，可以自己简单封装一个 [JSONUtil](https://github.com/YunaiV/SpringBoot-Labs/blob/master/lab-11-spring-data-redis/lab-07-spring-data-redis-with-jedis/src/main/java/cn/iocoder/springboot/labs/lab10/springdatarediswithjedis/util/JSONUtil.java) 类，未来如果我们想换 JSON 库，就比较方便了。其实，这个和 Spring Data Redis 所做的封装是一个思路

