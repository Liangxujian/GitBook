#### J2SE、J2EE、J2ME

Java EE（J2EE）：Java 2 Enterprise Edition，是 Java 的一种企业版，用于企业级应用开发（B/S开发）
Java SE（J2SE）：Java 2 Standard Edition，是 Java 的标准版，用于标准应用开发，也是 Java 基础版本（C/S 开发）
Java ME（J2ME）：Java 2 Micro Edition，是 Java 的微型版，用于手机、PDA 等嵌入式开发，针对手机开发（移动端开发）
Java 的三大版本，其版本关系如下：

<img src='D:\GitBook\About_Java\Java\assets\1553501136009.png'>



#### J2EE体系结构

由 J2EE 为 B/S 开发可知：J2EE 为多层分布式的应用模型（三层）

1. 客户层，运行在客户计算机上的组件（前端页面）
2. J2EE 应用服务器（Web 层+业务层）
   &emsp;&emsp;&emsp;Web 层——Tomcat 等容器
   &emsp;&emsp;&emsp;业务层——后台逻辑代码
3. 企业信息系统层（EIS），拿数据的地方（数据库或者客户的其他 ERP 等系统）

<img src='D:\GitBook\About_Java\Java\assets\1553501400434.png'>