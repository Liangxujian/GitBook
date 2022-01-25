# MySQL 操作语句之 CRUD

## 一、查询库中所有表和表中所有字段

### 查询中所有的表和表注释

```sql
SELECT TABLE_NAME, TABLE_COMMENT
FROM INFORMATION_SCHEMA.TABLES
WHERE table_schema = '库名';
```

### 查询表中所有字段和注释

```sql
SELECT column_name, column_comment
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_name = '表名' AND table_schema = '库名';
```

## 二、对数据的增删改

### 增加

````sql
INSERT INTO tabel_name(column_a, column_b, ...) VALUES(value_a, value_b, ...);
INSERT INTO newemp(empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES(7933, 'zs', 'CLERK', '7902', '1982-01-24', 2000, NULL, 30);
````

### 批量插入

````sql
INSERT INTO newemp(empno, ename, job, mgr, hiredate, sal, comm, deptno) 
VALUES(7933, 'zs', 'CLERK', '7902', '1982-01-24', 2000, NULL, 30),
      (7934, 'ls', 'CLERK', '7903', '1983-10-24', 2000, NULL, 30),
      (7935, 'ww', 'CLERK', '7904', '1984-01-24', 2000, NULL, 30);
````

### 删除

````sql
DELETE FROM table_name WHERE ...;
DELETE FROM newemp WHERE empno=7899;
````

### 修改

````sql
UPDATE table_name SET column_a=value_a, column_b=value_b, ... WHERE ...;
UPDATE newemp SET SAL=SAL+5000 WHERE ENAME='梁%' GROUP BY month(BIR);
````

## 三、对数据表的增删改

### 增加

* 增加数据表字段

  ````sql
  ALTER TABLE 表名 ADD 新字段名称 数据类型;
  ALTER TABLE fasp_t_pm_dept_agency ADD COLUMN IS_DELETED INTEGER DEFAULT 0 COMMENT '0-未删除；1-删除';
  ALTER TABLE emp ADD COLUMN (
      username VARCHAR(20) DEFAULT NULL COMMENT '用户名', 
      address VARCHAR(50) DEFAULT NULL COMMENT '地址');
  ````

* 增加主键约束

  ````sql
  ALTER TABLE 表名 ADD CONSTRAINT 主键名称 PRIMARY KEY(设主键的列名);
  ALTER TABLE emp ADD CONSTRAINT pk_eno PRIMARY KEY(eno);
  ````

* 增加外键约束

  ````sql
  -- 建表前
  FOREIGN KEY(从表字段名) REFERENCES 主表名(主表字段名);
  -- 建表后
  ALTER TABLE 从表名 CONSTRAINT 外键名 FOREIGN KEY(从表字段) REFERENCES 主表名(主表字段名);
  ALTER TABLE dept CONSTRAINT fk_employee_dept FOREIGN KEY(dept_no) REFERENCES employee(dept_no);
  ````

### 删除

* 删除数据表字段

  ````sql
  ALTER TABLE 表名 DROP 字段名;
  ALTER TABLE emp DROP tel;
  ````

* 删除主键约束

  ````sql
  ALTER TABLE 表名 DROP FOREIGN KEY 外键约束名称;
  ALTER TABLE emp DROP FOREIGN KEY fk_emp_dept;
  ````

* 删除外键约束

  ````sql
  ALTER TABLE 表名 DROP 字段名;
  ALTER TABLE employee DROP FOREIGN fk_employee_dept;
  ````

### 修改

> 注：如果 UPDATE 语句中 WHERE 条件走的是索引，则对该表施加的就是行锁；如果 SQL 语句没有走索引，则会全表扫描，行锁无法实现（行锁是通过索引加载的），取而代之的是表锁，即在此 UPDATE  语句执行完之前，任何对该表的 UPDATE 操作都是不允许的

* 修改表名

  ````sql
  ALTER TABLE 旧表名称 RENAME 新表名称;
  ALTER TABLE employee RENAME emp;
  ````

* 修改字段的数据类型

  ````sql
  ALTER TABLE 表名 MODIFY 字段名称 新数据类型;
  ALTER TABLE emp MODIFY salary INT(11);
  ````

* 修改字段名称

  ````sql
  ALTER TABLE 表名 CHANGE 旧字段名称  新字段名称  新数据类型;
  ALTER TABLE emp CHANGE name empName VARCHAR(20);
  ````