# HTTP 协议

> 本篇笔记参考自博客：
>
> [http协议的简单了解](https://www.cnblogs.com/xy404/p/3689219.html)
>
> [常见的HTTP请求头、响应头、状态码](https://blog.csdn.net/en_joker/article/details/81280970)

HTTP（Hypertext transfer protocol）文本传输协议，是一种通过浏览器和服务器进行数据交互，进行超文本（文本、图片、视频等）传输的规定。

![1565658535676](D:\GitBook\About_Java\JavaWeb开发\assets\1565658535676.png)

### 一、HTTP 协议规定了请求的 URL 格式

通过 http 协议中的域名或 IP 找到对应的服务器。

### 二、HTTP 协议规定了发送请求的格式

#### 2.1 请求行

请求行包括请求的方式（get、post 或其他）、要求响应的文件、http 版本。

#### 2.2 请求头

请求头包括本机信息、浏览器信息等等，当然，也包括 get 请求的 URL 中`?`后面的参数。

##### 常见的HTTP请求头

| 请求头          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Accept-Charset  | 用于指定客户端接受的字符集                                   |
| Accept-Encoding | 用于指定可接受的内容编码，如Accept-Encoding:gzip.deflate     |
| Accept-Language | 用于指定一种自然语言，如Accept-Language:zh-cn                |
| Host            | 用于指定被请求资源的Internet主机和端口号，如Host:www.taobao.com |
| User-Agent      | 客户端将他的操作系统、浏览器和其他属性告诉服务器             |
| Connection      | 当前连接是否保持，如Connection:Keep-Alive                    |

#### 2.3 请求体

请求体包括 post 传递数据的相关信息；get 方式传值时，请求体为空。

### 三、HTTP 协议规定了返回响应的格式

#### 3.1 响应码

响应码用来标识服务器响应的结果，如我们常看到的 200、404 等。大致的分类如下：

* 1开头表示消息；
* 2开头表示成功；
* 3开头表示重定向；
* 4开头表示失败；
* 5开头表示服务器异常

##### 常见的HTTP状态码

| 响应码 | 说明                                   |
| ------ | -------------------------------------- |
| 200    | 客户端请求成功                         |
| 302    | 临时跳转，跳转的地址通过Location指定   |
| 400    | 客户端请求有语法错误，不能被服务器识别 |
| 403    | 服务器受到请求，但是拒绝提供服务       |
| 404    | 请求的资源不存在                       |
| 500    | 服务器发生不可预期的错误               |

#### 3.2 响应头

响应头记录服务器相关信息如服务器是否启用压缩、服务器为 IIS 或 Ngnix、程序所用服务端语言等等。当然，缓存也是在这里设置的，通过修改响应头可以修改 html 在本地缓存的情况，如设置浏览器缓存过期的时间。

##### 常见的HTTP响应头

| 响应头           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| Server           | 使用的服务器名称，如Server:Apache/1.3.6(Unix)                |
| Content-Type     | 用来指明发送给接收者的实体正文的媒体类型，如Content-Type:text/html;charset=GBK |
| Content-Encoding | 与请求报头Accept-Encoding对应，告诉浏览器服务端采用的是什么压缩编码 |
| Content-Language | 描述了资源使用的自然语言，与Accept-Language对应              |
| Content-Length   | 指明实体正文的长度，用以字节方式存储的十进制数字来表示       |
| Keep-Alive       | 保持连接的时间，如Keep-Alive:timerout=5,max=120              |

Content-Security-Policy 头标识

X-Content-Type-Options 头标识

X-XSS-Protection 头标识

#### 3.3 响应体

响应体主要是我看到的 html 的相关内容了。