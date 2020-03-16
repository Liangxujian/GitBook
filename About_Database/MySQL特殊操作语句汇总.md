# MySQL 特殊操作语句汇总

### 一、删除数据库中的相同记录

1. distinct
2. group by 字段
3. count(*)>1 根据某个字段统计

````sql
DELETE FROM score WHERE id NOT IN(SELECT * FROM (SELECT id FROM score GROUP BY stu_id,subject)t)
DELETE FROM score WHERE id NOT IN(SELECT * FROM (SELECT id FROM score GROUP BY stu_id,subject)t)
````

### 二、SQL 去重统计

````sql
SELECT COUNT(DISTINCT username) FROM student
````

### 三、将查询结果集插入到另外一张表

`原则是：需要将结果集的字段类型和插入表的字段一一对应起来，最简单的就是 A、B 表结构一致`

1. 将 A 表查询结果集插入到 B 表

  ````sql
  INSERT INTO B SELECT * FROM A
  ````

2. 由此也可拓展将 A、C 表的联表查询结果集插入到B表中

3. 将查询结果集插入到 B 表指定的字段中

  ````sql
  INSERT INTO B(id,aa,bb,dd) SELECT replace(uuid(), '-', ''),aa,bb,ee FROM A
  ````

### 四、将多个查询结果集拼接到一个数据集中

````sql
SELECT * FROM user WHERE username LIKE '刘%' AND address LIKE '湖南%' 
UNION 
SELECT * FROM user WHERE username LIKE '黄%' AND address LIKE '广东%'
````

### 五、根据 ID 查询上一条、下一条记录

传入 ID 为'123456'、用户名为'张三'，前端数据根据用户名排序，且过滤条件为年龄> 20）

上一条记录：

````sql
SELECT * FROM user WHERE age>20 AND username<'张三' 
UNION 
SELECT * FROM user WHERE age>20 AND username='张三' AND id<'123456'
ORDER BY username DESC, id DESC LIMIT 0, 1
````

下一条记录：

````sql
SELECT * FROM user WHERE age>20 AND username>'张三' 
UNION 
SELECT * FROM user WHERE age>20 AND username='张三' AND id>'123456'
ORDER BY username, id LIMIT 0, 1
````

`PS:在MyBatis中注意对大于小于进行转换：">" → "&gt;"；"<" → "&lt;"`

### 六、删除数据库中的所有表（其中 mydb 为具体数据库名称）

````sql
SELECT CONCAT('DROP TABLE IF EXISTS ',table_name,';') FROM information_schema.`TABLES` WHERE TABLE_SCHEMA='mydb'
````

然后复制查询得到的结果集语句，全部执行：

````sql
DROP TABLE IF EXISTS xx;
DROP TABLE IF EXISTS xxx;
DROP TABLE IF EXISTS xxxx;
......
````

### 七、mysql 将字符串字段转为数字排序或比较大小

这里遇到的情况是需要根据企业注册资金排序，但是存储的字段为文本类型，导致排序错乱不准确。

这时候需要把字符串转成数字再排序。

最简单的办法就是在排序的字段后面加上+0：（尽管数据中包含中文单位，而且貌似能过滤掉"NULL"等中文字符）

```sql
SELECT 注册资金 FROM table_name ORDER BY 注册资金+0 desc limit 0,1000
```

![1562745236554](D:\GitBook\About_Database\assets\1562745236554.png)