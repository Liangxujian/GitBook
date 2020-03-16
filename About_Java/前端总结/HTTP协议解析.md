#### HTTP 协议解析

##### HTTP 协议头部信息解析

HTTP的头信息包括通用头、请求头、响应头和实体头四个部分。每个头域由一个域名，冒号（:）和域值三部分组成。

- **通用头标**：即可用于请求，也可用于响应，是作为一个整体而不是特定资源与事务相关联。
- **请求头标**：允许客户端传递关于自身的信息和希望的响应形式。
- **响应头标**：服务器和于传递自身信息的响应。
- **实体头标**：定义被传送资源的信息。即可用于请求，也可用于响应。

###### HTTP Request Header 请求头

| Header          | 解释                                                         | 示例                                              |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------- |
| Accept          | 指定客户端能够接收的内容类型                                 | Accept: text/plain, text/html                     |
| Accept-Charset  | 浏览器可以接受的字符编码集。                                 | Accept-Charset: iso-8859-5                        |
| Accept-Encoding | 指定浏览器可以支持的web服务器返回内容压缩编码类型。          | Accept-Encoding: compress, gzip                   |
| Accept-Language | 浏览器可接受的语言                                           | Accept-Language: en,zh                            |
| Accept-Ranges   | 可以请求网页实体的一个或者多个子范围字段                     | Accept-Ranges: bytes                              |
| Authorization   | HTTP授权的授权证书                                           | Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ== |
| Cache-Control   | 指定请求和响应遵循的缓存机制                                 | Cache-Control: no-cache                           |
| Connection      | 表示是否需要持久连接。（HTTP 1.1默认进行持久连接）           | Connection: close                                 |
| Cookie          | HTTP请求发送时，会把保存在该请求域名下的所有cookie值一起发送给web服务器。 | Cookie: $Version=1; Skin=new;                     |

