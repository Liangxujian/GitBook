# Oracle 查询语句记录

### 一、Oracle 关于时间获取及查询

> 本小结参考自博客：[http://www.2cto.com/database/201304/202675.html](http://www.2cto.com/database/201304/202675.html)

#### 1.1 获取系统当前时间

````sql
SELECT TO_CHAR(SYSDATE, 'yyyy-mm-dd hh24:mi:ss') FROM dual;
````

#### 1.2 获取年、月、日等

````sql
SELECT TO_CHAR(SYSDATE, 'yyyy' ) FROM dual; -- 获取当前年号
SELECT TO_CHAR(SYSDATE, 'MM' ) FROM dual; -- 获取当前月份
SELECT TO_CHAR(SYSDATE, 'Q' ) FROM dual; -- 获取年中的第几季度
SELECT TO_CHAR(SYSDATE, 'iw' ) FROM dual; -- 获取年中的第几周

-- year:
-- y         表示年的最后一位
-- yy        表示年的最后两位
-- yyy       表示年的最后三位
-- yyyy      表示当前年号

-- month:
-- mm        表示当前月份
-- mon       用简写形式，比如11月或者nov
-- month     用全称形式，比如11月或者november

-- day:
-- d         表示当前周的第几天
-- dd        表示当前月的第几天
-- ddd       表示当前年的第几天
-- dy        表示当前周的第几天，简写，比如星期五或者fri
-- day       表示当前周的第几天，全称，比如星期五或者friday

-- hour:
-- hh        小时(12)
-- hh24      小时(24)

-- mi        分
-- ss        秒
-- iw/ww     当年的第几个星期
-- W         当月的第几个星期（每年的1月1号至1月7号为第一周，以此类推，每年53周）
````

#### 1.3 日期格式转化

````sql
-- 获取时间函数：SYSDATE()
-- 格式化时间：TO_CHAR(SYSDATE(), 'YY/MM/DD HH24:MI:SS') 或 TO_DATE(SYSDATE(), 'YY/MM/DD HH24:MI:SS')
SELECT TO_DATE(SYSDATE, 'yy-mm-dd hh24:mi:ss') FROM dual
SELECT TO_DATE('2009-12-25 14:23:31', 'yyyy-mm-dd,hh24:mi:ss') FROM dual
-- 如果将时间改为12进制则错误（14>12）
SELECT TO_DATE('2009-12-25 14:23:31', 'yyyy-mm-dd,hh:mi:ss') FROM dual
````

#### 1.4 时间的加减

````sql
-- 当前时间减去7分钟
SELECT SYSDATE, SYSDATE - INTERVAL '7' MINUTE FROM dual
-- 小时（hour）；天（day）；月（month）；年（year）以此类推
-- 时间范围条件查询
SELECT * FROM table_name
WHERE TO_CHAR(UPDATE_TIME, 'yyyy-mm-dd hh24:mi:ss')
BETWEEN TO_CHAR(SYSDATE - INTERVAL '7' DAY, 'yyyy-mm-dd hh24:mi:ss') 
AND TO_CHAR(SYSDATE, 'yyyy-mm-dd hh24:mi:ss')
````

