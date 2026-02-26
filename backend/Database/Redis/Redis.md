Redis是一种键值型的NoSql数据库：
- 键值型
- NoSql
	- Key-Value
	- Document
	- Graph
- 单线程，每个命令具备原子性
- 低延迟，速度快
- 数据持久化
- 主从集群、分片集群
默认安装到usr/local/bin
- `redis-cli`：是redis提供的命令行客户端
- `redis-server`：是redis的服务端启动脚本
- `redis-sentinel`：是redis的哨兵启动脚本
```shell
#监听的地址，默认是127.0。0。1，会导致只能在本地访问。修改为0。0。0。0则可以在任意P访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
#守护进程，修改为yes后即回后台运行
daemonize yes
#密码，设置后访问Redis必须输人密码
requirepass 123321
#监听的端口
port 6379
#工作目录，默认是当前目录，也就是运行redis-server时的命令，日志、持久化等文件会保存在这个目录
dir.
#数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1
#设置redis能够使用的最大内存
maxmemory 512mb
#日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```
```config
[Unit]
Description=redis-server
After=network.target
[Service]Type=forking
ExecStart=/usr/local/bin/redis-server /opt/soft/redis-8.4.0/redis.confPrivateTmp=true
[Install]
WantedBy=multi-user.target
```
## 命令客户端
```shell
redis-cli [option] [commonds]
```
- `option`
	- -h x.x.x.x: 指定要链接的redis节点IP地址，默认127.0.0.1
	- -p xxxx: 指定连接redis节点的端口。默认是6379
	- -a xxxx: 指定访问密码
- `commonds`
	- ping：与redis服务端做心跳测试，服务端正方返回pong
- 不指定commonds进入redis-cli的交互控制台
- 第三方客户端
## 命令
- 数据结构

| key         | value                   |     |
| ----------- | ----------------------- | --- |
| String      | hello world             |     |
| Hash        | {name: "Jack", age: 21} |     |
| List        | [A -> B->C -> C]        |     |
| Set         | {A, B, C}               |     |
| SortededSet | {A: 1,B: 2, C:3}        |     |
| GEO         | {A:(120.3,30.5) }       | 特殊  |
| BitMap      | 0110110101110101011     |     |
| HyperLog    | 0110110101110101011     |     |
- `help @generic`：查看通用命令
- `help [command]`：查看一个命令的具体用法
### 通用命令
- `KEYS`：查看符合模板的所有key，不建议在生产环境上使用
- `del`：删除键
- `mset`：批量插入
- `exists`：判断key是否存在
- `expipe`：给key设置有效期，有效期到期自动删除
- `ttl`：查看剩余有效期
### String
- value是字符串
	- `string`：普通字符串
	- `int`：整数类型，可自增、自减
	- `float`：浮点类型，可自增、自建
	- 最大空间不超过512m
- String的常见命令有：
	- `SET`：添加或者修改已经存在的一个String类型的键值对
	- `GET`:根据key获取String类型的value
	- `MSET`：批量添加多个String类型的键值对
	- `MGET`：根据多个key获取多个String类型的value
	- `INCR`：让一个整型的key自增1
	- `INCRBY`:让一个整型的key自增并指定步长，例如：incrby num 2 让num值自增2
	- `INCRBYFLOAT`：让一个浮点类型的数字自增并指定步长
	- `SETNX`：添加一个String类型的键值对，前提是这个key不存在，否则不执行
	- `SETEX`：添加一个String类型的键值对，并且指定有效期
### key的层级格式
- 多个单词之间用 ':' 隔开
- 格式：项目名:业务名:类型:id
### hash类型
- 散列，无序字典，类似HashMap
- Hash的常见命令有：
	- `HSET key field value`：添加或者修改hash类型key的field的值
	- `HGET key field`： 获取一个hash类型key的field的值
	- `HMSET`：批量添加多个hash类型key的field的值
	- `HMGET`：批量获取多个hash类型key的field的值
	- `HGETALL`：获取一个hash类型的key中的所有的field和value
	- `HKEYS`：获取一个hash类型的key中的所有的field
	- `HVALS`：获取一个hash类型的key中的所有的value
	- `HINCRBY`：让一个hash类型key的字段值自增并指定步长
	- `HSETNX`：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行
