# MySQL 字段类型对应 Java

| 类型名称  | 显示长度 | 数据库类型            | JAVA类型             | JDBC类型索引(int) | 描述 |
| --------- | -------- | --------------------- | -------------------- | ----------------- | ---- |
| VARCHAR   | L+N      | VARCHAR               | java.lang.String     | 12                |      |
| CHAR      | N        | CHAR                  | java.lang.String     | 1                 |      |
| BLOB      | L+N      | BLOB                  | java.lang.byte[]     | -4                |      |
| TEXT      | 65535    | VARCHAR               | java.lang.String     | -1                |      |
|           |          |                       |                      |                   |      |
| INTEGER   | 4        | INTEGER UNSIGNED      | java.lang.Long       | 4                 |      |
| TINYINT   | 3        | TINYINT UNSIGNED      | java.lang.Integer    | -6                |      |
| SMALLINT  | 5        | SMALLINT UNSIGNED     | java.lang.Integer    | 5                 |      |
| MEDIUMINT | 8        | MEDIUMINT UNSIGNED    | java.lang.Integer    | 4                 |      |
| BIT       | 1        | BIT                   | java.lang.Boolean    | -7                |      |
| BIGINT    | 20       | BIGINT UNSIGNED       | java.math.BigInteger | -5                |      |
| FLOAT     | 4+8      | FLOAT                 | java.lang.Float      | 7                 |      |
| DOUBLE    | 22       | DOUBLE                | java.lang.Double     | 8                 |      |
| DECIMAL   | 11       | DECIMAL               | java.math.BigDecimal | 3                 |      |
| BOOLEAN   | 1        | 同TINYINT             |                      |                   |      |
|           |          |                       |                      |                   |      |
| ID        | 11       | PK (INTEGER UNSIGNED) | java.lang.Long       | 4                 |      |

### 一、MySQL 时间类型

mysql 字段的时间类型分为5种：`DATE`，`DATETIME`，`TIME`，`TIMESTAMP`，`YEAR`；

| 类型      | 字节大小 | 范围                                    | 格式                | 对应Java类型       |
| --------- | -------- | --------------------------------------- | ------------------- | ------------------ |
| DATE      | 3        | 1000-01-01/9999-12-32                   | yyyy-MM-dd          | java.sql.Date      |
| DATETIME  | 8        | 1000-01-01 00:00:00/9999-12-32 23:59:59 | yyyy-MM-dd hh:mm:ss | java.sql.Timestamp |
| TIME      | 3        | -838:59:59/838:59:59                    | hh:mm:ss            | java.sql.Time      |
| TIMESTAMP | 4        | 1970-01-01 00:00:01/2038-01-19 03:14:07 | yyyy-MM-dd hh:mm:ss | java.sql.Timestamp |
| YEAR      | 1        | 1901/2155                               | yyyy                | java.sql.Date      |

*每个时间类型都有一个有效范围和一个零值，当指定的类型的值超过有效范围时，就会使用零值*

### 二、MySQL 关于时间的函数

#### 2.1 DATE_FORMAT() 函数

##### 2.1.1 语法

````sql
DATE_FORMAT(date,format)
````

##### 2.1.2 示例

````sql
--Nov 20 2019 03:11 PM
SELECT DATE_FORMAT(NOW(),'%b %d %Y %h:%i %p');
--11-20-2019
SELECT DATE_FORMAT(NOW(),'%m-%d-%Y');
--20 Nov 19
SELECT DATE_FORMAT(NOW(),'%d %b %y');
--20 Nov 2019 15:11:22:000000
SELECT DATE_FORMAT(NOW(),'%d %b %Y %T:%f');
````

##### 2.1.3 参数解析

 [DATE_FORMAT参数解析.md](others\DATE_FORMAT参数解析.md) 

### 三、Java 添加时间类型数据

````java
service.save(entity)
// 或者
service.insert(entity)
````

### 四、Java 修改或根据时间类型查询

通过 SimpleDateFormat 将 Date 转换成"yyyy-MM-dd HH:mm:ss"等指定时间格式的 String，添加到 HashMap<String, Object> 中作为参数进行使用

