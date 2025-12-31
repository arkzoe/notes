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