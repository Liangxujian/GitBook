# MySQL 和 Oracle

## 一、自我总结

1. Oracle 是大型数据库，而 MySQL 是中小型数据库；
2. Oracle 支持多用户、大并发、大事务量的处理；
3. Oracle 能对数据的安全性和完整性进行有效控制；
4. Oracle 支持分布式数据处理，而 MySQL 较难实现；
5. Oracle 的可移植性很强；
6. Oracle 拥有大量的内置函数，例如 SYSDATE（得到系统当前时间）

## 二、具体对比

### 2.1 自动增长的数据类型区别

* MySQL 有自动增长的数据库类型，插入记录时不用操作字段，就会自动递增
* Oracle 没有自动增长的数据类型，需要建立一个自动增长的序列号，插入记录时要把序列号的下一个值赋予此字段

````sql
-- 创建序列号 emp_sequence：
CREATE SEQUENCE emp_sequence 
START WITH 1 -- 从1开始计数
INCREMENT BY 1 -- 每次加1
NOMAXVALUE -- 表示不设置最大值
NOCYCLE -- 一直累加，不循环
CACHE 10;

INSERT INTO emp VALUES (empseq.NEXTVAL, 'LEWIS', 'CLERK', 7902, SYSDATE, 1200, NULL, 20);
SELECT empseq.CURRVAL FROM dual;
````

`NEXTVAL –- 返回 sequence 的当前值；`
`CURRVAL –- 增加 sequence 的值，然后返回 sequence 值`

### 2.2 单引号的区别

* MySQL 里可以用双引号包起字符串；
* Oracle 只支持单引号，因此如果字符串文本里面包含单引号的，就必须用两个单引号示意。

### 2.3 翻页的 SQL 语句的区别

````sql
-- MySQL（limit）
SELECT * FROM table_name LIMIT 5, 10;

-- Oracle
SELECT * FROM table_name OFFSET 5 ROWS FETCH FIRST 10 ROWS ONLY;
````

### 2.4 日期字段的区别

* MySQL 日期字段有 DATE 和 TIME 两种，用 now() 函数获取当前数据库的时间；
* Oracle 日期字段只有 DATE，用 SYSDATE 获取当前数据库的时间（秒）

### 2.5 空字符串的区别

* MySQL 的非空字段也有空的内容（即 IS NOT NULL 非空值条件可以存放空字符串''）；
* Oracle 定义了非空字段就不容许有空的内容

### 2.6 字符串模糊比较的区别

* MySQL：`LIKE '%字符串%';`
* Oracle：可用`LIKE`，也可以用函数`instr(字段名, '字符串')>0`

### 2.7 释放资源

在程序和函数中，操作数据库的工作完成后要记得释放结果集和指针（略）

## 三、常见数据类型的对应关系

