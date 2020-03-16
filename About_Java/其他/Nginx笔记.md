# Nginx 笔记

> 本篇笔记参考自博客：http://www.belonk.com/c/nginx_download_install.html

## 一、Nginx 是什么



## 二、Nginx Windows 版本的基本使用

### 2.1 下载

Nginx 的官方网址：[http://nginx.org](http://nginx.org/)

Nginx 下载地址：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

![1564372112382](D:\GitBook\About_Java\其他\assets\1564372112382.png)

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

![1564372505472](D:\GitBook\About_Java\其他\assets\1564372505472.png)

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

  ![1564372938973](D:\GitBook\About_Java\其他\assets\1564372938973.png)

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

## 三、Nginx 实战配置

### 3.1 http 反向代理配置

nginx.conf 配置文件如下：

```bash
#运行用户
#user somebody;

#启动进程,通常设置成和cpu的数量相等
worker_processes  1;

#全局错误日志
error_log  D:/Tools/nginx-1.10.1/logs/error.log;
error_log  D:/Tools/nginx-1.10.1/logs/notice.log  notice;
error_log  D:/Tools/nginx-1.10.1/logs/info.log  info;

#PID文件，记录当前启动的nginx的进程ID
pid        D:/Tools/nginx-1.10.1/logs/nginx.pid;

#工作模式及连接数上限
events {
    worker_connections 1024;    #单个后台worker process进程的最大并发链接数
}

#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    #设定mime类型(邮件支持类型),类型由mime.types文件定义
    include       D:/Tools/nginx-1.10.1/conf/mime.types;
    default_type  application/octet-stream;

    #设定日志
    log_format  main  '[$remote_addr] - [$remote_user] [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log    D:/Tools/nginx-1.10.1/logs/access.log main;
    rewrite_log     on;

    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile        on;
    #tcp_nopush     on;

    #连接超时时间
    keepalive_timeout  120;
    tcp_nodelay        on;

    #gzip压缩开关
    #gzip  on;

    #设定实际的服务器列表
    upstream zp_server1{
        server 127.0.0.1:8089;
    }

    #HTTP服务器
    server {
        #监听80端口，80端口是知名端口号，用于HTTP协议
        listen       80;

        #定义使用www.xx.com访问
        server_name  www.helloworld.com;

        #首页
        index index.html

        #指向webapp的目录
        root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp;

        #编码格式
        charset utf-8;

        #代理配置参数
        proxy_connect_timeout 180;
        proxy_send_timeout 180;
        proxy_read_timeout 180;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarder-For $remote_addr;

        #反向代理的路径（和upstream绑定），location 后面设置映射的路径
        location / {
            proxy_pass http://zp_server1;
        }

        #静态文件，nginx自己处理
        location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp\views;
            #过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
            expires 30d;
        }

        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status           on;
            access_log            on;
            auth_basic            "NginxStatus";
            auth_basic_user_file  conf/htpasswd;
        }

        #禁止访问 .htxxx 文件
        location ~ /\.ht {
            deny all;
        }

        #错误处理页面（可选择性配置）
        #error_page   404              /404.html;
        #error_page   500 502 503 504  /50x.html;
        #location = /50x.html {
        #    root   html;
        #}
    }
}
```

1. 更改 host：在 C:\Windows\System32\drivers\etc 目录下的 host 文件中添加一条 DNS 记录：
   `127.0.0.1 www.helloworld.com`
2. 启动 webapp（应用），注意启动绑定的端口要和 nginx 中的 `upstream` 设置的端口保持一致。
3. 浏览器打开配置的链接：`www.helloworld.com`，访问成功即可。

### 3.2 负载均衡配置



## 四、总结

Nginx 作为轻量级、高并发、高性能的web服务器，目前使用非常广泛。一般建议 Nginx 部署在 Linux 服务器上，以发挥其高并发、高性能的特点。