# Mybatis
- 持久层框架，简化JDBC
1. 引入依赖
2. 准备数据库表，实体类
3. 配置Mybatis--application.properties
4. 编写Mybatis持久层接口，定义SQL（注解/XML）
	1. @Mapper应用程序运行时自动为接口创建实现类对象
	2. 自动将实现类对象注入到IOC容器
	3. SpringBootTest单元测试运行时加载SpringBoot环境

## mybatis的配置 
- mybatis. configuration. log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
- 查看日志
## 数据库连接池
- 容器，分配管理数据库连接
- 标准接口：DataSourse
	- 获取连接
- 常见
	- C3P0
	- DBCP
	- Druid
		- 阿里
	- Hikari--SpringBoot默认
## 增删改查
- 删--@Delete(delete from user where id = #{id})

| 符号     | 说明                   | 场景          |
| ------ | -------------------- | ----------- |
| #{...} | 占位符，执行时替换为?，生成预编译SQL | 参数值传递       |
| ${..}  | 拼接符，将参数拼在SQL中，SQL注入  | 表名、字段名动态设置时 |
- 增--@Insert
- 改--@Update
- 查--@Select
	- @Param为接口方法形参起名字，对应位置
	- 基于官方骨架创建的springboot项目，接口编译保留形参名，可省
## xml映射
1. XML映射文件的名称与Mapper接口名称一致,并且将XML映射文件和Mapper接口放置在相同包下(同包同名)，不用.用/
2. XML映射文件的namespace属性为Mapper接口全限定名一致
3. XML映射文件中sql语句的id与Mapper 接口中的方法名一致,并保持返回类型一致
- application.properties--指定XML映射配置文件的位置
```xml
mybatis.mapper-locations=classpath:mapper/ *. xml
```
- mybatisX插件
# MabatisPlus
1. 创建新项目
2. 勾jdbc
3. 添加mp起步依赖
4. 设置jdbc参数
5. 实体类与表结构
6. 定义数据接口，继承BaseMapper<>
# 特性
- 无侵入：只做增强不做改变，不会对现有工程产生影响
- 强大的 CRUD 操作：内置通用 Mapper，少量配置即可实现单表CRUD 操作
- 支持Lambda：编写查询条件无需担心字段写错
- 支持主键自动生成
- 内置分页插件
# CRUD制作

| 功能     | 自定义接口                                   | MP接口                                           |
| ------ | --------------------------------------- | ---------------------------------------------- |
| 新增     | boolean save(T t)                       | int insert(T t)                                |
| 删除     | boolean delete(int id)                  | int deleteById(Serializable id)                |
| 修改     | boolean update(T t)                     | int updateById(T t)                            |
| 根据id查询 | T getById(int id)                       | T selectById(Serializable id)                  |
| 查询全部   | List< T> getAll()                       | List< T> selectList()                          |
| 分页查询   | PageInfo< T> getAll(int page, int size) | IPage< T> selectPage(IPage< T> page)           |
| 按条件查询  | List< T> getAll(Condition condition)    | IPage< T> selectPage(Wrapper< T> queryWrapper) |
- Lombok：java类库，简化POJO实体类开发
	- @Date：设置get/set方法，无参/有参，tostring，hashCode，equals
## 分页
1. 设置分页拦截器作为Sprng管理的bean
```java
@Configuration
public class MpCongfig {
	@Bean
	public MybatisPlusInterceptor pageInterceptor(){
			MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
			interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
			return interceptor;
	}
}
```
2. 执行分页查询
3. 开启日志
# DQL编程控制
## 条件查询--设置查询条件
1. 常规
```java
QueryWrapper<User> qw = new QueryWrapper<User>();
//查询年龄大于等于18岁，小于65岁的用户
qw.lt("age",65);
qw.ge("age",18);
List<User> userList = userDao.selectList(qw);
System.out.println(userList);
```
2. 链式编程
```java
QueryWrapper<User> qw = new QueryWrapper<User>();/
/查询年龄大于等于18岁，小于65岁的用户
qw.1t("age",65).ge("age",18);
List<User> userList = userDao.selectList(qw);
System.out.println(userList);
```
3. lambda
```java
QueryWrapper<User> qw = new QueryWrapper<User>();
//查询年龄大于等于18岁，小于65岁的用户
qw.lambda() .1t(User: :getAge,65).ge(User: :getAge,18) ;
List<User> userList = userDao.selectList(qw);
System.out.println(userList);

LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();
//查询年龄大于等于18岁，小于65岁的用户
lqw.1t(User: :getAge,65) .ge(User: :getAge,18);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList) ;
```
- 并且--and
```java
LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();
//查询年龄大于等于18岁，小于65岁的用户
lqw.1t(User: :getAge,65) .ge(User: :getAge, 18);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```
- 或者--or
```java
LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();
//查询年龄大于等于18岁，小于65岁的用户
lqw.1t(User: :getAge,65) .or() .ge(User: :getAge, 18) ;
List<User> userList = userDao.selectList(lqw);
System.out.println(userList) ;
```
### null值处理
- 条件参数控制
```java
LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();
lqw.ge(null != userQuery.getAge(),User::getAge,userQuery.getAge());
lqw.1t(null != userQuery.getAge2(),User::getAge, userQuery.getAge2());
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```
- 条件参数控制--链式编程
```java
LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();lqw.ge(null != userQuery.getAge(),User::getAge,userQuery.getAge())
	.1t(null != userQuery.getAge2(),User::getAge, userQuery.getAge2());
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```
## 查询投影
- 查询结果中包含模型类中部分属性
```java
LambdaQueryWrapper<User> 1qw = new LambdaQueryWrapper<User>();
lqw. select(User: :getId, User: :getName, User: : getAge);
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);
```
- 包含模型类中未定义的属性
```java
QueryWrapper<User> qm = new QueryWrapper<User>();
qm.select("count(*) as nums,gender");
qm.groupBy("gender");
List<Map<String, Object>> maps = userDao.selectMaps(qm);
System.out.println(maps);
```
## 查询条件
- 范围匹配（>、=、between）
- 模糊匹配（like）
- 空判定（null）
- 包含性匹配（in）
- 分组（group）
- 排序（order）
- [条件构造器 | MyBatis-Plus](https://baomidou.com/guides/wrapper/)
## 查询API
## 字段映射与表名映射
- 名称：@TableField
- 类型：属性注解
- 位置：模型类属性定义上方
- 作用：设置当前属性对应的数据库表中的字段关系
- 相关属性
	- value：设置数据库表字段名称
	- exist：设置属性在数据库表字段中是否存在，默认为true。此属性无法与value合并使用
	- select：设置属性是否参与查询，此属性与select（）映射配置不冲突
- 名称：@TableName
- 类型：类注解
- 位置：模型类定义上方
- 作用：设置当前类对应与数据库表关系
- 相关属性
	- value：设置数据库表名称
# DML编程控制
## id生成控制策略
- 名称：@TableId
- 类型：属性注解
- 位置：模型类中用于表示主键的属性定义上方
- 作用：设置当前类中主键属性的生成策略
- 相关属性
	- value：设置数据库主键名称
	- type：设置主键属性的生成策略，值参照IdType枚举值
		- AUTo（0)：使用数据库id自增策略控制id生成
		- NONE（1)：不设置id生成策略
		- INPUT(2)：用户手工输入id
		- ASSIGN_ID（3）：雪花算法生成id（可兼容数值型与字符串型）
		- ASSIGN_UUID(4)：以UUID生成算法作为id生成策略
