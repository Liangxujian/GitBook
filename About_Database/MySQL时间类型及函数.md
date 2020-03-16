# MySQL 时间类型及函数

本篇笔记参考自博客：[mysql时间类型和格式转换](https://www.cnblogs.com/chunyun/p/11898014.html)

一、MySQL 时间类型

mysql 字段的时间类型分为5种:`DATE`,`DATETIME`,`TIME`,`TIMESTAMP`,`YEAR`;

| 类型      | 字节大小 | 范围                                    | 格式                | 对应Java类型 |
| --------- | -------- | --------------------------------------- | ------------------- | ------------ |
| DATE      | 3        | 1000-01-01/9999-12-32                   | yyyy-MM-dd          |              |
| DATETIME  | 8        | 1000-01-01 00:00:00/9999-12-32 23:59:59 | yyyy-MM-dd hh:mm:ss |              |
| TIME      | 3        | -838:59:59/838:59:59                    | hh:mm:ss            |              |
| TIMESTAMP | 4        | 1970-01-01 00:00:01/2038-01-19 03:14:07 | yyyy-MM-dd hh:mm:ss |              |
| YEAR      | 1        | 1901/2155                               | yyyy                |              |

