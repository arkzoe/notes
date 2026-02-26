
| 类/配置文件              | Spring | SpringBoot |
| ------------------- | ------ | ---------- |
| pom文件中的坐标           | 手动     | 勾选添加       |
| web3.0配置类           | 手动     | 无          |
| Spring/SpringMVC配置类 | 手动     | 无          |
| 控制器                 | 手动     | 手动         |
# 快速启动
1. Springboot打包（maven-package）
2. 执行启动命令
```bash
java -jar springboot.jar
```
- 起步依赖
- starter
	- 常见项目名称。定义当前项目使用的所有项目坐标，减少依赖配置
- parent
	- 继承的项目，定义坐标版本号（依赖管理），减少依赖冲突
- 开发
	- 仅修改GAV中的G和A，V由SoringBoot提供
	- 发生错误，再指定
- 辅助功能
- 启动方式：引导类
	- 创建项目采用jar打包方式
	- 引导类是项目的入口，运行main即可启动
- maven依赖管理变更起步依赖项
# 配置文件
- properties>yml>yaml
- yml
	- 大小写敏感
	- 数值前必须有空格作为分隔符
	- 缩进表示层级，空格
	- 相同层级左对齐
	- (#) 注释
	- 值0开头需''
	- 数组数据在数据书写位置下方使用减号作为数据开始符号，每行书写一个数据，减号与数据间空格分隔
- 数据读取
	- @Value读取单个数据，属性名引用方式：${一级属性名，二级属性名...}
	- 封装全部数据到Environment对象
	- 自定义对象封装
		- 自定义对象封装警告方案
		- spring-boot-configuration-processor
# 多环境
```yml
#设置启用的环境
spring:
	profiles:
		active: dev
---
#开发
spring:
	profiles: dev
server:
	port: 80
---
#生产
spring:
	profiles: pro
server:
	port: 81
---
#测试
spring:
	profiles: test
server:
	port: 82
```
```bash
# 命令行启动
java -jar springboot.jar --spring.profiles.active=dev
```
## 控制
1. maven中设置多环境
```xml
<profiles>
	<profile>
		<id>dev_env</id>
		<properties>
			<profile.active>dev</profile.active>
		</properties>
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
	</profile>
	<profile>
		<id>pro_env</id>
		<properties>
			<profile.active>pro</profile.active>
		</properties>
	</profile>
	<profile>
		<id>test_env</id>
		<properties>
			<profile.active>test</profile.active>
		</properties>
	</profile>
</profiles>
```
2. SpringBoot中引用Maven属性
```yml
#设置启用的环境
spring:
	profiles:
		active: ${profile.active}
---
#开发
spring:
	profiles: dev
server:
	port: 80
---
#生产
spring:
	profiles: pro
server:
	port: 81
---
#测试
spring:
	profiles: test
server:
	port: 82
```
3. 对资源文件开启默认占位符的解析
```xml
<build>
	<plugins>
		<plugin>
			<artifactId>maven-resources-plugin</artifactId>
			<configuration>
				<encoding>utf-8</encoding>
				<useDefaultDelimiters>true</useDefaultDelimiters>
			</configuration>
		</plugin>
	</plugins>
</build>
```
## 配置文件分类
1. file：config/application.yml
2. file：application.yml
3. classpath：config/application.yml
4. classpath：application.yml
- 1、2级留作系统打包后设置通用属性
- 3、4级用于系统开发阶段设置通用属性
## 整合
### Junit
- @SprngBootTest
- 测试类注解
- 位置：测试类定义上方
- 设置JUnit加载的SpringBoot启动类
- classes：设置SpringBoot启动类
### MyBatis
1. 创建时勾mybatis
2. 设置数据源参数
```yml
spring:
	datasource:
		type: com.alibaba.druid.pool.DruidDataSource
		driver-class-name: com.mysql.cj.jdbc.Driver
		url: jdbc:mysql://localhost:3306/ssm_db
		username: root
		password: root
```
# 事务管理
- @Transactional
- 运行时异常回滚
- 业务service的方法、类、接口上
```yml
#配置事务管理日志级别
logging：
  level：
    org. springframework. jdbc.support. JdbcTransactionManager： debug
```
- rollbackFor--属性
	- 控制出现何种异常类型，回滚
- propagation
	- 事务传播行为：一个事务方法被另一个事务方法调用时，这个事务方法如何进行事务控制

| 属性值           | 含义                                 |
| ------------- | ---------------------------------- |
| REQUIRED      | 【默认值】需要事务,有则加入,无则创建新事务             |
| REQUIRES_NEW  | 需要新事务,无论有无,总是创建新事务                 |
| SUPPORTS      | 支持事务,有则加入,无则在无事务状态中运行              |
| NOT_SUPPORTED | 不支持事务,在无事务状态下运行,如果当前存在已有事务,则挂起当前事务 |
| MANDATORY     | 必须有事务,否则抛异常                        |
| NEVER         | 必须没事务,否则抛异常                        |
| ...           |                                    |
# 原理
## 配置优先级
- 命令行参数-- >Java系统属性-D>properties>yml>yaml
## Bean管理
### 作用域
@Scope("作用域")

| 作用域           | 说明                            |
| ------------- | ----------------------------- |
| ==singleton== | 容器内同 名称 的 bean 只有一个实例(单例)(默认) |
| ==prototype== | 每次使用该 bean 时会创建新的实例(非单例/多例)   |
| request       | 每个请求范围内会创建新的实例(web环境中,了解)     |
| session       | 每个会话范围内会创建新的实例(web环境中,了解)     |
| application   | 每个应用范围内会创建新的实例(web环境中,了解)     |
|               |                               |
- @Lazy延迟初始化，延迟到第一次使用时再创建
- 单例的/无状态的bean：不保存数据的，启动初始化，线程安全
- 多例的/有状态的bean：保存数据
### 第三方Bean
- 无法用 aComponent 及衍生注解声明bean的,就需要用到 @Bean注解
	- 如果第三方bean需要依赖其它bean对象,直接在bean定义方法中设置形参即可,容器会根据类型自动装配
	- 通过@Bean注解的name或value属性可以声明bean的名称,如果不指定,默认bean的名称就是方法名
- 管理第三方bean对象，集中分类配置，通过@Configuration注解声明一个配置类
## springboot原理
### 起步依赖
- maven依赖传递
### 自动配置
1. @ComponentScan("扫描范围")x
2. @Import导入：加载到IOC容器中
	1. 导入 普通类
	2. 导入 配置类
	3. 导入 ImportSelector 接口实现类
	4. @Enable...封装@Import注解
#### 源码跟踪
- 该注解标识在SpringBoot工程引导类上,是SpringBoot中最最最重要的注解。该注解由三个部分组成：
1. @SpringBootConfiguration：该注解与 @Configuration 注解作用相同,用来声明当前也是一个配置类
2. @ComponentScan：组件扫描,默认扫描当前引导类所在包及其子包
3. @EnableAutoConfiguration：SpringBoot实现自动化配置的核心注解
##### @Conditional
- 作用：按照一定的条件进行判断,在满足给定条件后才会注册对应的bean对象到Spring IOC容器中
- 位置：方法、类
- @Conditional 本身是一个父注解,派生出大量的子注解：
	- @ConditionalOnClass：判断环境中是否有对应字节码文件,才注册bean到IOC容器
	- @ConditionalOnMissingBean：判断环境中没有对应的bean(类型 或 名称),才注册bean到IOC容器
	- @ConditionalOnProperty：判断配置文件中有对应属性和值,才注册bean到IOC容器
### 自定义starter
- 场景：在实际开发中,经常会定义一些公共组件,提供给各个项目团队使用。而在SpringBoot的项目中,一般会将这些公共组件封装为SpringBoot 的 starter(包含了起步依赖和自动配置的功能)
- starter后缀：依赖管理
- autoconfigure后缀：自动配置
# SpringBoot Web
## HTTP协议
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
- 控制反转：IOC，对象创建控制权由程序自身转移到外部
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
- 加入注解交由spring自行创建对象
## DI依赖注入
- 基于@Autowired进行依赖注入
1. 属性注入
	- 隐藏类之间的依赖关系、可能破坏类的封装性
2. 构造器注入
	- 繁琐，构造参数过多，臃肿
3. setter注入
	- 增加代码量
```java
//属性注入
@RestController
public class UserController{
	@Autowired //直接在类的成员变量上添加 `@Autowired`，Spring 会自动注入，无需手动new
	private UserService userService;
}
//构造器注入
@RestController
public class UserController{
	private final UserService userService;
	@Autowired //类只有一个构造函数，可省注解，适合对象不可变场景
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
- 注入规则
1. 按类型匹配，找到对应类型的bean注入
2. 有多个相同类型的bean，用`@Qualifier`指定
3. 非必须注入`@Autowired(required = false)`找不到对应的bean不报错