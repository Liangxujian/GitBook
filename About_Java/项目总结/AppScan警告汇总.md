# IBM Security AppScan警告汇总

## 一、跨站点请求伪造-CSRF

### 1.1 跨站点请求伪造是什么？

跨站点请求伪造-CSRF（Cross Site Request Forgery）是一种网络攻击方式。

说的白话一点就是，别的站点伪造你的请求，最可怕的是你还没有察觉并且接收了。听起来确实比较危险，下面有个经典的实例，了解一下跨站点请求伪造到底是怎么是实现的，知己知彼。

受害者：Father/Son
黑客：Black
银行：bank
     Father 在银行有一笔存款，可以通过请求：`http://bank.example/withdraw?account=father&amount=1000000&for=son`把钱转到 Son 下。通常情况下，该请求到达网站后，服务器会验证请求是否来自一个合法的 session，并且该 session 的用户 Father 已登录。Black 在该银行也有账户，于是他伪造了一个地址：`http://bank.example/withdraw?account=father&amount=1000000&for=black`，但是如果直接访问，服务器肯定会识别出当前登录用户是 Black 而不是 Father，不能接受请求。于是通过 CSRF 攻击方式，将此链接伪造在广告下，诱使 Father 自己点这个链接，那么请求就会携带 Father 浏览起的 cookie 一起发送到银行，而 Father 同时又登录了银行或者刚刚登录不久 session 还没有过期，那服务器发现 cookie 中有 Father 的登录信息，就接收了响应，攻击就成功了。

![1558511801359](D:\GitBook\About_Java\项目总结\assets\1558511801359.png)

### 1.2 CSRF防御策略

- 验证 Referer
  Referer 记录着请求源，从示例可以看出，受害者发送非法请求肯定不是在银行的界面，所以在服务器通过过滤器，验证 Referer 是不是 bank.example 开始就可以了，这个方法简单粗暴。（request.getHeader("Referer");）
  此外，该方法还可以用来实现下载资源防盗链功能
- 在请求参数中添加 token 验证
- 在 HTTP 头中自定义属性并验证

现在还没有一种完美的解决方案，但是通过 Referer 和 Token 方案结合起来使用，能很有效地防御CSRF攻击。

## 二、SQL 盲注

这里遇到的奇葩情况是 AppScan 就针对 url 请求的某个参数报 SQL 盲注的高危警告，尽管已在 Mybatis 中做了过滤，尽管其他参数也用这样的判断过滤，但 AppScan 就是针对这一个 url 请求。。。

````xml
<if test="category_name != null and category_name != '' and category_name != '-1'">
    AND tnno.category_name LIKE CONCAT(#{category_name}, '%') 
</if>
````

AppScan的逻辑就是往 url 请求中注入包含"-1"、"select"、"and"、"or"等 SQL 关键字作为请求参数，若是有返回即视为存在漏洞，这里的解决方法是在 Controller 中对 ajax 参数做过滤，如果包含关键字则立即返回，进入 error() 方法。

## 三、使用HTTP动词篡改的认证旁路

添加拦截器，拦截住除开 GET 和 POST 的方法。

````java
String method = ((HttpServletRequest) request).getMethod();
if ("GET".equals(method) || "POST".equals(method) || "HEAD".equals(method)) {
    chain.doFilter(request, response);
}
````

## 四、临时文件下载警告

````java
// 获取请求路径url
String url = ((HttpServletRequest) request).getRequestURI();
````

通过 url 请求路径判断并过滤其请求的 @requestMapping 地址除 .html 外的以 .* 为尾缀的情况。

## 五、在 Tomcat 里禁用 HTTP 危险方法

通常 HTTP 的 GET、POST 方法比较常用。如果 Tomcat 允许 PUT、DELETE、TRACK、OPTIONS 等方法，一些安全监测软件会认为是允许 HTTP 危险方法，作为一种漏洞写在检测报告中。其实按照 RESTful 风格，恰恰需要使用这些方法。

可以修改 tomcat/conf/web.xml（也可在项目的 /WEB-INF/web.xml 中修改），增加以下代码：

````bash
<?xml version="1.0" encoding="UTF-8"?>
<web-app ...>
    ...
    <security-constraint>
        <web-resource-collection>
            <url-pattern>/*</url-pattern>
            <http-method>PUT</http-method>
            <http-method>DELETE</http-method>
            <http-method>OPTIONS</http-method>
            <http-method>TRACE</http-method>
        </web-resource-collection>
        <auth-constraint></auth-constraint>
    </security-constraint>
</web-app>
````

