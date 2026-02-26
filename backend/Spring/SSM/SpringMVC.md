- 与Servlet等同，web层开发
- 基于java实现MVC模型的轻量级 Web框架
1. 导入SpringMVC坐标与Servlet坐标
2. 创建SpringMVC控制器类
3. 初始化SpringMVC环境，设定SpringMVC加载时对应的bean
4. 初始化Servlet容器，加载SpringMVC环境，设置SpringMVC处理的请求
- Controller
	- 类注解
	- SpringMVC控制器类定义上方
	- 设定SpringMVC核心控制器bean
- @RequestMapping
	 - 类型：方法注解
	- 位置：SpringMVc控制器方法定义上方
	- 作用：设置当前控制器方法请求访问路径
	- 相关属性
		- value（默认）：请求访问路径
- @ResponseBody
	- 类型：方法注解
	- 位置：SpringMVc控制器方法定义上方
	- 作用：设置当前控制器方法响应内容为当前返回值，无需解析
# 工作流程
- 启动服务器初始化过程
1. 服务器启动，执行ServlerContainerInitConfig类，初始化web容器
2. 执行createServletApplicationContext方法，创建WebApplicationContext对象
3. 加载SpringMvcConfig
4. 执行@ComponentScan加载对应的bean
5. 加载UserController，每个@RequestMapping名称对应一个具体的方法
6. 执行getServletMapping方法，定义所有的请求都通过SpringMVC
- 单次请求过程
1. 发送请求localhost/save
2. web容器发现所有请求都经过SpringMVC，请求交给SpringMVC处理
3. 解析请求路径/save
4. 由/save匹配路径执行对应的方法save()
5. 执行save()
6. 检测到有@ResponseBody直接将save()方法返回值作为响应求体返回给请求方
# Controller加载控制和bean加载控制
- SpringMVC相关bean（表现层bean）
	- 加载bean对应的包均在com.itheima.controller内
- Spring控制的bean
	- 业务bean（service）
	- 功能bean（DataSourse）
	- 1：加载的bean设置扫描范围为com.itheima，除掉controller包内的bean
	- 2：加载的bean设定扫描范围为精确范围，service、dao等
	- 3：不区分Spring和SpringMVC环境，加载到同一环境中
- @ComponentScan
	- 类注解
	- 属性
		- excludeFiters：排除扫描路径中加载的bean，需要指定类别type与具体项classes
		- includeFilters：加载指定的bean，需要指定类别type与具体项classes
```java
public class ServletcontainersInitconfig extends AbstractDispatcherServletInitializer {
	protected WebApplicationcontext createServletApplicationContext() {
		AnnotationConfigwebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
		ctx.register(SpringMvcconfig.class) ;
		return ctx;
	}
	protected WebApplicationContext createRootApplicationContext() {
		AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
		ctx.register(springconfig.class) ;
		return ctx;
	}
	protected String[] getServletMappings() {
		return new String[]{"/"};
	}
}
```
- 简化
```java
public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer{protected Class<?>[] getServletConfigClasses() {
	return new Class[]{
		springMvcConfig.class};
	}
	protected String[] getServletMappings() {
		return new String[]f"/"};
	}
	protected Class<?>[] getRootConfigClasses() {
		return new Class[]{springconfig.class};
	}
}
```
# Postman
- 网页调试与发送网页http请求的chrome插件
- 用于接口测试
- 注册登录
- 创建工作空间
- 发起测试
- vc中有这个插件
# 请求与响应
## 请求
### 请求映射路径
- @RequestMapping
- 方法注解类注解
- 位置：SpringMVC控制器方法定义上方
- 设置当前控制器方法请求访问路径，设置在类上统一设置当前控制器方法请求访问路径前缀
- 属性value：请求访问路径/前缀
### 请求方式
- GET
	- 普通参数：url地址传参，地址参数名与形参变量名相同，定义形参即可接收参数
	- 请求名不一样@RequestParam("请求名") 形参
	- 集合加@RequestParam
	- @RequestParam
		- 形参注解
		- springMVC控制器方法形参定义前面
		- 绑定请求参数与处理器方法形参间的关系
		- 参数
			- required：是否为必传参数
			- defaultValue：参数默认值
