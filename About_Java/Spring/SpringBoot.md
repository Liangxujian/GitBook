# SpringBoot

> 本篇笔记参考自博客：[为什么说 Java 程序员到了必须掌握 Spring Boot 的时候？](http://www.ityouknow.com/springboot/2018/06/12/spring-boo-java-simple.html)

### 一、关于 Spring Boot

#### 1.1 Spring Boot 简介

Spring Boot 并不是一种新的框架，而是在原来 Spring 框架的基础上进行了配置简化（它默认配置了很多框架的使用方式，就像 maven 整合了几乎所有的 jar 包，Spring Boot 整合了大部分的框架配置）。

多数的 Spring Boot 只需要很少的 Spring 配置，在“约定优于配置”的原则下，Spring Boot 框架使用了特定默认的方式来进行配置，从而使开发人员不再需要定义样板化的配置。所有项目所需要集成的常用框架，都有对应的组件支持。

#### 1.2 Spring Boot 特性

* 使用Spring项目引导页面可以在几秒构建一个项目
* 方便对外输出各种形式的服务，如 REST API、WebSocket、Web、Streaming、Tasks
* 非常简洁的安全策略集成
* 支持关系数据库和关系数据库
* 支持运行期内嵌容器，如 Tomcat、Jetty
* 强大的开发包，支持热启动
* 自动管理依赖
* 自带应用监控
* 支持各种 IDE，如 IntelliJ IDEA、NetBeans

### 二、Spring Boot 的优势

#### 2.1 Spring Boot 让开发变得简单

使用 Spring Boot 可以简化我们开发的流程，提升开发效率：

Before：
	1)	配置 web.xml，加载 Spring 和 SpringMVC
	2)	配置数据库连接、配置 Spring 事务
	3)	配置加载配置文件的读取，开启注解
	4)	配置日志文件
	……
	N)  配置完成后部署到 Tomcat 中执行

同时还需要考虑各个版本的兼容性，jar 包冲突等各种问题。

After：
	1)	登录网址http://start.spring.io/ 选择对应的组件直接下载
	2)	导入项目，进行开发

由这可看出，使用 Spring Boot 大大地简化了开发环境搭建方面的准备工作。

#### 2.2 Spring Boot 让测试变得简单

在 Spring Boot 中内置了7中强大的测试框架：

* JUnit：一个 Java 语言的单元测试框架
* Spring Test & Spring Boot Test：为 SpringBoot 应用提供集成测试和工具支持
* AssertJ：支持流式断言的 Java 测试框架
* Hamcrest：一个匹配器库
* Mockito：一个 java mock 框架
* JSONassert：一个针对 JSON 的断言库
* JsonPath：JSON XPath 库

我们只需要在项目中引入 spring-boot-starter-test 依赖包，就可以对数据库、Mock、 Web 等各种情况进行测试

Spring Boot Test 中包含了我们需要使用的各种测试场景，满足我们日常项目的测试需求。

#### 2.3 Spring Boot 让配置变得简单

Spring Boot 让配置变简单，说到这里我们就需要了解一下 Spring Boot 的核心思想：约定优于配置。那么什么是约定优于配置呢？

约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

本质是说，开发人员仅需规定应用中不符约定的部分。例如，如果模型中有个名为 User 的类，那么数据库中对应的表就会默认命名为 user。只有在偏离这一约定时，例如将该表命名为“user_info”，才需写有关这个名字的配置。

#### 2.4 Spring Boot 让部署变得简单

说起 Spring Boot 让部署变简单，就不得不说 Spring Boot 内嵌容器。内嵌容器不只让部署变得简单，其实在开发调试阶段也会带来非常大的便利性，对比以往开发 Web 项目时配置 Tomcat 的繁琐，会让大家使用 Spring Boot 内嵌容器开发时有更深的感触。使用 Spring Boot 开发 Web 项目，让我们不需要关心容器的环境问题，专心写业务代码即可。

Jenkins 是目前持续构建领域使用最广泛的工具之一，Jenkins 是一个独立的开源自动化服务器，可用于自动化各种任务，如构建，测试和部署软件。Jenkins 可以通过本机系统包 Docker 安装，甚至可以通过安装 Java Runtime Environment 的任何机器独立运行。

说直白一点 Jenkins 就是专门来负责如何将代码变成可执行的程序包，将它部署到目标服务器中，并对其运营状态（日志）进行监控的软件。自动化、性能、打包、部署、发布、发布结果自动化验证、接口测试、单元测试等等关于我们打包测试部署的方方面面 Jenkins 都可以很友好的支持。

