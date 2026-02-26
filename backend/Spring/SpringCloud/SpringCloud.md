- 将一个单体项目按功能拆分
- 注册中心拉取注册服务信息
- 配置中心拉取配置信息
![[Pasted image 20260223102631.png]]
# 服务架构
## 单体架构
业务所有功能集中在一个项目中开发，打成一个包部署
优：
- 架构简单
- 部署成本低
缺：
- 耦合度高
## 分步式架构
根据业务功能对系统进行拆分，每个业务模块作为独立项目开发，称为一个服务
优：
- 降低服务耦合
- 利于升级拓展
### 服务治理
- 服务拆分粒度
- 集群地址如何维护
- 服务之间如何实现远程调用
- 服务健康状态如何感知
## 微服务
一种分布式架构方案
- 单一职责：拆分力度更小，每一个服务都对应唯一的业务能力，做到单一职责，避免重复业务开发
- 面向服务：对外暴露业务接口
- 自治：团队独立、技术独立、数据独立、部署独立
- 隔离性强：服务调用做好隔离、容错、降级、避免出现级联问题
### 技术对比

|         | Dubbo           | SpringCloud             | SpringCloudAilbaba       |
| ------- | --------------- | ----------------------- | ------------------------ |
| 注册中心    | zookeeper、Redis | Eureka、Consul           | Nacos、Eureka             |
| 服务远程调用  | Dubbo协议         | Feign (http协议)          | Dubbo、Feign              |
| 配置中心    | 无               | SpringCloudConfig       | SpringCloudConfig、 Nacos |
| 服务网关    | 无               | SpringCloudGateway、Zuul | SpringCloudGateway、 Zuul |
| 服务监控和保护 | dubbo-admin，功能弱 | Hystrix                 | Sentinel                 |
1. SpringCloud + Feign
	- 使用SpringCloud技术栈
	- 服务接口采用Restful风格
	- 服务调用采用Feign方式
2. SpringCloudAlibaba + Feign
	- 使用SpringCloudAlibaba技术栈
	- 服务接口采用Restful风格
	- 服务调用采用Feign方式
3. SpringCloudAlibaba+Dubbo
	- 使用SpringCloudAlibaba技术栈
	- 服务接口采用Dubbo协议标准
	- 服务调用采用Dubbo方式
4. Dubbo原始模式
	- 基于Dubbo老l旧技术体系
	- 服务接口采用Dubbo协议标准
	- 服务调用采用Dubbo方式
# 服务拆分
1. 不同微服务，不要重复开发相同业务
2. 微服务数据独立，不要访问其他微服务的数据库
3. 将自己业务暴露为接口，供其他微服务调用
## 服务远程调用
基于RestTemplate发起http请求实现远程调用
# Eureka
服务提供者：一次业务中，被其它微服务调用的服务（提供接口给其它微服务）
服务消费者：一次业务中，调用其它微服务的服务（调用其它微服务提供的接口）
## 原理分析
![[Pasted image 20260223135048.png]]
- 消费者该如何获取服务提供者具体信息？
	- 服务提供者启动时向eureka注册自己的信息
	- eureka保存这些信息
	- 消费者根据服务名称向eureka拉取提供者信息
- 如果有多个服务提供者，消费者该如何选择？
	- 服务消费者利用负载均衡算法，从服务列表中挑选一个
- 消费者如何感知服务提供者健康状态？
	- 服务提供者会每隔30秒向EurekaServer发送心跳请求，报告健康状态
	- eureka会更新记录服务列表信息，心跳不正常会被剔除
	- 消费者就可以拉取到最新的信息
## 搭建注册中心
1. 创建项目，引入spring-cloud-starter-netflix-eureka-server的依赖
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```
2. 编写启动类，添加@EnableEurekaServer注解
3. 添加application.yml文件，编写下面的配置:
```yaml
server:
	port: 10086
spring:
	application:
		name: eurekaserver
eureka:
	client:
		service-url:
			defaultZone: http://127.0.0.1:10086/eureka/
```
## 服务注册
将user-service服务注册到EurekaServer步骤如下:
1. 在user-service项目引l入spring-cloud-starter-netflix-eureka-client的依赖
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
2. 在application.yml文件，编写下面的配置:
```yaml
spring:
	application:
		name: userservice
eureka:
	client:
		service-url:
			defaultZone: http://127.0.0.1:10086/eureka/
