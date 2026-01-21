# DCL
### 用户管理
```mysql
查询用户
USE mysql;
SELECT * FROM user;

创建用户
CREATE USER '用户名'@'主机名' IDENRIGIED BY '密码';

修改用户密码
ALTER USER '用户名'@'主机名' IDENRIGIED WITH mysql_native_password BY '密码';

删除用户
DROP USER '用户名'@'主机名';
```
### 权限控制

| 权限                 | 说明         |
| ------------------ | ---------- |
| ALL,ALL PRIVILEGES | 所有权限       |
| SELECT             | 查询数据       |
| INSERT             | 插入数据       |
| UPDATE             | 修改数据       |
| DELETE             | 删除数据       |
| ALTER              | 修改表        |
| DROP               | 删除数据库/表/视图 |
| CREATE             | 创建数据库/表    |
```mysql
查询权限
SHOW GRANTS FOR '用户名'@'主机名';

授予权限
GRANT 权限列表 ON 数据库.表名 TO '用户名'@'主机名';

撤销权限
REVOKE 权限列表 ON 数据库.表名 FROM '用户名'@'主机名';
```
# DDL
## 数据库操作
### 查询
```mysql
所有数据库   show databases;
当前        select databases();
```
### 创建
```mysql
CREATE database [if not exists] 数据库名
	[DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```
### 删除
```mysql
DROP DATABSE [IF EXIST] 数据库名;
```
### 使用
```mysql
USE 数据库名;
```
## 表操作
### 查询
```mysql
查询当前数据库所有表
SHOW TABLES;

查询表结构
DESC 表名;

查询指定表的建表语句
SHOW CREATE TABLE 表名;
```
### 创建
```mysql
CREAT TABLE 表名(
	字段1 类型 [COMMENT 字段1注释],
	字段2 类型 [COMMENT 字段2注释],
	...       最后一个自段没逗号
)[COMMENT 表注释];
```
### [[数据类型]]
### 修改
```mysql
添加
ALTER TABLE 表名 ADD 字段名 类型 [comment 注释][约束]

修改数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型

改字段名和字段类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型（长度） [comment 注释][约束]

删除字段
ALTER TABLE 表名 DROP 字段名

修改表名
ALTER TABLE 表名 RENAME TO 新表名

删表
DROP TABLE [IF EXISTS] 表名

删除指定表，并重新创建
TRUNCATE TABLE 表名
```
# DML
### 添加(INSERT)
```mysql
给指定字段添加数据
INSERT INTO 表名 (字段名1，字段名2，。。。) VAKUES(值1，值2，。。。);

给全部字段添加数据
INSERT INTO 表名 VALUES(值1，值2，。。。);

批量添加数据
INSERT INTO 表名(字段名1，字段名2，。。。) VALUSE(值1，值2，。。。),(值1，值2，。。。);
INSERT INTO 表名 VALUES(值1，值2，。。。),(值1，值2，。。。);

-- 字符串日期类型应包含在引号中
```
### 修改(UPDATE)
```mysql
UPDATE 表名 SET 字段名1 = 值1,字段名2 = 值2,...[WHERE D条件];
```
### 删除(DELETE)
```mysql
DELETE FROM 表名 [WHERE 条件];
```
# DQL
### 语法
```mysql
SELECT
	字段列表
FROM
	表名列表   //基本查询
WHERE
	条件列表   //条件查询
GROUP BY
	分组字段列表
HAVNG
	分组后条件列表//分组查询  聚合函数
ORDER BY
	排序字段列表//排序查询
LIMIT
	分页参数//分页查询
```
#### 基本查询
```mysql
查询多个字段
SELECT 字段1，字段2，。。。FROM 表名;
SELECT * FROM 表名;

设置别名
SELECT 字段1[AS 别名1]，字段2[AS 别名2]，。。。FROM 表名;

去除重复记录
SELECT DISTINCT 字段列表 FROM 表名;
```
#### 条件查询
```mysql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

| 比较运算符            | 功能                     |
| ---------------- | ---------------------- |
| >                |                        |
| >=               |                        |
| <                |                        |
| <=               |                        |
| =                |                        |
| <>或!=            |                        |
| BETWEEN...AND... |                        |
| IN(...)          |                        |
| LIKE 占位符         | 模糊匹配（__匹配单个字符，%匹配多个字符） |
| IS NULL          |                        |
| 逻辑运算符            |                        |
| AND 或 &&         |                        |
| OR 或 \|\|        |                        |
| NOT 或 !          |                        |
#### 聚合函数
- 将一列数据作为一个整体，进行纵向计算

| 函数    | 功能  |
| ----- | --- |
| count |     |
| max   |     |
| min   |     |
| avg   |     |
| sum   |     |
SELECT 聚合函数（字段列表）FROM 表名;
NULL 不参与
#### 分组查询
```mysql
SELECT 字段列表 FROM 表名列表 [WHERE 条件列表]
GROUP BY 分组字段列表 [HAVNG 分组后条件过滤条件]
```
where与having区别
- 执行时机不同：where分组前执行，不满足不参与分组，having分组后对结果进行过滤
- 判断条件不同：where不能对聚合函数判断，having可以
==执行顺序：where > 聚合函数 > having
分组后查询字段一般为聚合函数和分组字段，查询其他字段无意义==
#### 排序查询
```mysql
SELECT 字段列表 FROM 表名列表
ORDER BY 字段1 排序方式1，字段2，排序方式2
```
- ASC：升序（默认）
- DESC：降序
#### 分页查询
```mysql
SELECT 字段列表 FROM 表名 LIMIT 起始索引，查询记录数；
```
- 起始索引从0开始，起始索引=（查询页码 - 1）* *每页显示记录
- 分页查询是数据库方言，不同数据库有不同实现
- 查询第一页，起始索引可省
#### 执行顺序
```mysql
SELECT
	字段列表        4
FROM
	表名列表        1
WHERE
	条件列表        2
GROUP BY
	分组字段列表    3
HAVNG
	分组后条件列表
ORDER BY
	排序字段列表    5
LIMIT
	分页参数        6
```