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