### List类型
- LinkedList双向链表
- 特点
	- 有序
	- 可重复
	- 插入删除快
	- 速度一般
- List的常见命令有：
	- `LPUSH key element ...`：向列表左侧插入一个或多个元素
	- `LPOP key`：移除并返回列表左侧的第一个元素，没有则返回nil
	- `RPUSH key element.`：向列表右侧插入一个或多个元素
	- `RPOP key`：移除并返回列表右侧的第一个元素
	- `LRANGE key star end`： 返回一段角标范围内的所有元素
	- `BLPOP`和`BRPOP`：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil
### Set类型
- HashSet，value为null的HashMap
	- 无序
	- 不可重复
	- 快
	- 支持交集、并集、差集等
- `SADD key member ..`：向set中添加一个或多个元素
- `SREM key member` .:移除set中的指定元素
- `SCARDkoy`： 返回set中元素的个数
- `SISMEMBER key member`:判断一个元素是否存在于set中
- `SMEMBERS`：获取set中的所有元素
- `SINTER key1 key2 ...`: 求key1与key2的交集
- `SDIFF key1 key2 ...` : 求key1与key2的差集
- `SUNiON key1 key2 ..`: 求key1和key2的并集
### SortedSet类型
- 类似TreeSet，每个元素都带有一个score属性，基于score属性对元素排序，底层实现是跳表加哈希表
	- 可排序
	- 不重复
	- 速度快
- `ZADDkey score member`：添加一个或多个元素到sorted set，如果已经存在则更新其score值
- `ZREM key member`: 删除sorted set中的一个指定元素
- `ZScoRE key member` :获取sorted set中的指定元素的score值
- `ZRANK key member`: 获取sorted set 中的指定元素的排名
- `ZCARDkey`： 获取sorted set中的元素个数
- `ZCOuNT key min max`：统计score值在给定范围内的所有元素的个数
- `ZINcRBY key increment member`: 让sorted set中的指定元素自增，步长为指定的increment值
- `ZRANGE key min max`：按照score排序后，获取指定排名范围内的元素
- `ZRANGEBYSCORE key min max`: 按照score排序后，获取指定score范围内的元素
- `ZDIFF、ZINTER、ZUNION`： 求差集、交集、并集
- 排名默认升序，降序须在命令Z后面加REV
# java客户端
- Jedis：以Redis命令作为方法名称，线程不安全，需连接池
- lettuce：基于 Netty 的线程安全，支持异步和响应式编程模型，哨兵、集群、管道模式
- Pedisson：基于redis实现的分布式、可伸缩的java数据结构
## Jedis
1. 引入依赖
```maven
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>3.7.0</version>
</dependency>
```
2. 建立连接
```java
private Jedis jedis;
@BeforeEachvoid setUp() {
	//建立连接
	jedis = new Jedis("192.168.150.101", 6379);
	//设置密码
	jedis.auth("123321") ;
	//选择库
	jedis.select(0) ;
}
```
3. 测试string
```java
@Test
void testString() {
	//插入数据，方法名称就是redis命令名称，非常简单
	String result = jedis.set("name", "张三");
	System.out.println("result = " + result);
	//获取数据
	String name = jedis.get("name");
	System.out.println("name = " + name) ;
}
```
4. 释放资源
```java
@AfterEach
void tearDown() {
	//释放资源
	if (jedis != null){
		jedis.close();
	}
}
```
### jedis线程池
```java
public class JedisConnectionFactory {
	private static final JedisPool jedisPool;
	static {
		JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
		//最大连接
		jedisPoolConfig.setMaxTotal(8);
		//最大空闲连接
		jedisPoolConfig.setMaxIdle(8) ;
		//最小空闲连接
		jedisPoolConfig.setMinIdle(0);
		//设置最长等待时间，ms
		jedisPoolConfig.setMaxWaitMillis(200) ;
		jedisPool = new JedisPool(jedisPoolConfig, "192.168.150.101", 6379,1000,"123321");
	}
	//获取Jedis对象
	public static Jedis getJedis(){
		return jedisPool.getResource();
	}
}
```
## SpringDataRedis
- SpringData是Spring中数据操作的模块，包含对各种数据库的集成
- 提供了对不同Redis客户端的整合（Lettuce和jedis）
- 提供了RedisTemplate统一APl来操作Redis
- 支持Redis的发布订阅模型
- 支持Redis哨兵和Redis集群
- 支持基于Lettuce的响应式编程
- 支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化
- 支持基于Redis的jDKCollection实现
### RedisTemplate

