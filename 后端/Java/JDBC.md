- 接口
- 数据库厂商实现
```java
//注册驱动
Class.forName("com.mysql.jdbc.Dricer");
//获取连接
Connection conn = DriverManager.getConnection(url,username,psssword);
//定义SQL语句
String sql = "";
//获取执行SQL对象
Statement stmt = conn.createStatement();
//执行SQL
stml.executeUpdate(sql);
//返回处理结果
//释放资源
stmt.close();
conn.close();
```
# API
## DriverManager
- 驱动关联类
- 注册驱动--MySQL5之后可省
- 获取数据库连接
	- url：连接本机简写为jdbc:mysql:///数据库名?参数键值对&...
		- useSSL = false，禁用安全连接方式
## Connection
1. 获取执行SQL对象
```java
//普通执行SQL对象
Statement createStatement();

//预编译SQL的执行SQL对象，防止SQL注入
PreparedStatement prepareStatement(sql);

//执行存储过程的对象
CallableStatement prepareCall(sql)
```
2. 管理事务
- MySQL事务管理
	- 开启事务：BEGIN;START TRANSACTION;
	- 提交事务：COMMIT;默认自动提交
	- 事务回滚：ROLLBACK;
- JDBC事务管理
	- 开启事务：setAutoCommit(boolean autoCommit);true自动；false手动，开启事务
	- 提交事务：commit();
	- 事务回滚：rollback();
## Statement
- 执行SQL
```java
int ececute(sql);//执行DML，DDL
//返回值：1.DML语句影响的行数 2.DDL语句执行成功可能返回0

ResultSet executeQuery(sql);//执行DQL
//返回值：ResultSet 结果集对象
```
## ResultSet
- 封装DQL查询语句结果
- 获取查询结果
- boolean next()：1.将光标从当前位置向前移动一行 2.判断当前行是否为有效行
	- 返回值：
		- true：有效行，当前行有数据
- xxx GetXxx(参数)：获取数据
- 参数
	- int：列的编号，从1开始
	- String：列的名称
## PrepareStatement
- 预编译SQL的执行SQL对象，防止SQL注入
- 性能高，先发送给服务器检查编译，后执行
- SQL注入：操作输入修改事先定义好的SQL语句
1. 获取PrepareStatement对象
```java
//SQL中的参数值用?替代
String sql = "select * from user where username = ? and password = ?";
//通过Connection对象获取，传入对应sql语句
PreparedStatement pstmt = conn.prepareStatement(sql);//敏感字符转义
```
2. 设置参数
- setXxx(参数1，2)：给?赋值
	- 参数1：?的位置编号，从1开始
	- 参数2：?的值
3. 执行SQL
- executeUpdaate();/executeQuery();
### 原理
- 获取PrepareStatement对象时，将sql语句发给服务器，检查编译
- 执行时不需进行，速度快
- sql模板一样，只需一次检查、编译
1. 开启预编译：useServerPrepStmts=true
2. 配置MySQL执行日志
```mysql
log-output=FILE
general-log=1
general_log_file="p:\mysql.log"
slow-query-log=1
slow_query_log_file="D:\mysql_slow.log"
long_query_time=2
```
# 数据库连接池
- 容器，分配管理数据库连接
- 允许程序重复使用一个现有的数据库连接
- 释放空闲时间超过最大空闲时间的数据库连接来避免数据库连接泄露
- 好处
	- 资源重用
	- 提升响应速率
	- 避免数据库连接泄露
- DataSource
	- 接口，第三方实现
	- Connection getConnection()
- 常用
	- DBCP
	- C3P0
	- Druid
		- 阿里开源
		- 最好的之一
1. 导包
2. 定义配置
3. 加载配置
```java
Properties prop = new PropertiesO);
prop.load(new FileInputStream(name:"src/druid.properties"));
```
4. 获取连接池对象
```java
DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
```
5. 获取数据库连接
```java
Connection connection = dataSource.getConnectionO;
```