- 全局配置
```yml
mybatis-plus:
	global-config:
		db-config:
			id-type: auto
			table-prefix: tbl_
```
## 多记录操作
- 按照主键删除多条记录
```java
List<Long> ids= Arrays.asList(new Long[]{2,3});
userDao.deleteBatchIds(ids);
```
- 根据主键查询多条记录
```java
List<Long> ids= Arrays.asList(new Long[]{2,3});
List<User> userList = userDao.selectBatchIds(ids);
```
## 逻辑删除
- 删除操作业务问题：业务数据从数据库中丢弃
- 逻辑删除：为数据设置是否可用状态字段，删除时设置状态字段为不可用状态，数据保留在数据库中
1. 数据库表中添加逻辑删除标记字段--`deleted`
2. 实体类中添加对应字段，设定当前字段为逻辑删除标记字段--`@TableLogic`
3. 配置逻辑删除字段字面值
```yml
mybatis-plus:
	global-config:
		db-config:
			logic-delete-field: deleted
			logic-not-delete-value: 0
			logic-delete-value: 1
```
## 乐观锁
1. 数据库表中添加锁标记字段--`version`
2. 实体类中添加对应字段，并设定当前字段为逻辑删除标记字段--`@version`
3. 配置乐观锁拦截器实现锁机制对应的动态SQL语句拼装
```java
@Configuration
public class MpConfig {
	@Bean
	public MybatisPlusInterceptor mpInterceptor() {
		MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
		mpInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
		return mpInterceptor;
	}
}
```
4. 使用乐观锁机制在修改前必须先获取到对应数据的verion方可正常进行
- 执行修改前先执行查询语句
- 执行修改时使用version作为乐观锁检查依据
# 代码生成器
- MyBatisPlus提供
- 数据库相关配置：读取数据库获取信息
- 开发者自定义配置：手工配置
1. 坐标
```xml
<dependency>
	<groupId>com.baomidou</ groupId>
	<artifactId>mybatis-plus-generator</artifactId>
	<version>3.4.1</version>
</dependency>
<dependency>
	<groupId>org.apache.velocity</groupId>
	<artifactId>velocity-engine-core</artifactId>
	<version>2.3</version>
</dependency>
```
```java
//创建代码生成器对象，执行生成代码操作
AutoGenerator autoGenerator = new AutoGenerator();
autoGenerator.execute();

//数据源相关配置：读取数据库中的信息，根据数据库表结构生成代码
DataSourceConfig dataSourceConfig = new DataSourceConfig();
dataSourceConfig.setUrl("jdbc:mysql: / /localhost:3306/mybatisplus_db?serverTimezone=UTC") ;
dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
dataSourceConfig.setUsername("root");
dataSourceConfig.setPassword("root");
autoGenerator.setDataSource(dataSourceConfig) ;

//设置全局配置
GlobalConfig globalConfig = new GlobalConfig();
globalConfig.setoutputDir(System.getProperty("user.dir")+"/mybatisplus_04_generator/src/main/java");
globalConfig.setopen(false);
globalConfig·setAuthor("黑马程序员");
globalConfig.setFileOverride(true) ;
globalConfig.setMapperName("%sDao") ;
globalConfig.setIdType(IdType.ASSIGN_ID);autoGenerator.setGlobalConfig(globalConfig) ;

//包相关配置
PackageConfig packageInfo = new PackageConfig();
packageInfo.setParent("com.itheima");
packageInfo.setEntity("domain");
packageInfo.setMapper("dao") ;
autoGenerator.setPackageInfo(packageInfo) ;

//策略配置
StrategyConfig strategy = new StrategyConfig();
strategy.setInclude("tbl_user");
strategy.setTablePrefix("tbl_");
strategy.setRestControllerStyle(true);
strategy.setEntityLombokModel(true) ;
strategy.setLogicDeleteFieldName("deleted");
strategy.setVersionFieldName("version");
autoGenerator.setStrategy(strategy);
```