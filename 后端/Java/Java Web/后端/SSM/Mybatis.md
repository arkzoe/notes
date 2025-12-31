- 持久层框架，简化JDBC
1. 引入依赖
2. 准备数据库表，实体类
3. 配置Mybatis--application.properties
4. 编写Mybatis持久层接口，定义SQL（注解/XML）
	1. @Mapper应用程序运行时自动为接口创建实现类对象
	2. 自动将实现类对象注入到IOC容器
	3. SpringBootTest单元测试运行时加载SpringBoot环境

# mybatis的配置 
- mybatis. configuration. log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
- 查看日志
# 数据库连接池
- 容器，分配管理数据库连接
- 标准接口：DataSourse
	- 获取连接
- 常见
	- C3P0
	- DBCP
	- Druid
		- 阿里
	- Hikari--SpringBoot默认
# 增删改查
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
# xml映射
1. XML映射文件的名称与Mapper接口名称一致,并且将XML映射文件和Mapper接口放置在相同包下(同包同名)，不用.用/
2. XML映射文件的namespace属性为Mapper接口全限定名一致
3. XML映射文件中sql语句的id与Mapper 接口中的方法名一致,并保持返回类型一致
- application.properties--指定XML映射配置文件的位置
```xml
mybatis.mapper-locations=classpath:mapper/ *. xml
```
- mybatisX插件