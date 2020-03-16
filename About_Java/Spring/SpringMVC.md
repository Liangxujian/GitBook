#### SpringMVC

##### 一、SpringMVC 处理请求流程

① 浏览器发送请求
② DispatchServlet 接收请求并把访问路径送到`映射处理器`中进行解析
③ 经过解析后，通过处理器适配调用相应 Controller 中的处理方法
④ 视图解析
⑤ 视图渲染
⑥ 返回相应

首先是浏览器发送请求，由 SpringMVC 的 DispatchServlet 接收请求并把请求路径送到映射处理器中进行解析；
处理器经过解析，调用对应的 Controller 方法进行逻辑处理；
后台程序执行完毕后，视逻辑进行转发或重定向，或者返回 json 格式的字符串给请求端。

##### 二、SpringMVC 和 Struts2 区别：

1. 机制：
   **SpringMVC 的入口是 servlet，而 Struts2 的是 filter。**

2. 性能：
   SpringMVC 的性能会比 Struts2 稍快。

3. 设计：
   **Struts2 是基于类的设计**，每次发送请求都会实例一个 action，每个 action 都会被注入属性；
   **SpringMVC 基于方法设计**，粒度更细。

   **SpringMVC 是方法级别的拦截**，拦截到方法后`根据参数上的注释，把 request 数据注入`进去，一个方法对应一个 request 上下文；
   **Struts2 是类级别的拦截**，每次来请求就创建一个 action，然后`调用 setter 和 getter 方法把 request 中的数据注入`，即通过 setter 和 getter 方法和 request 打交道，一个 action 对象对应一个 request 上下文。

4. 参数传递：
   **Struts2** 在接收参数的时候，可以用属性来接收参数，说明**参数是多个方法共享的**；
   **SpringMVC 方法之间独立独享** request、response 数据，请求数据通过参数获取。
5. intercepter 的实现机制：
   Struts2 有自己的 interceptor 机制；
   SpringMVC 用的是独立的 AOP 方式。
   因此 Struts2 的配置文件量比 SpringMVC 大，SpringMVC 更加简洁，开发效率也会更高。
6. SpringMVC 验证：
   **SpringMVC 在处理 ajax 请求的时候很方便，只需一个注解 @ResponseBody，然后直接返回相应文本即可；**
   **而Struts2 则需要导入相关json插件。**
7. 配置：
   **SpringMVC 和 Spring 是无缝的，因此项目的管理和安全比 Struts2 高；**
   **Struts2 的实现需要 xml 配置更多。**

##### 口述总结

具体来说有几点：

1. 组件级别方面（Java 三大组件：servlet、filter、listener）
   SpringMVC 的入口是 servlet，而 Struts2 的是 filter
2. 拦截等级方面
   SpringMVC 是方法级别的拦截，请求的 URL 根据映射处理器的解析找到对应的处理方法，一个方法对应一个 request 上下文；
   Struts2 是类级别的拦截，每次来请求就创建一个 action，通过 setter 和 getter 方法处理 request 请求，一个 action 对象对应一个 request 上下文
3. 请求参数方面
   Struts2 在接收参数的时候，可以用属性来接收参数，因此它的参数是可以多个方法共享的；
   SpringMVC 不同的处理方法之间独立独享 request 和 response 数据
4. 对 ajax 请求返回 json 数据方面
   SpringMVC 在处理 ajax 请求的时候很方便，只需要一个 @ResponseBody 注释，然后直接返回相应文本即可，而Struts2 则需要相关的 json 插件
5. 和 Spring 框架兼容方面
   SpringMVC 和 Spring 是无缝的，所以项目的管理和安全比 Struts2 高