```
## 服务发现
服务拉取是基于服务名称获取服务列表，然后在对服务列表做负载均衡
1. 修改OrderService的代码，修改访问的url路径，用服务名代替ip、端口：
```java
String url = "http://userservice/user/" + order.getUserId();
```
2. 在order-service项目的启动类OrderApplication中的RestTemplate添加负载均衡注解：
```java
@Bean
@LoadBaLanced
public RestTemplate restTemplate() {
	return new RestTemplate();
}
```
# Ribbon均衡负载
![[Pasted image 20260223152616.png]]

| 内置负载均衡规则类                 | 规则描述                                                                                                                                                                                                                                                            |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则                                                                                                                                                                                                                                |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：<br>（1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加<br>（2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的<br>< clientName>,< clientConfigNameSpace>.ActiveConnectionsLimit属性进行配置 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择                                                                                                                                                                                                |
| `ZoneAvoidanceRule`       | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询                                                                                                                                                                                     |
| BestAvailableRule         | 忽略哪些短路的服务器，并选择并发数较低的服务器                                                                                                                                                                                                                                         |
| RandomRule                | 随机选择一个可用的服务器                                                                                                                                                                                                                                                    |
| RetryRule                 | 重试机制的选择逻辑                                                                                                                                                                                                                                                       |
通过定义IRule实现可以修改负载均衡规则，有两种方式：
1. 代码方式：在order-service中的OrderApplication类中，定义一个新的IRule
```java
@Bean
public IRule randomRule(){
	return new RandomRuLe();
}
```
2. 配置文件方式：在order-service的application.yml文件中，添加新的配置也可以修改规则:
```yaml
userservice:
	ribbon:
		NFLoadBalancerRuLeCLassName: com.netfLix.LoadbaLancer.RandomRuLe 
		# 负载均衡规则
```
## 饥饿加载
Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：
```yaml
ribbon:
	eager-load:
		enabled：true # 开启饥饿加载
		clients：userservice # 指定对userservice这个服务饥饿加载
```
# Nacos
1. 在cloud-demo父工程中添加spring-cloud-alilbaba的管理依赖:
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-alibaba-dependencies</artifactId>
	<version>2.2.5.RELEASE</version>
	<type>pom</type>
	<scope>import</scope>
</dependency>
```
2. 注释掉order-service和user-service中原有的eureka依赖。
3. 添加nacos的客户端依赖：
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```
4. 修改user-service&order-service中的application.yml文件，注释eureka地址，添加nacos地址：
```yaml
spring:
	cloud:
		nacos:
			server-addr: Localhost: 8848 # nacos 服务端地址
```
5. 启动并测试
## 服务分级存储模型
服务调用尽可能选择本地集群的服务，跨集群调用延迟较高
本地集群不可访问时，再去访问其它集群
1. 修改application.yml，添加如下内容：
```yaml
spring:
	cloud:
		nacos:
			server-addr: Localhost:8848 # nacos 服务端地址
			discovery:
				cLuster-name: HZ # 配置集群名称，也就是机房位置，例如：HZ，杭州
```
2. 在Nacos控制台可以看到集群变化
## NacosRule负载均衡
优先选择同集群
1. 修改order-service中的application.yml，设置集群为HZ:
```yaml
spring:
	cloud:
		nacos:
			server-addr: locaLhost:8848 # nacos 服务端地址
			discovery:
				cLuster-name: HZ # 配置集群名称，也就是机房位置
```
2. 然后在order-service中设置负载均衡的iRule为NacosRule，这个规则优先会寻找与自己同集群的服务：
```yml
userservice:
	ribbon:
		NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRuLe # 负载均衡规则
```
3. 注意将user-service的权重都设置为1
## 服务实例权重设置
Nacos控制台可以设置实例的权重值，0~1之间
同集群内的多个实例，权重越高被访问的频率越高
权重设置为0则完全不会被访问
## 环境隔离
  Nacos中服务存储和数据存储的最外层都是一个名为namespace的东西，用来做最外层隔离
1. 在Nacos控制台可以创建namespace，用来隔离不同环境
2. 然后填写一个新的命名空间信息
3. 保存后会在控制台看到这个命名空间的id
4. 修改order-service的application.yml， 添加namespace
## 与Eureka对比
![[Pasted image 20260223171056.png]]
服务注册到Nacos时，可以选择注册为临时或非临时实例，通过下面的配置来设置：
```yaml
spring:
	cloud:
		nacos:
			discovery:
				ephemeral: false # 设置为非临时实例
