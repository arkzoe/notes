- IOC控制反转，程序不主动new对象，转换为IOC容器提供
- [[Web基础#^784046]]
- 被创建管理的对象在IOC容器中称为==Bean==
- DI，在容器中建立bean与bean之间的依赖关系的过程
## bean基础配置
- 交给容器进行管理的bean
	- 表现层对象
	- 业务层对象
	- 数据层对象
	- 工具对象
- 不适合
	- 封装实体的对象

| 类别   | 描述                                                                      |
| ---- | ----------------------------------------------------------------------- |
| 名称   | bean                                                                    |
| 所属   | 标签                                                                      |
| 功能   | 定义Spring核心容器管理的对象                                                       |
| 格式   | bean<br>    bean/<br>	bean /bean<br>beans                               |
| 属性列表 | id：bean的id，使容器通过id值获取对应的bean，在一个容器中id值唯一<br>class：bean的类型，即配置bean的全路径类名 |
### scope属性
1. Singleton（单例） - **默认作用域**
	- **含义**：在整个 Spring IoC 容器中，**只存在一个**该 Bean 的实例
	- **生命周期**：容器启动时创建（如果设置为懒加载则在第一次请求时创建），容器关闭时销毁
	- **共享性**：所有对该 Bean 的请求和引用，都会返回**同一个共享的实例**
	- **使用场景**：**无状态的**服务类，如 Service、DAO、工具类等。这是最常用、最高效的作用域
2. Prototype（原型）
	- **含义**：每次从容器中请求该 Bean 时，都会**创建一个新的实例**
	- **生命周期**：实例化后由 Spring 容器返回给请求方，之后容器就不再管理其生命周期。其销毁需要由客户端代码或垃圾回收机制负责
	- **共享性**：**不共享**。每次都是一个新的对象
	- **使用场景**：**有状态的**对象，比如每个 HTTP 请求都需要自己独立数据的对象
### 实例化bean的三种方式
- 构造方法实例化bean
```java
public class BookDaoImpl implements BookDao{
	public BookDaoImpl(){}
	public void save(){
	}
}
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
```
- 静态工厂实例化
```java
public class OrderDaoFactory{
	public static OrderDao getOrderDao(){
		return new OrderDaoImpl();
	}
}
<bean
	id="orderDao"
	factory-method="getOrderDao"
	class="com.itheima.factoty.OrderDaoFactory"/>
```
- 实例工厂实例化
```java
public class UserDaoFactory{
	public UserDao getUserDao(){
		return new UserDaoImpl();
	}
}
<bean id="userDaoFactory" class="com.itheima.factory.UserDaoFactory"/>
//配合使用
<bean
	id="userDao"
	factory-method="getUserDao"//方法名不固定，每次使用需配置
	factory-bean="userDaoFactory"
/>
```
### 生命周期
- 配置init-method
- 注册关闭钩子，虚拟机退出前先关闭容器再退出虚拟机
	- registerShutdownHook()
- 手工关闭
	- close
- 使用接口initializationbean,disposablebean
- 初始化容器
	1. 创建对象（内存分配）
	2. 执行构造方法
	3. 执行属性注入（set操作）
	4. 执行bean初始化方法
- 使用bean
	1. 执行业务操作
- 关闭/销毁容器
	1. 执行bean销毁方法
## 依赖注入
### setter
- 引用类型
```java
//提供可访问的set方法
public class BookServiceImpl implements BookService{
	private BookDao bookDao;
	public void setBookDao(BookDao bookDao) {
		this.bookDao = bookDao;
	}
}
//使用property标签ref属性注入引用类型对象
<bean id="bookService" class="com. itheima. service. impl. BookServiceImpl">
	<property name="bookDao" ref="bookDao"/>
</bean>
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
```
- 简单类型
```java
//提供可访问的set方法
public class BookDaoImpl implements BookDao {
	private int connectionNumber;
	public void setConnectionNumber(int connectionNumber) {
		this. connectionNumber = connectionNumber;
	}
}
//使用property标签value属性注入引用类型对象
<bean id="bookDao" class="com. itheima.dao. impl. BookDaoImpl">
	<property name="connectionNumber" value="10"/>
</bean>
```
### 构造
- 引用
```java
public class BookServiceImpl implements BookService{
	private BookDao bookDao;
	public BookServiceImpl(BookDao bookDao) {
		this.bookDao = bookDao;
	}
}
//配置中使用constructor-arg标签ref属性注入引用类型对象
<bean id="bookService" class="com. itheima.service. impl. BookServiceImpl">
	<constructor-arg name="bookDao" ref="bookDao"/>
</bean>
<bean id="bookDao" class="com. itheima.dao.impl.BookDaoImpl"/>
```
- 简单
```java
//在bean中定义引用类型属性并提供可访问的set方法
public class BookDaoImpl implements BookDao {
	private int connectionNumber;
	public void setConnectionNumber(int connectionNumber) {
		this. connectionNumber = connectionNumber;
	}
}
//配置中使用constructor-arg标签value属性注入简单类型数据
<bean id="bookDao" class="com. itheima.dao. impl. BookDaoImpl">
	<constructor-arg name="connectionNumber" value="10"/>
</bean>
```
### 选择
1. 强制依赖使用构造器进行,使用setter注入有概率不进行注入导致null对象出现
2. 可选依赖使用setter注入进行,灵活性强
3. Spring框架倡导使用构造器,第三方框架内部大多数采用构造器注入的形式进行数据初始化,相对严谨
4. 如果有必要可以两者同时使用,使用构造器注入完成强制依赖的注入,使用setter注入完成可选依赖的注入
5. 实际开发过程中还要根据实际情况分析,如果受控对象没有提供setter方法就必须使用构造器注入
6. 自己开发的模块推荐使用setter注入
## 依赖自动装配
- Ioc容器根据bean依赖的资源在容器中自动查找并注入到bean的过程
- 方式
	- 类型
	- 名称
	- 构造方法
	- 不启用
- autowire属性设置自动装配的类型
	- 用于引用类型依赖注入，不能对简单类型操作
	- 使用按类型装配时(byType)必须保障容器中相同类型的bean唯一,推荐使用
	- 使用按名称装配时(byName)必须保障容器中具有指定名称的bean,因变量名与配置耦合,不推荐使用
	- 自动装配优先级低于setter注入与构造器注入,同时出现时自动装配配置失效
## 集合注入
- 数组
- list
- set
- map
```java
<property name="array">
	<array>
		<value></value>
	</array>
</property>
<property name="map">
	<map>
		<entry key="" value="">
	</map>
</property>
<property name="properties">
	<props>
		<prop key=""></prop>
	</props>
</property>
```
## 加载properties文件
1. 开启context命名空间
2. 使用context命名空间加载properties文件
```java
<context:property-placeholder location="">
```
3. 使用属性占位符${}读取properties文件中的属性
```java
● 不加载系统属性
<context : property-placeholder location="jdbc. properties" system-properties-mode="NEVER"/>
● 加载多个properties文件
<context : property-placeholder location="jdbc.properties, msg.properties"/>
● 加载所有properties文件
<context : property-placeholder location=" *. properties"/>
● 加载properties文件标准格式
<context : property-placeholder location="classpath :*. properties"/>
● 从类路径或jar包中搜索并加载properties文件
<context : property-placeholder location="classpath *:*. properties"/>
```
## 容器
- 加载配置文件
1. 类路径加载配置文件
```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
```
2. 文件路径加载配置文件
```java
ApplicationContext ctx = new FileSystemXmlApplicationContext("D:\applicationContext.xml");
```
3. 加载多个配置文件
```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("bean1.xml", "bean2.xml");
```
- 获取bean
1. 使用bean名称获取
```java
BookDao bookDao = (BookDao) ctx.getBean("bookDao");
```
2. 使用bean名称获取并指定类型
```java
BookDao bookDao = ctx.getBean("bookDao", BookDao.class);
```
3. 使用bean类型获取
```java
ctx.getBean(BookDao.class);
```
- beanfacrtory是Ioc容器的顶层接口，创建完毕后，所有的bean均为延迟加载
- ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载
- ApplicationContext提供基础bean操作相关方法，通过其他接口拓展功能
- 常用的初始化类
	- ClassPathXmlApplicationContext
	- FileSystemXmlApplicationContext
# 注解开发
## 定义bean
- @Component定义bean
- 配置文件组件扫描加载bean
- @Component衍生
	- Controller：表现层bean定义
	- Service：业务层
	- Repository：数据层
## 纯注解开发
- 用java类替代配置文件
- @Configuration设定当前类为配置文件
- @ComponentScan("包")：多个用数组格式
- 读配置文件改成读配置类
### bean作用范围和生命周期
```java
//SpringConfig
@Repository
@Scope("singleton")
public class BookDaoImpl implements BookDao {
	public void save() {
		System.out.println("book dao save ... ");
	}
	@PostConstruct
	public void init() {
		System.out.println("init ... ");
	}
	@PreDestroy
	public void destroy() {
		System.out.println("destroy ... ");
	}
}
```
### 自动装配
- 基于反射设计创建对象并暴力反射对应属性为私有属性初始化数据，无需setter
- 自动装配建议使用无参构造方法创建对象（默认），不提供对应构造方法，就提供唯一构造方法
- @Autowired：按类型装配
- @Qualifier()：指定注入，无法单独使用，需配合Autowired
- @Value：进行简单类型注入，配置文件写属性
- @PropertySource加载配置文件，不能用通配符
### 第三方bean
1. 直接写
```java
@Configuration
public class SpringConfig {
	//1.定义一个方法获得要管理的对象
	//2.添加@Bean,表示当前方法的返回值是一个bean
	@Bean
	public DataSource dataSource(){
		DruidDataSource ds = new DruidDataSource();
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUrl("jdbc:mysql://localhost: 3306/spring_db"
		ds.setUsername("root"); I
		ds.setPassword("root");
		return ds;
	}
}
```
2. 导入式
```java
//JDBCConfig.java
public class JDBCConfig{
	//1.定义一个方法获得要管理的对象
	//2.添加@Bean,表示当前方法的返回值是一个bean
	@Bean
	public DataSource dataSource(){
		DruidDataSource ds = new DruidDataSource();
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUrl("jdbc:mysql://localhost: 3306/spring_db"
		ds.setUsername("root"); I
		ds.setPassword("root");
		return ds;
	}
}

//SpringConfig
@Configuration
@Import(JDBCConfig.class)
public class SpringConfig {
}
```
3. 扫描式
#### 第三方bean注入
- 简单类型@Value
- 引用类型为bean定义方法设置形参，容器根据类型自动装配

| 功能        | XML配置                                           | 注解                                                                           |
| --------- | ----------------------------------------------- | ---------------------------------------------------------------------------- |
| 定义bean    | bean标签<br>--id属性<br>--class属性                   | @Component<br>--@Controller<br>--@Service<br>--@Repository<br>@ComponentScan |
| 设置依赖注入    | setter注入<br>--引用/简单<br>构造器注入<br>--引用/简单<br>自动装配 | @Autowired<br>--@Qualifier<br>@Value                                         |
| 配置第三方bean | bean标签<br>静态工厂、实例工厂、FactoryBean                 | @Bean                                                                        |
| 作用范围      | scope属性                                         | @Scope                                                                       |
| 生命周期      | 标准接口<br>--init-method<br>--destroy-method       | @PostConstructor<br>@PreDestroy                                              |
# 整合
## MuBatis
```java
初始化SqlSessionFactory
// 1. 创建SqLSessionFactoryBuilder对象
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
// 2. 加载SqLMapConfig.xmL配置文件
InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
// 3. 创建SqLSessionFactory对象
SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);

获取连接,获取实现
// 4. 获取SqlSession
SqlSession sqlSession = sqlSessionFactory.openSession();
//5. 执们SqLSession对象执行查询,获取结果User
AccountDao accountDao = sqlSession.getMapper(AccountDao.class);

获取数据层接口
Account ac = accountDao. findById(2);
System.out.println(ac);

关闭连接
// 6. 释放资源
sqlSession.close();
```
```java
<configuration>
	<properties resource="jdbc.properties"></properties>
//	<typeAliases>
//		<package name="com.itheima.domain"/>
//	</typeAliases>

//	<environments default="mysq1">
//		<environment id="mysq1">
//			<transactionManager type="JDBC"></transactionManager>
//			<dataSource type="POOLED">
//				<property name="driver" value="${jdbc.driver}"></property>
//				<property name="url" value="${jdbc.url}"></property>
//				<property name="username" value="${jdbc.username}"></property>
//				<property name="password" value="${jdbc.password}"></property>
//			</dataSource>
//		</environment>
//	</environments>
@Bean
public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
	SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
	ssfb.setTypeAliasesPackage("com.itheima.domain");
	ssfb.setDataSource(dataSource);
	return ssfb;
}
//	<mappers>
//		<package name="com.itheima.dao"></package>
//	</mappers>
@Bean
public MapperScannerConfigurer mapperScannerConfigurer(){
	MapperScannerConfigurer msc = new MapperScannerConfigurer();
	msc.setBasePackage("com.itheima.dao");
	return msc;
}
</configuration>
```
## Junit
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class AccountServiceTest {
	@Autowired
	private AccountService accountService;
	@Test
	public void testFindById(){
		System.out.println(accountService.findById(|));
	}
}
```
# 事务
- Transactional
- 事务作用： 在数据层保障一系列的数据库操作同成功同失败
- Spring事务作用：在数据/==业务层==保障一系列的数据库操作
```java
publicinterface PlatformTransactionManager{
	void commit(TransactionStatus status)throwsTransactionException;
	void rollback(TransactionStatus status)throws TransactionException;
}
publicinterface PlatformTransactionManager{

}
```
- Spring注解事务通常添加在业务层接口不添加到业务层实现类中，降低耦合
- 注解式事务可以添加到业务方法上表示当前方法开启事务，添加到接口上表示当前接口所有方法开启事务
- 事务角色
	- 事务管理员：发起事务方，在Spring中代指业务层开启事务方法
	- 事务协调员：代指数据层方法，业务层方法
- 配置

| 属性                     | 作用                | 实例                                            |
| ---------------------- | ----------------- | --------------------------------------------- |
| read0nly               | 设置是否为只读事务         | readOnly=true只读事务                             |
| timeout                | 设置事务超时时间          | timeout =-1 (永不超时)                            |
| ==rollbackFor==        | 设置事务回滚异常（class）   | rollbackFor=<br>{NullPointException.class}    |
| rollbackForClassName   | 设置事务回滚异常（String）  | 同上格式为字符串                                      |
| noRollbackFor          | 设置事务不回滚异常（class）  | noRollbackFor =<br>(NullPointException.class) |
| noRollbackForClassName | 设置事务不回滚异常（String） | 同上格式为字符串                                      |
| propagation            | 设置事务传播行为          | .....                                         |
- 运行时异常，error类型回滚
- 事务传播行为：事务协调员对事务管理员所携带事务的处理态度

| 传播属性          | 事务管理员               | 事务协调员                        |
| ------------- | ------------------- | ---------------------------- |
| REQUIRED (默认) | 开启T                 | 加入T                          |
|               | 无                   | 新建T2                         |
| REQUIRES_NEW  | 开启T                 | 新建T2                         |
|               | 无                   | 新建T2                         |
| SUPPORTS      | 开启T                 | 加入T                          |
|               | 无                   | 无                            |
| NOT SUPPORTED | 开启T                 | 无                            |
|               | 无                   | 无                            |
| MANDATORY     | 开启T                 | 加入T                          |
|               | 无                   | ERROR                        |
| NEVER         | 开启T                 | ERROR                        |
|               | 无                   | 无                            |
| NESTED        | 设置savePoint，一旦事务回滚， | 事务将回滚到savePoint处，交由客户响应提交/回滚 |
