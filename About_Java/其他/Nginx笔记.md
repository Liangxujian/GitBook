# Nginx 笔记

> 本篇笔记参考自博客：http://www.belonk.com/c/nginx_download_install.html

## 一、Nginx 是什么



## 二、Nginx Windows 版本的基本使用

### 2.1 下载

Nginx 的官方网址：[http://nginx.org](http://nginx.org/)

Nginx 下载地址：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

![1564372112382](.\assets\1564372112382.png)

如图所示，目前 Nginx 官方分为三个版本：

- 主线开发版本（Mainline version）：即功能较新的处于开发中的版本，可以用于学习，但不太稳定，不适合商用；
- 稳定版本（Stable version）：功能稳定，适合商用；
- 历史遗留版本（Legacy versions）：较早的历史版本。

其中

- CHANGES 的链接，是对于此版本的更新日志记录；
- nginx-x.x.x 的链接，对应的是 Nginx 特定版本的源代码**（Nginx 的 Linux 版本就是源码）**；
- pgp 链接，记录的是使用 GPG 加密软件计算后的签名信息，用于下载文件的验证，防止文件被篡改；
- nginx/Windows-x.x.x 对应的是 Nginx 的 Windows 版本的下载链接。

### 2.2 安装

Nginx Windows 版本为绿色版本，解压即用，无须安装。解压下载的压缩包，得到如下的目录结构：

![1564372505472](.\assets\1564372505472.png)

### 2.3 基本使用

#### 2.3.1 启动

* 方法一

  双击 nginx.exe

* 方法二

  运行 cmd，进入 Nginx 目录，运行命令：

  `nginx.exe`：这种方式会使命令行一直处于执行中，无法进行后续操作

  或者

  `start nginx`：（推荐）

  *logs 文件夹中出现 nginx.pid 文件即为启动成功*

  ![1564372938973](.\assets\1564372938973.png)

#### 2.3.2 关闭

* 方法一

  直接在任务管理器中kill掉nginx进程（不推荐）

* 方法二

  运行 cmd，进入 Nginx 目录，输入命令：

  `nginx.exe –s quit`：平缓停止，完整有序的停止 nginx，并保存相关信息（推荐）

  或者

  `nginx.exe –s stop`：快速停止 nginx，不保存相关信息

#### 2.3.3 重新加载配置文件

运行 cmd，进入 nginx 目录，输入命令：`nginx –s reload`

#### 2.3.4 重新打开日志文件

运行 cmd，进入 nginx 目录，输入命令：`nginx –s reopen`

#### 2.3.5 Nginx 版本查看

运行 cmd，进入 nginx 目录，输入命令：`nginx -v`或者`nginx -V`

*PS：大写的 V 会输出更多的版本及编译配置信息*

## 三、Nginx 配置实例

### 3.1 http 反向代理配置

nginx.conf 配置文件示例： [Nginx config.conf.md](Nginx config.conf.md) 

1. 更改 host：在 C:\Windows\System32\drivers\etc 目录下的 host 文件中添加一条 DNS 记录：
   `127.0.0.1 www.helloworld.com`
2. 启动 webapp（应用），注意启动绑定的端口要和 nginx 中的 `upstream` 设置的端口保持一致。
3. 浏览器打开配置的链接：`www.helloworld.com`，访问成功即可。

### 3.2 负载均衡配置

## 四、Nginx 常用功能

### 4.1 Http代理，反向代理

> 作为web服务器最常用的功能之一，尤其是反向代理。

![23165034317](.\assets\23165034317.png)

Nginx 在做反向代理时，提供性能稳定，并且能够提供配置灵活的转发功能。Nginx 可以根据不同的正则匹配，采取不同的转发策略，比如图片文件结尾的走文件服务器，动态页面走 web 服务器，只要你正则写的没问题，又有相对应的服务器解决方案，你就可以随心所欲的玩。并且 Nginx 对返回结果进行错误页跳转，异常判断等。如果被分发的服务器存在异常，他可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。

### 4.2 负载均衡

Nginx 提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略，就天马行空，只有你想不到的没有他做不到，可以参照所有的负载均衡算法，一一找出来做下实现。

#### 4.2.1 普通轮询

理解为 nginx 随机分发请求。

![普通轮询](.\assets\23171543052.png)

#### 4.2.2 加权轮询

“加权”理解为优先级，性能好的服务器配置高权重。

![加权轮询](.\assets\23171725136.png)

#### 4.2.3 Ip hash算法

对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，以解决session不共享的问题。

![ip hash算法](.\assets\23172234301.png)

### 4.3 web 缓存

Nginx 可以对不同的文件做不同的缓存处理，配置灵活，并且支持 FastCGI_Cache，主要用于对 FastCGI 的动态程序进行缓存。配合着第三方的 ngx_cache_purge，对制定的 URL 缓存内容进行增删管理。

## 五、其他

惊群现象：一个网路连接到来，多个睡眠的进程被同事叫醒，但只有一个进程能获得链接，这样会影响系统性能。

## 六、总结

Nginx 作为轻量级、高并发、高性能的web服务器，目前使用非常广泛。一般建议 Nginx 部署在 Linux 服务器上，以发挥其高并发、高性能的特点。