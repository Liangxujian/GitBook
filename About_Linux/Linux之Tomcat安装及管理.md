# Linux 之 Tomcat 安装及配置

> 本篇笔记部分参考自博客：[Linux服务器启动/停止Tomcat命令方法](http://www.mabiji.com/tomcat/startup-sh.html) 

*PS：使用 Tomcat 需先有 jdk 环境*

## 一、解压

把 Linux 类型的 jdk 安装包（jdk-8u144-linux-x64.tar.gz）通过 SecureFX 拉到 /usr/local 路径下的 software 文件夹（个人习惯）下解压

````bash
tar zxvf apache-tomcat-8.0.21.tar.gz
````

## 二、修改 Tomcat 服务器启动端口

这里修改为 9999

````bash
vim /usr/local/software/apache-tomcat-8.0.21/conf/server.xml
````

![1562746488704](assets/1562746488704.png)

esc，:+wq 保存退出

## 三、管理 Tomcat 服务器

进入 bin 文件夹
开启服务：

* 方法一：直接启动

  执行命令：`./startup.sh`

* 方法二：作为服务启动

  使用该命令启动 Tomcat 后，Tomcat 可以作为 Linux 的服务一直运行

  执行命令：`nohup ./startup.sh &`
  （其中，nohup 表示不挂断，即关闭终端，脚本依然运行，&表示以后台方式运行）

* 方法三：控制台动态输出方式启动

  执行命令：`./catalina.sh run`

  执行命令后，能够动态显示 Tomcat 后台的控制台输出信息，使用快捷键`Ctrl+C`可以停止并关闭服务

注意：

1. 方式一和三启动的 Tomcat 当客户端连接断开的时候，Tomcat 也会立即停止服务；而方式二不受影响。
2. 方式一和二启动的 Tomcat，其日志会写到相应的日志文件（catalina.out）中，不能动态地查看 Tomcat 控制台信息，需执行命令：`cat catalina.out`进行查看；
   方法三可以动态查看 Tomcat 控制台动态信息（包括`log4j`和`System.out.println()`等输出的信息）
3. 如果使用了方法一启动了 Tomcat，可以切换到`\logs`文件夹中，使用命令`tail -f -n 100 catalina.out`实时查看日志后 100 行信息

## 四、Tomcat 停止/关闭命令

切换到 Tomcat 的 bin 目录：`cd /usr/local/tomcat/bin`
执行停止命令：`./shutdown.sh`

## 五、检查 Tomcat 是否关闭

执行 Tomcat 的停止命令后，可以通过该命令`ps -ef|grep java`，来检查 Tomcat 是否已经关闭

![1562747435977](assets/1562747435977.png)

此时可看出 Tomcat 尚未关闭。
可使用命令：`kill -9 12810`（对应 Tomcat 运行所对应的端口），去除对应端口的程序
再次查看：`ps -ef|grep java`

![1562747478625](assets/1562747478625.png)

此时 Tomcat 即为关闭状态。

## 六、查看 Tomcat 启动日志

切换到 logs 文件夹

````bash
cat catalina.out # 查看启动日志（可用tab键进行提示）
# 或者
tail -f -n 100 catalina.out # 实时查看日志后100行信息
````

## 七、Tomcat 主页访问分析

主机通过：`http://Linux的IP:设置的端口号`，访问 Tomcat 服务器主页
访问不上的可能是端口占用或防火墙问题导致

### 7.1 端口占用

查看当前是否有进程占用了9999端口

````bash
lsof -i:9999(设置的Tomcat端口号)
````

根据显示的进程ID，查看具体信息

````bash
ps axu|grep 1383
````

![1562747840625](assets/1562747840625.png)

或者可以直接在 Linux 访问 Tomcat 主页，看服务器是否启动正常

````bash
curl http://localhost:设置的端口号
# 或者
curl http://Linux系统IP:设置的端口号
````

### 7.2 防火墙问题

#### 7.2.1 查看防火墙是否启动

````bash
[root@localhost ~]# systemctl status iptables.service
鈼[0m iptables.service - IPv4 firewall with iptables
   Loaded: loaded (/usr/lib/systemd/system/iptables.service; enabled; vendor preset: disabled)
   Active: active (exited) since Wed 2021-01-06 03:44:52 EST; 3 weeks 0 days ago
  Process: 12404 ExecStart=/usr/libexec/iptables/iptables.init start (code=exited, status=0/SUCCESS)
 Main PID: 12404 (code=exited, status=0/SUCCESS)
   Memory: 0B
   CGroup: /system.slice/iptables.service

Jan 06 03:44:52 localhost.localdomain systemd[1]: Starting IPv4 firewall with i....
Jan 06 03:44:52 localhost.localdomain iptables.init[12404]: iptables: Applying f...
Jan 06 03:44:52 localhost.localdomain systemd[1]: Started IPv4 firewall with ip....
Hint: Some lines were ellipsized, use -l to show in full.
# 其中存在 Active: inactive (dead) 即为关闭状态；Active: active (exited)即为开启状态
````

#### 7.2.2 修改防火墙配置

````bash
vim /etc/sysconfig/iptables
````

![1562748085850](assets/1562748085850.png)

添加不拦截 Tomcat 服务器端口

#### 7.2.3 刷新防火墙配置

````shell
[root@localhost ~]# source /etc/sysconfig/iptables
-bash: *filter: command not found
-bash: :INPUT: command not found
-bash: :FORWARD: command not found
-bash: :OUTPUT: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: -A: command not found
-bash: COMMIT: command not found
````

若刷新配置后端口仍被拦截，则需要关闭重新启动防火墙

#### 7.2.4 启动防火墙

````bash
[root@localhost ~]# systemctl start iptables.service
````

#### 7.2.5 关闭防火墙

````bash
[root@localhost ~]# systemctl stop iptables.service
````

## 八、指定 Tomcat 使用的 JDK 或 JRE 版本

进入 Tomcat 的 bin 目录，修改 catalina.sh 文件和 setclasspath.sh，在文件开头的空白处加上如下两句：

````bash
export JAVA_HOME=/usr/local/java/jdk1.6.0_18
export JRE_HOME=/usr/local/java/jdk1.6.0_18/jre
````