```
1. Nacos与eureka的共同点
	- 都支持服务注册和服务拉取
	- 都支持服务提供者心跳方式做健康检测
2. Nacos与Eureka的区别
	- Nacos支持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式
	- 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
	- Nacos支持服务列表变更的消息推送模式，服务列表更新更及时
	- Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用cP模式；Eureka采用AP方式
## 配置管理
DataID:业务名称-环境.yaml
有热更新需求的
### 配置拉取
1. 引入Nacos的配置管理客户端依赖：
```xml
<!--nacos配置管理依赖-->
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```
2. 在userservice中的resource目录添加一个`bootstrap.yml`文件，这个文件是引导文件，优先级高于application.yml:
```yaml
spring:
	application:
		name: userservice # 服务名称
	profiles:
		active: dev #开发环境，这里是dev
	cloud:
		nacos:
		server-addr: localhost:8848 # Nacos地址
		config:
			file-extension: yaml # 文件后缀名
```
3. 通过value注解读取
### 配置热更新
Nacos中的配置文件变更后，微服务无需重启就可以感知。不过需要通过下面两种配置实现：
1. 在@Value注入的变量所在类上添加注解`@RefreshScope`
```java
@sLf4j
@RestController
@RequestMapping("/user")@RefreshScope
public class UserController {
	@Value（"${pattern.dateformat}")
	private String dateformat;
}
```
2.使用`@ConfigurationProperties`注解
```java
@Component
@Data
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
	private String dateformat;
}
```
### 多环境配置共享
微服务启动时会从nacos读取多个配置文件：
- `[spring.application.name]-[spring.profiles.active].yaml`, 例如: userservice-dev.yaml
- `[spring.application.name].yaml`, 例: userservice.yaml
无论profile如何变化，`[spring.application.name].yaml`这个文件一定会加载，因此多环境共享配置可以写入这个文件
测试/生产环境    >  共享   > 本地
服务名-profile.yaml > 服务名称.yaml > 本地配置
### 集群搭建
搭建集群的基本步骤：
- 搭建数据库，初始化数据库表结构
- 下载nacos安装包
- 配置nacos：集群配置、数据库配置
- 启动nacos集群
- nginx反向代理
# Fegin
声明式http客户端
使用Feign的步骤如下：
1. 引入依赖：
```xml
<dependency>
	<groupId>org. springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-penfeign</artifactId>
</dependency>
```
2. 在order-service的启动类添加注解开启Feign的功能：
```java
@EnableFeignClients
@MapperScan("cn.itcast.order.mapper")
@SpringBootApplication
public class OrderApplication
	public static void main(String[] args) {
		SpringApplication.run(OrderApplication.class, args);
	}
```
3. 编写Feign客户端：
```java
@Feignclient("userservice")
public interface UserClient {
	@GetMapping("/user/{id}")
	User findById(@PathVariable("id") Long id);
}
```
主要是基于SpringMVC的注解来声明远程调用的信息，比如：
- 服务名称：userservice
- 请求方式：GET
- 请求路径： /user/{id}
- 请求参数：Long id
- 返回值类型：User
4. 替代RedisTemplate
```java
@Autowired
private UserClient userclient;
public Order queryOrderById(Long orderId) {
	//1.查询订单
	Order order = orderMapper.findById(orderId);
	//2.利用Feign发起http请求，查询用户
	User user = userClient.findById(order.getUserId());
	//3.封装user到0rder
	order.setUser(user);
	//4.返回
	return order;
}
```
## 自定义配置
Feign运行自定义配置来覆盖默认配置，可以修改的配置如下：

| 类型                  | 作用       | 说明                                |
| ------------------- | -------- | --------------------------------- |
| feign.Logger.Leve   | 修改日志级别   | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL |
| feign.codec.Decoder | 响应结果的解析器 | http远程调用的结果做解析，例如解析json字符串为java对象 |
| feign.codec.Encoder | 请求参数编码   | 将请求参数编码，便于通过http请求发送              |
| feign. Contract     | 支持的注解格式  | 默认是SpringMVC的注解                   |
| feign. Retryer      | 失败重试机制   | 请求失败的重试机制，默认是没有，不过会使用Ribbon的重试    |

配置Feign日志有两种方式：
1. 配置文件方式
- 全局生效：
```yaml
feign:
	client:
		config:
			default:  #这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
				LoggerLevel: FULL # 日志级别
