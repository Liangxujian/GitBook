# 数据库索引SQL

### 一、MySQL

#### 1.1 创建索引

##### 1.1.1 ALTER TABLE

ALTER TABLE 用来创建普通索引、UNIQUE 唯一索引或 PRIMARY KEY 索引。

````sql
ALTER TABLE 表名 ADD INDEX 索引名称 (字段1, 字段2, ......);
ALTER TABLE 表名 ADD UNIQUE (字段1, 字段2, ......);
ALTER TABLE 表名 ADD PRIMARY KEY (字段1, 字段2, ......);  -- 添加主键
````

##### 1.1.2 CREATE INDEX

CREATE INDEX 可对表增加普通索引或 UNIQUE 索引。

````sql
CREATE INDEX 索引名称 ON 表名 (字段1, 字段2, ......);
CREATE UNIQUE INDEX 索引名称 ON 表名 (字段1, 字段2, ......);
````

#### 1.2 删除索引

可利用ALTER TABLE或DROP INDEX语句来删除索引。类似于CREATE INDEX语句，DROP INDEX可以在ALTER TABLE内部作为一条语句处理，语法如下。

````sql
DROP INDEX 索引名称 ON 表名;
ALTER TABLE 表名 DROP INDEX 索引名称;
ALTER TABLE 表名 DROP PRIMARY KEY;  -- 删除主键
````

#### 1.3 查看索引

````sql
show index from 表名;
show keys from 表名;
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

### 二、Oracle

#### 2.1 为某一字段创建索引

````sql
create index 索引名称 on 表名(字段1, 字段2, ......);
````

#### 2.2 删除索引

````sql
drop index 索引名称;
````

#### 2.3 查看索引

````sql
select * from user_indexes where table_name=upper('表名');  -- 查询一张表里面索引
select * from user_ind_columns where index_name=('索引名称');  -- 查询被索引的字段
````