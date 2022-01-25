# MySQL 问题之 cmd

![img](.\assets\491580-20150929152709590-126220061.png)

MySQL 会出现中文乱码的原因主要是以下几点：

1. server 本身设定问题，例如还停留在 latin1
2. table 的语系设定问题（包含 character 与 collation）
3. 客户端程式（例如 php）的连线语系设定问题

强烈建议使用 utf8，utf8 可以兼容世界上所有字符。

## 第一步：修改配置文件编码

找到安装 MySQL 的目录找到 my.ini 文件

![img](.\assets\1145093-20170623161119570-1891308007.png)

用记事本打开 my.ini 文件，看看 default-character-set 是不是 utf8 不是的话改为 utf8 即可！（以前的版本可能没有这句话直接加上就好了！）

```ini
# MySQL client library initialization.
[client]
port=3306
[mysql]
default-character-set=utf8
```

## 第二步：CMD 命令

```sql
mysql> show variables like'%char%';
+--------------------------+---------------------------------------------------------+
| Variable_name            | Value                                                   |
+--------------------------+---------------------------------------------------------+
| character_set_client     | utf8                                                    |
| character_set_connection | utf8                                                    |
| character_set_database   | utf8                                                    |
| character_set_filesystem | binary                                                  |
| character_set_results    | utf8                                                    |
| character_set_server     | utf8                                                    |
| character_set_system     | utf8                                                    |
| character_sets_dir       | E:\Program Files\MySQL\MySQL Server 5.5\share\charsets\ |
+--------------------------+---------------------------------------------------------+
-- 改变原有数据库的编码方式
mysql> set character_set_database=utf8;
Query OK, 0 rows affected (0.11 sec)

mysql> set character_set_server=utf8;
Query OK, 0 rows affected (0.00 sec)

mysql> set character_set_client=gb2312;
Query OK, 0 rows affected (0.00 sec)

mysql> set character_set_connection=gb2312;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like'%char%';
+--------------------------+---------------------------------------------------------+
| Variable_name            | Value                                                   |
+--------------------------+---------------------------------------------------------+
| character_set_client     | gb2312                                                  |
| character_set_connection | gb2312                                                  |
| character_set_database   | utf8                                                    |
| character_set_filesystem | binary                                                  |
| character_set_results    | utf8                                                    |
| character_set_server     | utf8                                                    |
| character_set_system     | utf8                                                    |
| character_sets_dir       | E:\Program Files\MySQL\MySQL Server 5.5\share\charsets\ |
+--------------------------+---------------------------------------------------------+
-- 以上处理插入乱码问题，下面处理查询乱码问题
mysql> set character_set_results=gb2312;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like'%char%';
+--------------------------+---------------------------------------------------------+
| Variable_name            | Value                                                   |
+--------------------------+---------------------------------------------------------+
| character_set_client     | gb2312                                                  |
| character_set_connection | gb2312                                                  |
| character_set_database   | utf8                                                    |
| character_set_filesystem | binary                                                  |
| character_set_results    | gb2312                                                  |
| character_set_server     | utf8                                                    |
| character_set_system     | utf8                                                    |
| character_sets_dir       | E:\Program Files\MySQL\MySQL Server 5.5\share\charsets\ |
+--------------------------+---------------------------------------------------------+

mysql> select * from user;
+---------+----------+----------+------+------+-------------+---------------------+
| user_id | username | password | age  | role | mobile      | create_time         |
+---------+----------+----------+------+------+-------------+---------------------+
|       1 | 小明     | 123456   |   11 | 0    | 12345679810 | 2021-02-22 10:26:33 |
+---------+----------+----------+------+------+-------------+---------------------+
```

### 