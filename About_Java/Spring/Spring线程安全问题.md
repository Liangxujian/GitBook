# Spring 线程安全性问题

由于 Spring 管理的 Bean 对象默认是单例模式，也就是说，面对不同的 request 请求，系统都会用原有的 instance 去处理，当多个线程调用它的时候，它里面的 instance 变量就不是线程安全的了，会发生窜数据的问题。

例如：有一个控制器是使用单例形式，且 Controller 中有一个私有的变量 a，所有请求到同一个 Controller 时，使用的 a 变量是共用的，若是某个请求中修改了这个变量 a，则在别的请求中能够读到这个修改的内容。

解决方法：

1. 在 Controller 中使用 ThreadLocal 变量，将变量 a 存储到一个线程安全的地方
2. 在 Spring 配置文件 Controller 中声明 scope="prototype"，每次都创建新的 controller

*PS：我们在使用 Spring 开发 web 时要注意：默认 Controller、Dao、Service 都是单例的。*

### ThreadLocal 示例和说明

[ThreadLocal.md]( [ThreadLocal.md](..\Java高并发拓展\ThreadLocal.md) )

