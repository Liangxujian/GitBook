# OracleDriver 报错

> 本篇笔记参考自博客：[Spring boot项目启动报错oracle.jdbc.driver.OracleDriver is deprecated，但项目还可以起来](https://blog.csdn.net/yanyanhj/article/details/88352908)

### 一、问题概述

SpringBoot 项目启动报错：oracle.jdbc.driver.OracleDriver is deprecated，但项目还可以起来

### 二、报错信息

[main] ERROR c.a.d.pool.DruidAbstractDataSource-oracle.jdbc.driver.OracleDriver is deprecated.

### 三、解决方案

修改配置文件中的：
`spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver`
为：
`spring.datasource.driver-class-name=oracle.jdbc.OracleDriver`

### 四、问题分析

在 Orcale9 之后，需要将：
`oracle.jdbc.driver.OracleDriver`
改为：
`oracle.jdbc.OracleDriver`