# SpringBoot Web
# HTTP协议
- 基于TCP协议：面向连接，安全
- 基于请求-响应模型：一次请求对应一次响应
- 无状态协议：每次请求响应独立
	- 缺点：请求不能共享数据
	- 快
## 请求
- 请求行：第一行（请求方式、资源路径、协议）
- 请求头：第二行开始K:V
- 请求体：POST请求，存放请求参数
- GET：参数在请求行中，没有请求体，大小有限制
- POST：参数在请求体中，大小没限制
### 数据获取
- Web服务器对请求数据进行解析，封装
- @RestController
- HttpServletRequest
## 响应
- 响应行：第一行（协议、状态码、描述）
- 响应头：第二行开始K:V
- 响应体：POST请求，存放请求参数

| 状态码 |           |
| --- | --------- |
| 1xx | 响应中，临时状态码 |
| 2xx | 成功        |
| 3xx | 重定向       |
| 4xx | 客户端错误     |
| 5xx | 服务端错误     |
### 数据设置
- HttpServletResponse
- ResponseEntity String
# 分层解耦
## 三层架构
- controller：控制层，接受前端发送的请求，对请求进行处理，响应数据
- service：业务逻辑，处理具体的业务逻辑
- dao：数据访问层，增删改查
---
- 控制反转：IOC，对象创建控制权由程序自身转移到外部 ^c0c023
- 依赖注入：DI，容器为应用提供运行时，所依赖的资源
- Bean对象：IOC容器中创建、管理的对象
## IOC控制反转
- 将对象交给IOC容器管理需加对应注解

| 注解          | 说明          | 位置                   |
| ----------- | ----------- | -------------------- |
| @Component  | 声明bean的基础注解 | 不属于以下三类使用            |
| @Controller | 衍生          | 控制层类                 |
| @Service    | 衍生          | 业务层类                 |
| @Repository | 衍生          | 数据访问层类（mabatis整合，少用） |
- 需被@ComponentScan扫描生效
- 没有显式配置，包含在启动类声明注解@SpringBootApplication，默认范围是启动类所在包及其子包
## DI依赖注入
- 基于@Autowired进行依赖注入
1. 属性注入
	1. 隐藏类之间的依赖关系、可能破坏类的封装性
2. 构造器注入
	1. 繁琐，构造参数过多，臃肿
3. setter注入
	1. 增加代码量
```java
//属性注入
@RestController
public class UserController{
	@Autowired
	private UserService userService;
}
//构造器注入
@RestController
public class UserController{
	private final UserService userService;
	@Autowired//只有一个构造函数，可省
	public UserController(UserService userService){
		this.userService = userService;
	}
}
//setter注入
@RestController
public class UserController{
	private UserService userService;
	@Autowired//只有一个构造函数，可省
	public void setUserController(UserService userService){
		this.userService = userService;
	}
}
```
- @Autowired根据==类型==注入
- 存在多个类型相同的bean报错
- @Primary类上提高优先级
- @Qualifier(类名)
- @Resourse(name = "类名")