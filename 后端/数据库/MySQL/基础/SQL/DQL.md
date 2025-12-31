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