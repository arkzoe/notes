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