- POST
	- 普通参数：form表单post请求传参，表单参数名与形参变量名相同，定义形参即可接收参数
	- 乱码：添加过滤器设置编码
### 参数json
- 加json坐标
- @EnableWebMVC开启自动转换json数据支持
- 设置接受数据@RequestBody
- @RequestBody
	- 形参注解
	- springMVC控制器方法形参定义前
	- 将请求中请求体包含的数据传递给请求参数，一个处理器方法只能使用一次
- @RequestBody与@RequestParam区别
	- @RequestParam用于接收url地址传参，表单传参【application/x-www-form-urlencoded】@RequestBody用于接收json数据【application/json】
- 应用
	- 后期开发中，发送json格式数据为主，@RequestBody应用较广如果发送非json格式数据，选用@RequestParam接收请求参数
### 日期类型
- @DateTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")
- 名称：@DateTimeFormat
	- 类型：形参注解
	- 位置：SpringMVc控制器方法形参前面
	- 作用：设定日期时间型数据格式范例
	- 属性：pattern：日期时间格式字符串
- 类型转换器
	- Converter接口
## 响应
- 响应页面
- 响应数据--方法前加@ResponseBody
- 
	- 返回对应对象
	- 文本
	- json
- @ResponseBody
	- 方法注解
	- SpringMVC控制器方法定义上方
	- 设置当前控制器方法响应内容为当前返回值，无需解析
- 类型转换
	- HttpMessageConverter
# REST风格--开发风格
- 表现形式状态转换
- 隐藏资源访问行为
- 书写简化
## RESTful风格
1. 设定请求动作
	- @RequestMapping
	- 方法注解
	- SpringMVC控制器方法定义上方
	- 设置当前控制器方法的请求路径
	- 属性
		- value：请求路径
		- method：请求动作
			- GET：查询
			- POST：新增/保存
			- PUT：修改/更新
			- DELETE：删除
2. 设定请求参数路径
- RequestBody：接受json数据
- RequestParam：接受url地址传参或者表单传参
- PathVariable：接受路径参数，使用{参数名}描述路径参数
- 应用
	- 请求超过一个，json
	- 发送非json数据，RequestParam
	- restful开发，参数数量少，可用PathVariable接收
### RESTful快速开发
- RestController
- 类注解
- spingmvc的restful开发控制器类定义上
- 设置当前控制器类为restful风格，等同Controller+ResponseBody

- PostMapping GetMapping PutMapping DeleteMapping
- 方法注解
- spingmvc的restful开发控制器方法定义上
- 设置访问路径与请求方式
- value：请求路径
```java
@RequestMapping (value = "/books",method = RequestMethod.POST)
@ResponseBody
public String save(@RequestBody Book book)
{
	System.out.println("book save..." + book);
	return "{'module':'book save'}";
}
@RequestMapping value = "/books" ,method = RequestMethod.PUT)
@ResponseBody
public String update(@RequestBody Book book)
{
	System.out.println("book update..."+book);
	return "{'module':'book update'}";
}
```
```java
@RestController //Controller+ResponseBody
@RequestMapping("/books")
public class bookcontroller{
	//@RequestMapping (value = "/books",method = RequestMethod.POST)
	@PostMapping
	public String save(@RequestBody Book book)
	{
		System.out.println("book save..." + book);
		return "{'module':'book save'}";
	}
	//@RequestMapping value = "/books" ,method = RequestMethod.PUT)
	@PutMapping("/{id}")
	public String update(@RequestBody Book book)
	{
		System.out.println("book update..."+book);
		return "{'module':'book update'}";
	}
}
```
# SSM整合
1. 创建工程
2. SSM整合
	- Spring
		- SpringConfig
	- MyBatis
		- MybatisConfig
		- JdbcConfig
		- jdbc.properties
	- SpringMVC
		- ServletConfig
		- SpringMvcConfig
