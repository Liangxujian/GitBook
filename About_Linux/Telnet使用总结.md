# Telnet 使用总结

本篇笔记参考自博客：[https://blog.csdn.net/tongxinhaonan/article/details/53305639](https://blog.csdn.net/tongxinhaonan/article/details/53305639)

## 一、什么是 Telnet

telnet 命令通常用来远程登录。telnet 程序是基于 telnet 协议的远程登录客户端程序。telnet 协议是 TCP/IP 协议族中的一员，是 Internet 远程登陆服务的标准协议和主要方式。它为用户提供了在本地计算机上完成远程主机工作的能力。在终端使用者的电脑上使用 telnet 程序，用它连接到服务器。终端使用者可以在 telnet 程序中输入命令，这些命令会在服务器上运行，就像直接在服务器的控制台上输入一样。可以在本地就能控制服务器。要开始一个 telnet 会话，必须输入用户名和密码来登录服务器。

## 二、Telnet 判断

在执行 telnet 时会提示命令不存在，此时输入命令`rpm -qa telnet-server`查看 telnet 版本等信息，若无输出内容，则表示没有安装。

## 三、Telnet 安装

### 在线安装

分别执行两个命令：`yum install telnet`和`yum install telnet-server`

* 检查 telnet 是否安装成功：`rpm -qa telnet`
* 检测 telnet-server 是否安装成功 ：`rpm -qa telnet-server`

### rpm 包安装

下载地址：
[http://vault.centos.org/](http://vault.centos.org/)
[http://vault.centos.org/6.3/os/x86_64/Packages/](http://vault.centos.org/6.3/os/x86_64/Packages/)*（6.3表示系统版本号）*

找到安装包：
telnet-server-0.17-48.el6.x86_64.rpm
telnet-0.17-48.el6.x86_64.rpm

把 rpm 包下载后添加到 Linux 系统中，然后执行命令：
`rpm -ivh telnet-0.17-48.el6.x86_64.rpm`
`rpm -ivh telnet-server-0.17-48.el6.x86_64.rpm`

在执行`rpm -ivh telnet-server-0.17-48.el6.x86_64.rpm`命令时 ，可能会遇到如下警告：

> warning: telnet-server-0.17-48.el6.x86_64.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
> error: Failed dependencies:
> xinetd is needed by telnet-server-1:0.17-48.el6.x86_64

由于 telnet 服务也是由 xinetd 守护的，所以解决方式就是先安装 xinetd 包（下载地址同上），下载后添加到 Linux 系统中，执行命令：`rpm -ivh xinetd-2.3.14-39.el6_4.x86_64.rpm`，安装成功后再安装 telnet-server。

最后要启动 telnet 服务，就必须先启动 xinetd：`service xinetd start`

至此安装完成。

## 四、Telnet 命令使用

查看远方服务器 ssh 端口是否开放：`telnet 192.168.200.131 8080`

![1564467176251](D:\GitBook\About_Linux\assets\1564467176251.png)