# 系统数据库

| 数据库                | 含义                                                    |
| ------------------ | ----------------------------------------------------- |
| mysql              | 存储MySQL服务器正常运行所需要的各种信息(时区、主从、用户、权限等)                  |
| information_schema | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类型及访问权限等                 |
| performance_schema | 为MySQL服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数            |
| sys                | 包含了一系列方便DBA和开发人员利用performance_schema性能数据库进行性能调优和诊断的视图 |
# 常用工具
- **mysql**--mysql客户端工具
```shell
mysql [options] [database]
-u,--user=name           # 指定用户名
-p,--password[=pwd]      # 指定密码
-h,--host=name           # 指定服务器IP或域名
-p,--port=port           # 指定连接接口
-e,--execute=name        # 执行SQL并退出
```
- -e在mysql客户端执行sql，不用连接数据库
- **mysqladmin**
	- 检查服务器配置和当前状态
	- mysqladmin --help
- **mysqlbinlog**
	- 日志管理工具
```shell
mysqlbinlog [options] log-files1 log-files2...
-d,--database=name       指定数据库名，只列出指定数据库相关操作
-o,--offset=#            忽略掉日志中前n行命令
-r,--result-file=name    将输出的文本格式日志输出到指定文件
-s,--short-from          显示简单格式，省略一些信息
--start-datetime=date1 --stop-datetime=date2      指定日期间隔内的所有文件
--start-position=pos1  --stop-position=pos2       指定位置间隔内的所有日志
```
- **mysqlshow**
	- 客户端对象查找工具
```shell
mysqlshow [options][db_name[table_name[col_name]]]
-count 显示数据库及表的统计信息
-i     显示指定数据库或者指定表的状态信息
```
- **mysqldump**
	- 备份数据库或在不同数据库之间进行数据迁移
```shell
mysqldump [options] db_name[tables]
mysqldump [options] --database/-B db1[db2...]
mysqldump [options] --all-database/-A
# 连接选项
-u,--user=name           # 指定用户名
-p,--password[=pwd]      # 指定密码
-h,--host=name           # 指定服务器IP或域名
-P,--port=port           # 指定连接接口
# 输出选项
--add-drop-database      在每个数据库创建语句前加上drop database语句
--add-drop-table         在每个表创建语句前加上drop table语句，默认开启；不开--skip-drop-table
-n,--no-create-db        不包含数据库的创建语句
-t,--no-create-info      不包含数据表的创建语句
-d,--no-data             不包含数据
-T,--tab=name            自动生成两个文件：一个sql文件，创建表结构的语句；一个txt文件，数据文件
```
- **mysqlimport/source**
	- 数据导入工具，导入mysqldump加-T导出的文本文件
```shell
mysqlimport [options] db_name textfile1 [textfile2...]
# 导入sql文件
source /root/xxx.sql
```