3. 功能模块
	- 表与实体类
	- dao（接口+自动代理）
	- service（接口+实现类）
		- 业务层接口测试（整合JUnit）
	- controller
		- 表现层接口测试（PostMan ）
## 表现层数据封装
- 前端接受数据格式
	- 创建结果模型类
	- 封装数据到data属性
	- 封装操作到code属性
	- 封装特殊消息到msg中
## 异常处理器
- 出现异常现象的常见位置与常见诱因如下：
	- 框架内部抛出的异常：因使用不合规导致
	- 数据层抛出的异常：因外部服务器故障导致（例如：服务器访问超时）
	- 业务层抛出的异常：因业务逻辑书写错误导致（遍历业务书写操作，导致索引异常）
	- 表现层抛出的异常：因数据收集、校验等规则导致（不匹配的数据类型间导致异常）
	- 工具类抛出的异常：因工具类书写不严谨不够健壮导致（必要释放的连接长期未释放）
- 异常均抛到表现层处理
```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
	@ExceptionHandler(Exception.class)
	public Result doException(Exception ex){
		return new Result(666,nu11);
	}
}
```
### 项目异常处理方案
#### 分类
- 业务异常（BusinessException)
	- 规范的用户行为产生的异常
	- 不规范的用户行为操作产生的异常
- 系统异常（SystemException)
	- 项目运行过程中可预计且无法避免的异常
- 其他异常（Exception）
	- 编程人员未预期到的异常
#### 处理
- 业务异常
	- 发送消息给用户提醒规范操作
- 系统异常
	- 发送固定信息给用户
	- 运维，提醒维护
	- 日志
- 其他
	- 发送固定信息给用户
	- 提醒维护
	- 日志

- 异常类继承运行时异常自动上抛
1. 定义项目系统级异常
2. 定义项目业务级异常
3. 定义异常编码
4. 触发自定义异常aop包装
5. 拦截并处理
## 拦截器
- 指定操作前后调用预设代码
- 阻止方法执行
- 归属不同：`Filter`属于`Servlet`技术，`Interceptor`属于`SpringMVC`技术
- 拦截内容不同：Filter对所有访问进行增强，`Interceptor`仅针对`SpringMVc`的访问进行增强
1. 声明拦截器`bean`，实现`HandlerInterceptor`
2. 定义配置类，继承`WebMvcConfigurationSupport`，实现`addInterceptor`方法（注意：扫描加载配置）
3. 添加拦截器并设定拦截的访问路径，路径可以通过可变参数设置多个
4. 使用标准接口`WebMvcConfigurer`简化开发
- 返回false终止操作
### 执行顺序
- perHandle
	- return true
		- controller
		- postHandle
		- afterCompletion
	- false
		- 结束
### 参数
- 前置处理
```java
public boolean preHandle(HttpServletRequest request,
		HttpServletResponse response,
		Object handler) throws Exeption {
	System.out.println("preHandle...");
	return true;
}
```
- 参数
	- request：请求对象
	- response：响应对象
	- handler：被调用的处理器对象，本质上是一个方法对象，对反射技术中Method对象再包装
- 返回值
	- false，被拦截的处理器不执行
- 完成后处理
```java
public void afterCompletion(HttpServletRequest request,
		HttpServletResponse response,
		Object handler,
		Exception ex) throws Exception {
	System.out.println("afterCompletion...");
}
```
- ex：处理器执行过程中出现异常对象，针对异常情况单独处理
### 多拦截器执行顺序
- 配置多个拦截器，形成拦截器链
- 先添加的先运行
- 若出现对原始处理器的拦截，后面的拦截器均终止运行
- 拦截器运行中断，仅运行前面的拦截器的`afterCompletion`操作