```
- 局部生效：
```yaml
feign:
	client:
		config:
			userservice:
			# 这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
				LoggerLeveL: FULL # 日志级别
```
2. java代码方式，需要先声明一个Bean:
```java
public class FeignClientConfiguration {
	@Bean
	public Logger.Level feignLogLevel(){
		return Logger.Level.BASIC;
	}
}
```
- 而后如果是全局配置，则把它放到@EnableFeignClients这个注解中：
```java
@EnableFeignClients(defaultConfiguration = FeignClientConfiguration.class)
```
- 如果是局部配置，则把它放到@FeignClient这个注解中：
```java
@FeignClient(value = "userservice", configuration = FeignClientConfiguration.class)
```
## 性能优化
Feign底层的客户端实现：
- URLConnection：默认实现，不支持连接池
- Apache HttpClient：支持连接池
- OKHttp：支持连接池
因此优化Feign的性能主要包括：
- 使用连接池代替默认的uRLConnection
- 日志级别，最好用basic或none
1. Feign添加HttpClient的支持:引入依赖：
```xml
<!--httpclient的依赖 -->
<dependency>
	<groupId>io.github.openfeign</groupId>
	<artifactId>feign-httpclient</artifactId>
</dependency>
```
配置连接池：
```yaml
feign:
	client:
		config:
			default: # default全局的配置
				LoggerLevel: BASIC # 日志级别，BASIC就是基本的请求和响应信息
	httpclient:
		enabled: true # 开启feign对HttpClient的支持
		max-connections: 200 # 最大的连接数
		max-connections-per-route: 50 # 每个路径的最大连接数
```
## 最佳实践
1. 继承：给消费者的FeignClient和提供者的controller定义统一的父接口作为标准
	- 服务紧耦合
	- 父接口参数列表中的映射不会被继承
```java
public interface UserAPI{
	@GetMapping("/user/{id}")
	User findByid(@PathVariable("id") Long id);
}
```
2. 抽取：将FeignClient抽取为独立模块，并且把接口有关的POjO、默认的Feign配置都放到这个模块中，提供给所有消费者使用
- 实现步骤
	1. 首先创建一个module，命名为feign-api，然后引入feign的starter依赖
	2. 将order-service中编写的UserClient、User、DefaultFeignConfiguration都复制到feign-api项目中
	3. 在order-service中引l入feign-api的依赖
	4. 修改order-service中的所有与上述三个组件有关的import部分，改成导入feign-api中的包
	5. 重启测试
当定义的FeignClient不在SpringBootApplication的扫描包范围时，这些FeignClient无法使用。有两种方式解决：
1. 指定FeignClient所在包
```java
@EnableFeignClients(basePackages = "cn.itcast.feign.clients")
```
2. 指定FeignClient字节码
```java
@EnableFeignClients(clients = {UserClient.class})
```
# Gateway网关
功能：
- 身份认证和权限校验
- 服务路由、均衡负载
- 请求限流
Zuul是基于Servlet的实现，属于阻塞式编程。而SpringCloudGateway则是基于Spring5中提供的WebFlux，属于响应式编程的实现，具备更好的性能

配置内容：
- 路由id：路由唯一标示
- uri：路由目的地，支持lb和http两种
- predicates：路由断言，判断请求是否符合要求，符合则转发到路由目的地
- filters：路由过滤器，处理请求或响应
## 搭建
1. 创建新的module，引I入SpringCloudGateway的依赖和nacos的服务发现依赖:
```xml
<!--网关依赖-->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!--nacos服务发现依赖-->
<dependency>
	<groupId>com.alibaba. cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```
2. 编写路由配置及nacos地址
```yaml
server:
	port: 10010 # 网关端口
