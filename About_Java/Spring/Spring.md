#### Spring

##### 一、关于 Spring 框架

Spring 是一个轻量级的 Java 开发框架，其核心是 Bean 工厂，我们将对象之间的依赖关系交给 Spring，从而降低组件之间的耦合关系

##### 二、Spring 框架七大模块：

1. `Core 封装包`——提供 IOC 特性（BeanFactory 工厂模式），用于构造所需的 M（Model）

   （将类的创建和依赖关系写在配置文件中，由配置文件注入实现了松耦合）

2. `Context 封装包`——构建于 Core 封装包基础上，提供了一种框架式的对象访问方式
   （比如说通过 Servlet 容器，方便实现 J2EE 的应用）
3. `Dao 封装包`——提供了 JDBC 抽象层，简化了 JDBC 编码（HQL），方便数据库开发
4. `ORM 封装包`——即“对象关系映射”集成，提供简单的声明式事务管理（包括 Hibernate 等）
5. `AOP 封装包`——即“切面编程”，定义`方法拦截器`（method-interceptors）和`切点`（pointcuts），将安全、事务等程序逻辑相对独立的功能抽取出来，利用 Spring 的配置文件将这些功能插进去，实现了按照方面编程，减弱了代码功能的耦合，提高了复用性
6. `Web`和`Web MVC 封装包`——提供了 Java Web 应用的 MVC 框架实现或者与其他流行的 Web 框架集成

##### 三、Spring 框架的 IOC 控制反转

Spring 的 IOC 实际上就是把实例对象的创建和依赖交给 Spring 配置文件，由配置文件注入来实现松耦合。
这其中使用了 Java 的工厂设计模式。

Java 的工厂模式分为三种：简单工厂模式，一般工厂模式和抽象工厂模式。

总的来说，就是提供一个用于创建对象的工厂接口，让其工厂实现类去决定实例化创建哪一个产品类。

##### 四、Spring 框架的 AOP 切面编程

Spring 的 AOP 实际上就是将程序中安全、事务等逻辑相对独立的功能抽取出来，利用 Spring 的配置文件将这些功能插入到指定位置中，实现了按方面编程，减弱了代码之间的耦合，提高了复用性。

这其中使用了 Java 的代理设计模式。

Java 的代理模式分为两种：静态代理和动态代理。

这两种模式的区别是：静态代理的代理类实现的是代理对象的接口，所以当存在多个实现方法的时候，需要一一重写。

而动态代理所实现的接口是`InvocationHandler`，传入的实现类类型和方法名，实现功能，避免了静态代理重写多个方法的缺点。

##### 五、AOP、拦截器和过滤器

###### Spring AOP：

​	Spring AOP，是 AOP 的一种实现，使用的是代理模式。

###### Filter（过滤器）

​	Filter 是 J2EE 的规范，Servlet2.3 开始引入/实现的是职责链模式。Filter 可以用来设置字符集、控制权限、控制转向等等。Filter 也是 AOP 的一种实现。

###### Interceptor（拦截器）

​	Interceptor 是 Struct2 中的概念。同样是 AOP 的一种实现。

###### 拦截器和过滤器的区别：

1. 拦截器是基于 Java 的反射机制，过滤器是基于 java 的函数回调
2. 拦截器不依赖于 servlet 容器，而过滤器依赖于 servlet 容器
3. 拦截器只能对 action 请求起作用，过滤器几乎对所有的请求起作用
4. 拦截器可以访问 action 上下文，值栈里的对象，而过滤器不能访问
5. 在 action 生命周期中，拦截器可以被多次调用，过滤器只能在 servlet 初始化时调用一次
6. 拦截器可以获取 IOC 容器中的各个 bean，过滤器不行，在拦截器中注入一个 service 可以调用逻辑业务