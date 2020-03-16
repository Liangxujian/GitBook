#### 项目总结

##### Tdog 电子商城（Servlet 中多线程安全问题）

因为 Servlet 在项目中只实例化了一次，因此有多个客户发送请求的时候，它内部的属性或方法会被多个线程改变，数据容易产生覆盖等多线程并发问题。
解决办法：

1. 避免在 Servlet 层中使用实例变量，改在方法内部声明局部变量，使用 HttpSession 和 ServletRequest 存储数据，这两个是线程安全的，来一个用户就新建一个；还有 ServletContext 也是线程安全的。
2. 避免使用非线程安全的集合。

##### IFriend 交友网站（Spring 多线程安全问题）

SpringMVC Controller 层的 DispatchServlet 默认是 singleton（非线程安全的），意味着每个 request 过来，系统都会用原有的 instance 去处理（虽然减少了创建和处理的时间）
避免方法：

1. 避免在 Controller 中定义实例变量；

2. 在 Spring 的配置文件 applicationContext.xml 中配置：

   ````xml
   <!-- 这样配置说明支持多线程事务 -->
   <bean scope = "prototype">
   ````