spring:
	application:
		name: gateway # 服务名称
	cloud:
		nacos:
			server-addr: localhost:8848 # nacos地址
	getway:
		routes: # 网关路由配置
		- id: user-service # 路由id，自定义，只要唯一即可
			# uri：http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
			uri: lb://userservice # 路由的目标地址 Lb就是负载均衡，后面跟服务名称
			predicates: # 路由断言，判断请求是否符合路由规则的条件
			- Path=/user/** # 这个式按照路径匹配，以/user/揩油就符合要求
```
![[Pasted image 20260224112710.png]]
## 路由断言工厂
我们在配置文件中写的断言规则只是字符串，这些字符串会被PredicateFactory读取并处理，转变为路由判断的条件
例如Path=/user/**是按照路径匹配，这个规则是由
org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory类来处理的像这样的断言工厂在SpringCloudGateway还有十几个

| 名称       |              说明              |
| ---------- |:------------------------------:|
| After      |      是某个时间点后的请求      |
| Before     |     是某个时间点之前的请求     |
| Between    |    是某两个时间点之前的请求    |
| Cookie     |     请求必须包含某些cookie     |
| Header     |     请求必须包含某些header     |
| Host       | 请求必须是访问某个host（域名） |
| Method     |     请求方式必须是指定方式     |
| Path       |    请求路径必须符合指定规则    |
| Query      |    请求参数必须包含指定参数    |
| RemoteAddr |    请求者的ip必须是指定范围    |
| Weight     |            权重处理            |
## 路由过滤器
GatewayFilter是网关中提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理：
有三十多种过滤器工厂
## 默认过滤器
如果要对所有的路由都生效，则可以将过滤器工厂写到default下。格式如下：
```yaml
spring:
	application:
		name: gateway # 服务名称
	cloud:
		nacos:
			server-addr: localhost:8848 # nacos地址
		gateway:
			routes: # 网关路由配置 
			id: user-service
			uri: lb://userservice
			predicates:
				- Path=/user/** 
			-id: order-service
			uri: Lb://orderservice
			predicates:
				Path=/order/**
		default-filters: # 默认过滤器，会对所有的路由请求都生效
			AddRequestHeader=Truth, Itcast is freaking awesome! # 添加请求头
```
## 全局过滤器
全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与GatewayFilter的作用一样
区别在于GatewayFilter通过配置定义，处理逻辑是固定的。而GlobalFilter的逻辑需要自己写代码实现。定义方式是实现GlobalFilter接口
```java
public interface GlobalFilter {
	/**
	* 处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
	*
	* @param exchange 请求上下文，里面可以获取Request、Response等信息
	* @param chain 用来把请求委托给下一个过滤器
	* @return {@code Mono<Void>} 返回标示当前过滤器业务结束*/
	Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```
1. 实现GlobalFilter接口
2. 添加@Order注解或实现Ordered接口
3. 编写处理逻辑
## 过滤器执行顺序
请求进入网关会碰到三类过滤器：当前路由的过滤器、DefaultFilter、GlobalFilter
请求路由后，会将当前路由过滤器和DefaultFilter、GlobalFilter，合并到一个过滤器链（集合）中，排序后依次执行每个过滤器
1. 每一个过滤器都必须指定一个int类型的order值，order值越小，优先级越高，执行顺序越靠前
2. GlobalFilter通过实现Ordered接口，或者添加@Order注解来指定order值，由我们自己指定
3. 路由过滤器和defaultFilter的order由Spring指定，默认是按照声明顺序从1递增
4. 当过滤器的order值一样时，会按照defaultFilter >路由过滤器>GlobalFilter的顺序执行
- order值越小，优先级越高
- 当order值一样时，顺序是defaultFilter最先，然后是局部的路由过滤器，最后是全局过滤器
## 跨域问题
域名不一致
- 域名不同
- 域名相同，端口不同
跨域问题：浏览器禁止请求的发起者与服务端发生跨域ajax请求，请求被浏览器拦截的问题

网关处理跨域采用的同样是CORS方案，并且只需要简单配置即可实现：
```yaml
spring:
	cloud:
		gateway:
		#...
		gLobaLcors: # 全局的跨域处理
			add-to-simple-urL-handler-mapping: true # 解决options请求被拦截问题
			corsConfigurations:
			'[/**]':
			allowed0rigins: # 允许哪些网站的跨域请求
			-"http://locaLhost:8090"
			-"http://www.leyou.com"
			allowedMethods: # 允许的跨域ajax的请求方式
			-"GET"
			-"POST"
			-"DELETE"
			-"PUT"
			-"OPTIONS"
			aLLowedHeaders: "*"  # 允许在请求中携带的头信息
			allowCredentials: true # 是否允许携带
			cookiemaxAge: 360000 # 这次跨域检测的有效期
```