使用 Jenkins 部署 Spring Boot 项目非常简单，只需要前期做一些简单的配置，当我们需要发布项目时只需要点击项目对应的发布按钮，就可以将项目从版本库中拉取、打包、发布到目标服务器中，大大简化了运维后期的部署工作。

虚拟化技术的发展给我们带来了更多的可能性，我们可以利用容器化技术，将 Spring Boot 项目做成镜像，根据容器集群的策略来实现弹性扩容、动态部署等。所以 Spring Boot + Docker + Jenkins 会将 Spring Boot 项目的部署做得更简单化、智能化。

#### 2.5 Spring Boot 让监控变得简单

可以说 Spring Boot 就是一款自带监控的开源软件，在设计之初就考虑到应用的监控问题，专门提供了一款监控组件来完成这个工作，这个组件就是 Spring Boot Actuator。

Spring Boot Actuator 是 Spring Boot 提供的对应用系统监控的集成功能，可以查看应用配置的详细信息，例如自动化配置信息、创建的 Spring beans 以及一些环境属性等。

当然 Spring Boot Actuator 虽然可以监控一个 Spring Boot 应用的健康情况，实际上现在的系统都是需要很多的服务相互配合来完成工作，如何通过一个监控软件来监控所以的 Spring Boot 项目将变得比较紧迫。

在开源界也有人意识到了这个问题，并且基于 Spring boot actuator 做出了一款强大的监控软件，这个软件就是 Spring Boot admin。

Spring Boot Admin 是一个管理和监控 Spring Boot 应用程序的开源软件。每个应用都认为是一个客户端，通过 HTTP 或者使用 Eureka 注册到 admin server 中进行展示，Spring Boot Admin UI 部分使用 AngularJs 将数据展示在前端。

Spring Boot Admin 是一个针对 spring-boot 的 actuator 接口进行 UI 美化封装的监控工具。他可以：在列表中浏览所有被监控 spring-boot 项目的基本信息，详细的 Health 信息、内存信息、JVM 信息、垃圾回收信息、各种配置信息（比如数据源、缓存列表和命中率）等，还可以直接修改 logger 的 level。

使用 Spring Boot Admin 不仅可以监控 Spring Boot 项目，还可以监控 Spring Cloud 项目，因此使用了 Spring Boot 项目之后我们监控 Spring Boot 集群效果如下：

![1562837215715](D:/GitBook/About_Java/Spring/assets/1562837215715.png)

简单、直观、易用是它的特点，针对一些特殊情况还可以提供报警服务。所以说使用 Spring Boot Actuator 解决了单个 Spring Boot 的监控问题，使用 Spring Boot Admin 就是解决了整个集群监控的问题。

### 三、Spring、Spring Boot 和 Spring Cloud

Spring 最初最核心的两大核心功能 Spring IOC 和 Spring AOP 成就了 Spring，Spring 在这两大核心的功能上不断的发展，才有了 Spring 事务、Spring MVC 等一系列伟大的产品，最终成就了 Spring 帝国，到了后期 Spring 几乎可以解决企业开发中的所有问题。

Spring Boot 是在强大的 Spring 帝国生态基础上面发展而来，发明 Spring Boot 不是为了取代 Spring，是为了让人们更容易的使用 Spring。所以说没有 Spring 强大的功能和生态，就不会有后期的 Spring Boot 火热，Spring Boot 使用约定优于配置的理念，重新重构了 Spring 的使用，让 Spring 后续的发展更有生命力。

Spring Cloud 是一系列框架的有序集合。它利用 Spring Boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot 的开发风格做到一键启动和部署。

Spring 并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过 Spring Boot 风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。

根据上面的说明我们可以看出来，Spring Cloud 是为了解决微服务架构中服务治理而提供的一系列功能的开发框架，并且 Spring Cloud 是完全基于 Spring Boot 而开发，Spring Cloud 利用 Spring Boot 特性整合了开源行业中优秀的组件，整体对外提供了一套在微服务架构中服务治理的解决方案。

综上我们可以这样来理解，正是由于 Spring IOC 和Spring AOP 两个强大的功能才有了 Spring，Spring 生态不断的发展才有了 Spring Boot，使用 Spring Boot 让 Spring 更易用更有生命力，Spring Cloud 是基于 Spring Boot 开发的一套微服务架构下的服务治理方案。