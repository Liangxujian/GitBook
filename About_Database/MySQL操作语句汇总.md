# MySQL 操作语句汇总

### 一、对数据的增删改

#### 1.1 增加

````sql
INSERT INTO tabel_name(column_a, column_b, ...) VALUES(value_a, value_b, ...);
INSERT INTO newemp(empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES(7933, 'zs', 'CLERK', '7902', '1982-01-24', 2000, NULL, 30);
````

#### 1.2 批量插入：

````sql
INSERT INTO newemp(empno, ename, job, mgr, hiredate, sal, comm, deptno) 
VALUES(7933, 'zs', 'CLERK', '7902', '1982-01-24', 2000, NULL, 30),
      (7934, 'ls', 'CLERK', '7903', '1983-10-24', 2000, NULL, 30),
      (7935, 'ww', 'CLERK', '7904', '1984-01-24', 2000, NULL, 30);
````

#### 1.3 删除

````sql
DELETE FROM table_name WHERE ...;
DELETE FROM newemp WHERE empno=7899;
````

#### 1.4 修改

````sql
UPDATE table_name SET column_a=value_a, column_b=value_b, ... WHERE ...;
UPDATE newemp SET SAL=SAL+5000 WHERE ENAME='梁%' GROUP BY month(BIR);
````

### 二、对数据表的增删改

#### 2.1 增加

* 增加数据表字段

  ````sql
  ALTER TABLE 表名 ADD 新字段名称 数据类型;
  ALTER TABLE emp ADD address VARCHAR(50);
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

#### 2.2 删除

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

#### 2.3 修改

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