# MySQL 视图

> 本篇笔记参考自博客：[MySQL视图简介](http://c.biancheng.net/view/2582.html)

## 一、MySQL 创建视图（CREATE VIEW）

````sql
-- 基本语法
CREATE OR REPLACE VIEW <视图名> AS <SELECT语句>
````

### 1.1 单表视图

````sql
CREATE VIEW view_students_info AS SELECT * FROM tb_students_info;
-- 创建后，SELECT语句的结果集会存储到一张虚拟表中
SELECT * FROM view_students_info;
````

默认情况下，创建的单表视图和基本表的字段是一样的，也可以通过指定视图字段的名称来创建视图。

````sql
CREATE VIEW v_students_info(s_id, s_name, d_id, s_age, s_sex, s_height, s_date)
AS SELECT id, name, dept_id, age, sex, height, login_date
FROM tb_students_info;
````

### 1.2 多表视图

````sql
CREATE VIEW v_stu_dept_info(s_id, s_name, d_id, s_age, s_sex, s_height, s_date, d_name)
AS SELECT a.id, a.name, a.dept_id, a.age, a.sex, a.height, a.login_date, b.dept_name
FROM tb_students_info a, tb_departments b
WHERE a.dept_id = b.dept_id;
````

## 二、查询视图

````sql
-- 基本语法
DESCRIBE 视图名;
DESCRIBE v_students_info;
````

## 三、修改视图

````sql
-- 基本语法
ALTER VIEW <视图名> AS <SELECT语句>
ALTER VIEW view_students_info
AS SELECT id,name,age
FROM tb_students_info;
-- 如果不好修改，可以重新生成视图：CREATE OR REPLACE VIEW ......
````

## 四、删除视图

*（这里做大概了解，到实际应用时继续补充）*

````sql
-- 基本语法
DROP VIEW <视图名1> [ , <视图名2> …]
DROP VIEW IF EXISTS v_students_info;
````