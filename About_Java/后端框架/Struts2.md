#### Struts2

##### 一、Struts2 处理请求流程：

HTTP 请求
↓
web.xml：
HTTP 请求（可以说 url 链接也可以说 form 表单）首先会被 web.xml 里的 StrutsPrepareAndExecuteFilter 过滤器过滤，默认只拦截 .action 的请求，其余拦截在 struts 配置中添加。struts.action.extension 属性自定义的 .do 或其他请求。
↓
struts.xml：
.acion 请求经过 web.xml 过滤转发到 struts.xml，根据 url 地址与相应类进行匹配，匹配成功进入相应的 action 类。
↓
前置拦截器：
在进入相应 action 类之前首先要经过前置拦截器，完成数据封装、文件上传等操作。
↓
Action 类：
经过前置拦截器以后，在 action 类中执行操作，作用相当于 servlet，完成相应操作（比如接收数据、持久化数据）以后返回一个字符串
↓
后置拦截器：
后置拦截器完成异常信息拦截、日志信息处理等操作。
↓
struts.xml：
根据返回的字符串，匹配相应的结果页。
↓
JSP 页面：
JSP 页面就是与 struts.xml 中相匹配的结果页面。