| 说明                                                 | MySQL                                                        | Oracle                                                       |
| ---------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 变长字符串                                           | VARCHAR[0-65535]<br/>定义长度默认按字符长度计算，如果是GBK编码的汉字将占用2个字节 | VARCHAR2[1-4000]<br/>VARCHAR是VARCHAR2的同义词，定义默认按字节长度计算 |
| 整数                                                 | TINYINT(-128-127)<br/>SMALLINT(-32768-32767)<br/>MEDIUMINT(-8388608-8388607)<br/>INT(-2147483648-2147483647)<br/>BIGINT(-9223372036854775808-9223372036854775807) | TINYINT可以用NUMBER(3,0)代替<br/>SMALLINT可以用NUMBER(5,0)代替<br/>MEDUIMINT可以用NUMBER(7,0)代替<br/>INT可以用NUMBER(10,0)代替<br/>BIGINT可以用NUMBER(20,0)代替<br/>ORACLE中有SMALLINT,INT,INTEGER类型，不过这是NUMBER(38,0)的同义词 |
| 数值类型                                             | DECIMAL[1-65[,0-30]]<br/>NUMERIC是DECIMAL的同义词            | NUMBER可表示数范围： 1*10^-130至1*10^126<br/>NUMBER([1-38][,-84-127])<br/>DECIMAL、NUMERIC、DEC是NUMBER的同义词 |
| 浮点型                                               | FLOAT(D,M)                                                   | oracle10g开始增加BINARY_FLOAT类型<br/>10g以前无专用类型，可以用NUMBER代替<br/>ORACLE中有FLOAT和REAL类型，不过这是NUMBER的同义词 |
| 双精度浮点型                                         | DOUBLE(D,M)                                                  | oracle10g开始增加BINARY_DOUBLE类型<br/>10g以前无专用类型，可以用NUMBER代替<br/>ORACLE中有DOUBLE PRECISION类型，不过这是NUMBER的同义词 |
| 位类型                                               | BIT(1-64)                                                    | 无                                                           |
| 日期类型                                             | DATE，3字节存储，只存储日期，没有时间，支持范围是[1000-01-01]至[9999-12-31]<br/>TIME，3字节存储，只存储时间，没有日期，支持范围是[-838:59:59]至[838:59:59]<br/>DATETIME，占8字节存储，可表示日期和时间，支持范围是[1000-01-01 00:00:00]至[9999-12-31 23:59:59]<br/>TIMESTAMP，占4字节存储，可表示日期和时间，范围是[1970-01-01 00:00:00]至[2038-01-19 03:14:07] | DATE类型，7字节存储，可表示日期和时间，支持范围是[-4712-01-01 00:00:00]至[9999-12-31 23:59:59] |
| 高精度日期                                           | 5.6.4以前不支持小数秒精度<br/>5.6.4开始TIME,DATETIME,TIMESTAMP支持，最多可以6位小数秒，也就是微秒级别 | TIMESTAMP[0-9]，占用空间7-11个字节，当小数秒精度为0时与DATE类型相同，小数秒最高精度可达9位，也就是纳精度 |
| 年份                                                 | YEAR，1字节存储，只存储年份，支持范围是[1901]至[2155]        | 无对应类型，可以用NUMBER(3,0)代替                            |
| 定长字符串                                           | CHAR[0-255]，定义长度默认按字符长度计算，最大保存255字符     | CHAR[1-2000]，定义默认按字节长度计算                         |
| 无符号说明                                           | 支持，用于数值类型                                           | 不支持                                                       |
| 大字符串，一般用于存储文本文件或超大描述及备注类信息 | TINYTEXT，最大支持255个字节<br/>TEXT，最大支持65535个字节<br/>MEDIUMTEXT，最大支持16MB个字节<br/>LONGTEXT，最大支持4GB字节<br/>字段不支持默认值 | 支持(CLOB) oracle10g以前最大支持4GB个字节<br/>oracle10g开始最大支持4GB个数据块，数据块大小为2KB-32KB<br/>oracle还有一个LONG类型，是早期的存储大字符串类型，最大支持2GB字节，现已不推荐使用 |
| 二进制对象，一般用于存储文件或图片数据               | TINYBLOB，最大支持255个字节<br/>BLOB，最大支持65535个字节<br/>MEDIUMBLOB，最大支持16MB个字节<br/>LONGBLOB，最大支持4GB字节<br/>字段不支持默认值 | 支持(BLOB)<br/>oracle10g以前最大支持4GB个字节<br/>oracle10g开始最大支持4G个数据块，数据块大小为2KB-32KB<br/>oracle还有一个LONG RAW类型，是早期的存储二进制类型，最大支持2GB字节,现已不推荐使用 |
| 二进制信息                                           | BINARY(0-255)，定长<br/>VARBINARY(0-65535)，变长             | RAW(1-2000)                                                  |
| 枚举类型                                             | ENUM(v1,v2,v3,...)，最多65535个元素                          | 不支持                                                       |
| 集合类型                                             | SET(v1,v2,v3,...)，最多64个元素                              | 不支持                                                       |

