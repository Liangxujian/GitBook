# OA、CRM、ERP

* OA（Office Automation，办公自动化） —— 负责组织行为管理
* CRM（Customer Relationship Management ，客户关系管理） —— 负责客户经营
* ERP（Enterprise Resource Planning，企业资源计划） —— 负责业务处理

很多时候，为了避免重复的维护，通常其他系统（CRM，ERP，其他业务支撑系统）会从 OA 中获取组织架构。

获取方式：

1. WebService 接口（这里指系统之间的接口）：
   * 优点：基于 HTTP 协议，是标准通用的。
   * 缺点：数据冗余较多，比较麻烦。
2. 数据库共享：比如通过创建数据库视图（view），将视图授权给特定数据库用户。
   * 优点：简单。
   * 缺点：只能在公司内部的网络。
3. 使用 json：通过 Servlet/Controller 提供 json 数据。
   * 优点：相对简单，数据冗余少