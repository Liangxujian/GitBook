# MySQL 日期操作语句

> 天 - 周 - 月 - 季度 - 年

```sql
SELECT NOW(); -- 2021-02-20 18:21:22
SELECT CURDATE(); -- 2021-02-20
SELECT CURTIME(); -- 18:21:22
DATE_FORMAT -- 日期格式化
DATE_SUB -- 日期减去指定的时间间隔
DATE_ADD -- 日期添加指定的时间间隔
-- 今天
SELECT * FROM table_name WHERE TO_DAYS(column_name) = TO_DAYS(NOW());
-- 昨天
SELECT * FROM table_name WHERE TO_DAYS(NOW()) - TO_DAYS(column_name) <= 1;
-- 七天内的数据
SELECT * FROM table_name WHERE DATE_SUB(CURDATE(), INTERVAL 7 DAY) <= DATE(column_name);
-- 30天内的数据
SELECT * FROM table_name WHERE DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= DATE(column_name);

-- 查询本周的数据
SELECT * FROM table_name WHERE YEARWEEK(DATE_FORMAT(column_name, '%Y-%m-%d')) = YEARWEEK(NOW());
SELECT * FROM table_name WHERE WEEKOFYEAR(DATE_FORMAT(column_name, '%y-%m-%d')) = WEEKOFYEAR(NOW())
-- 查询上周的数据
SELECT * FROM table_name WHERE YEARWEEK(DATE_FORMAT(column_name, '%Y-%m-%d')) = YEARWEEK(NOW()) - 1;

-- 查询本月的数据
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y%m') = DATE_FORMAT(CURDATE(), '%Y%m');
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y-%m') = DATE_FORMAT(NOW(), '%Y-%m');
SELECT * FROM table_name WHERE YEAR(DATE_FORMAT(column_name, '%y-%m-%d')) = YEAR(NOW()) AND MONTH(DATE_FORMAT(column_name, '%y-%m-%d')) = MONTH(NOW());
-- 查询本月的数据（忽略年度的月度数据）
SELECT * FROM table_name WHERE MONTH(DATE_FORMAT(column_name, '%y-%m-%d')) = MONTH(NOW());
-- 查询上个月的数据
SELECT * FROM table_name WHERE PERIOD_DIFF(DATE_FORMAT(NOW(), '%Y%m'), DATE_FORMAT(column_name, '%Y%m')) = 1;
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y-%m') = DATE_FORMAT(DATE_SUB(CURDATE(), INTERVAL 1 MONTH), '%Y-%m');
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y%m') = DATE_FORMAT(DATE_SUB(CURDATE(), INTERVAL 1 MONTH), '%Y%m');
-- 查询某年某月数据（2017年10月份）
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y-%m') = '2017-10';
SELECT * FROM table_name WHERE DATE_FORMAT(column_name, '%Y-%m') = DATE_FORMAT('2017-10-05', '%Y-%m');

-- 查询本季度数据（忽略年度的季度数据）
SELECT * FROM table_name WHERE QUARTER(column_name) = QUARTER(NOW());
-- 查询上季度数据（忽略年度的季度数据）
SELECT * FROM table_name WHERE QUARTER(column_name) = QUARTER(DATE_SUB(NOW(), INTERVAL 1 QUARTER));

-- 查询半年内的数据
SELECT * FROM table_name WHERE column_name BETWEEN DATE_SUB(NOW(), INTERVAL 6 MONTH) AND NOW();
-- 查询本年的数据
SELECT * FROM table_name WHERE YEAR(column_name) = YEAR(NOW());
-- 查询去年的数据
SELECT * FROM table_name WHERE YEAR(column_name) = YEAR(DATE_SUB(NOW(), INTERVAL 1 YEAR));
```

