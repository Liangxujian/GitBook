# 数据库索引SQL

## 一、MySQL

### 1.1 创建索引

#### 1.1.1 ALTER TABLE

ALTER TABLE 用来创建普通索引、UNIQUE 唯一索引或 PRIMARY KEY 索引。

````sql
ALTER TABLE 表名 ADD INDEX 索引名称 (字段1, 字段2, ......);
ALTER TABLE 表名 ADD UNIQUE (字段1, 字段2, ......);
ALTER TABLE 表名 ADD PRIMARY KEY (字段1, 字段2, ......);  -- 添加主键

-- table_name 是要增加索引的表名，column_list 指出对哪些列进行索引，多列时各列之间用逗号分隔。
-- 索引名 index_name 可选，缺省时，MySQL 将根据第一个索引列赋一个名称。
-- ALTER TABLE 允许在单个语句中更改多个表，因此可以在同时创建多个索引。
````

#### 1.1.2 CREATE INDEX

CREATE INDEX 可对表增加普通索引或 UNIQUE 索引。

````sql
CREATE INDEX 索引名称 ON 表名 (字段1, 字段2, ......);
CREATE UNIQUE INDEX 索引名称 ON 表名 (字段1, 字段2, ......);

-- table_name、index_name 和 column_list 具有与 ALTER TABLE 语句中相同的含义。
-- 使用 CREATE INDEX 创建索引，索引名不可缺省。
-- 不能用 CREATE INDEX 语句创建 PRIMARY KEY （主键）索引。
````

### 1.2 删除索引

可利用 ALTER TABLE 或 DROP INDEX 语句来删除索引。类似于 CREATE INDEX 语句，DROP INDEX 可以在 ALTER TABLE 内部作为一条语句处理，语法如下。

````sql
DROP INDEX 索引名称 ON 表名;
ALTER TABLE 表名 DROP INDEX 索引名称;
ALTER TABLE 表名 DROP PRIMARY KEY;  -- 删除主键，该语句不需要指定索引名。若表中没有主键索引，但有一个或者多个唯一索引，则MySQL会删除第一个唯一索引。

-- 删除表中某添加了索引的列，会影响到对应索引。如果该索引是组合索引，删除列后，则只是从索引删除该列而已。但如果删除的是索引对应的所有列，则整个索引也会被删除。
````

### 1.3 查看索引

````sql
SHOW INDEX FROM 表名;
SHOW KEYS FROM 表名;
````

| 字段名称     | 注释                                                         |
| ------------ | ------------------------------------------------------------ |
| Table        | 表名                                                         |
| Non_unique   | 如果索引不能包括重复词，则为0。如果可以，则为1               |
| Key_name     | 索引名称                                                     |
| Seq_in_index | 索引中的列序列号，从1开始                                    |
| Column_name  | 索引所涉及的字段名称                                         |
| Collation    | 列以什么方式存储在索引中。在MySQL中，有值‘A'（升序）或NULL（无分类） |
| Cardinality  | 索引中唯一值的数目的估计值。通过运行ANALYZE TABLE或myisamchk -a可以更新。基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL使用该索引的机会就越大 |
| Sub_part     | 如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL |
| Packed       | 指示关键字如何被压缩。如果没有被压缩，则为NULL               |
| Null         | 如果列含有NULL，则含有YES。如果没有，则该列含有NO            |
| Index_type   | 用过的索引方法（BTREE, FULLTEXT, HASH, RTREE）               |
| Comment      | mysql中随机提取数据库记录                                    |

## 二、Oracle

### 2.1 为某一字段创建索引

````sql
CREATE index 索引名称 ON 表名(字段1, 字段2, ......);
````

### 2.2 删除索引

````sql
DROP index 索引名称;
````

### 2.3 查看索引

````sql
SELECT * FROM user_indexes WHERE table_name=upper('表名');  -- 查询一张表里面索引
SELECT * FROM user_ind_columns WHERE index_name=('索引名称');  -- 查询被索引的字段
````

## 三、建立索引注意问题（MySQL）

1. 在对一个大表建立索引时应考虑到表空间和磁盘空间是否足够，同时，建立索引时会对表进行加锁，要注意在业务空闲的时候进行操作。
2. 如果不加索引，那么查找任何哪怕只是一条特定的数据都会进行一次全表扫描，对于一张数据量很大，且数据重复率很小的表来说，加上索引对性能有很大的提高。但对于性别那种只有两个值存储的，就没必要使用索引了。
3. 组合索引：`SELECT * FROM users WHERE area='beijing' AND age=22`
   如果只是在 area 和 age 上分别创建单个索引的话，由于 MySQL 查询每次只能使用一个索引，所以这样已经能相对不做索引时提高了全表扫描很多效率；但是如果在 area、age 两列上建立组合引的话将带来更高的效率。如果我们创建了（area,，age，salary）的复合索引，那么其实相当于创建了（area，age，salary）、（area，age）、（area）三个索引，这被称为最佳左前缀特性。因此我们在创建复合索引时应该将最常用作限制条件的列放在最左边，依次递减。
4. NULL 值和索引：不要让建立了索引的列上存在 NULL 值：在普通索引中，列中为 NULL 值的数据都将不会被包含在索引中；在复合索引中，只要有一列含有NULL 值，那么这一列对于此复合索引就是无效的。
5. 使用短索引：对字符串的列添加索引，如果可以则应尽量加上一个前缀长度。例如一个 CHAR(255) 的列，如果数据大多只有10个或20个字符，且多数的值是惟一的，那么就不要对整个列进行索引。短索引不仅可以提高查询速度而且可以节省磁盘空间和 I/O 操作。
6. 排序的索引问题：MySQL 查询只使用一个索引，因此如果 where 子句中已经使用了索引的话，那么 order by 中的列是不会使用索引的。因此如果排序的要求符合数据库的默认排序，就不要使用排序操作；且尽量不要包含多个列的排序，可考虑建立组合索引。
7. LIKE 和索引：`LIKE '%张%'`不会使用索引，`LIKE '张%'`会使用索引。
8. 不要在列上进行运算：SELECT * FROM users WHERE YEAR(adddate)
   （PS：`YEAR()`函数会返回一个 DATE 类型数据的年份：YEAR('2018-08-08') => 2018）
9. 不使用 NOT IN 和 IN：可考虑 NOT EXISTS 和 EXISTS 代替