| API                         | 返回值类型           | 说明              |
| --------------------------- | --------------- | --------------- |
| redisTemplate.opsForValue() | Value0perations | 操作String类型数据    |
| redisTemplate.opsForHash()  | Hashoperations  | 操作Hash类型数据      |
| redisTemplate.opsForList()  | Listoperations  | 操作List类型数据      |
| redisTemplate.opsForSet()   | SetOperations   | 操作Set类型数据       |
| redisTemplate.opsForZSet()  | ZSetoperations  | 操作SortedSet类型数据 |
| redisTemplate               |                 | 通用的命令           |
1. 引入依赖
```maven
<!--Redis依赖-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<！--连接池依赖-->
<dependency>
	<groupId>org.apache. commons</groupId>
	<artifactId>commons-pool2</artifactId>
</dependency>
```
2. 配置文件
```yaml
spring:
	redis:
	host: 192.168.150.101
	port: 6379
	password: 123321
	lettuce:
	pool:
		max-active： 8 # 最大连接
		max-idle：8 # 最大空闲连接
		min-idle：0 # 最小空闲连接
		max-wait：100 # 连接等待时间
```
3. 注入RedisTemplate
```java
@Autowired
private RedisTemplate redisTemplate;
```
4. 编写测试
```java
@SpringBootTest
public class RedisTest {
	@Autowired
	private RedisTemplate redisTemplate;
	@Test
	void testString() {
		//插入一条string类型数据
		redisTemplate.opsForValue().set("name", "李四");
		//读取一条string类型数据
		Object name = redisTemplate.opsForValue().get("name");
		System.out.println("name = " + name);
	}
}
```
## SpringDataRedis序列化方式
- RedisTemplate接收任意Object写入Redis，会把Object序列化为字节形式，默认采用jdk序列化
- 可读性差
- 内存占用大
### 自定义序列化方式
1. 自定义RedisTemplate
	- json序列化会将类的class类型写入json
2. 修改RedisTemplate序列化器为GenericJackson2JsonRedisSerializer
```java
@Bean
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
throws UnknownHostException {
	// 创建Template
	RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
	//设置连接工厂
	redisTemplate.setConnectionFactory(redisConnectionFactory);
	//设置序列化工具
	GenericJackson2JsonRedisSerializer jsonRedisSerializer =new GenericJackson2JsonRedisSerializer();
	// key和 hashkey采用 string序列化
	redisTemplate.setKeySerializer(RedisSerializer.string());
	redisTemplate.setHashKeySerializer(RedisSerializer.string());
	// vaLue和 hashValue采用 JsoN序列化
	redisTemplate.setValueSerializer(jsonRedisSerializer);
	redisTemplate.setHashValueSerializer(jsonRedisSerializer);
	return redisTemplate;
}
```
1. 使用StringRedisTemplate
	- String默认提供StringRedisTemplate类，序列化是string方式
2. 写入Redis时手动把对象序列化为json
3. 读取Redis时手动把读取到的json反序列化为对象
```java
@Autowired
private StringRedisTemplate stringRedisTemplate;
// JSON工具
private static final ObjectMapper mapper = new ObjectMapper();
@Test
void testStringTemplate() throws JsonProcessingException {
	//准备对象
	User user = new User("虎哥", 18);
	//手动序列化
	String jso = mapper.writeValueAsString(user);
	//写入一条数据到redis
	stringRedisTemplate.opsForValue().set("user:2oo", json) ;
	//读取数据
	String val = stringRedisTemplate.opsForValue().get("user:2oo");
	//反序列化
	User user1 = mapper.readValue(val, User.class);
	System.out.println("user1 = " + userl);
}
```