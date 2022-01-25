# Linux 之 MySQL 安装与配置

## 一、Linux(Centos 7) 安装 mysql-5.7.24

> 本篇笔记摘录自：[Linux下安装mysql-5.7.24](https://www.jianshu.com/p/276d59cbc529)

> 注1：**本文档讲解安装版本为 mysql-5.7.24，对于 5.7.24 之后的版本，不适用此说明文档，主要原因在于之后版本的 MySQL 配置文件的目录位置和结构有所改变，使用此说明可能会出现找不到配置文件或者配置后不生效的情况。**
>
> 注2：**安装过程中务必保证文件路径的前后统一，否则可能会导致不可预期的结果，推荐直接使用文中的命令进行操作。**

### 1.1 安装前准备

1、检查是否已经安装过mysql，执行命令

````bash
[root@localhost /]# rpm -qa | grep mysql
````

![image-20200723192934370](.\assets\23192934370.png)

从执行结果，可以看出我们已经安装了**mysql-libs-5.1.73-5.el6_6.x86_64**，执行删除命令

````bash
[root@localhost /]# rpm -e --nodeps mysql-libs-5.1.73-5.el6_6.x86_64
````

再次执行查询命令，查看是否删除

````bash
[root@localhost /]# rpm -qa | grep mysql
````

![image-20200723193109839](.\assets\23193109839.png)

2、查询所有 MySQL 对应的文件夹

````bash
[root@localhost /]# whereis mysql
mysql: /usr/bin/mysql /usr/include/mysql
[root@localhost lib]# find / -name mysql
/data/mysql
/data/mysql/mysql
````

删除相关目录或文件

````bash
[root@localhost /]#  rm -rf /usr/bin/mysql /usr/include/mysql /data/mysql /data/mysql/mysql 
````

验证是否删除完毕

````bash
[root@localhost /]# whereis mysql
mysql:
[root@localhost /]# find / -name mysql
[root@localhost /]# 
````

3、检查 MySQL 用户组和用户是否存在，如果没有，则创建

````bash
[root@localhost /]# cat /etc/group | grep mysql
[root@localhost /]# cat /etc/passwd |grep mysql
[root@localhost /]# groupadd mysql
[root@localhost /]# useradd -r -g mysql mysql
[root@localhost /]# 
````

4、从官网下载是用于 Linux 的 MySQL 安装包

下载命令：

````bash
[root@localhost /]#  wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
````

也可以直接到 [mysql官网](https://downloads.mysql.com/archives/community/) 选择对应版本进行下载。

![image-20200723193522507](.\assets\23193522507.png)

### 1.2 安装 MySQL

1、在执行 **wget** 命令的目录下或你的上传目录下找到Mysql安装包：**mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz**

执行解压命令：

````bash
[root@localhost /]#  tar xzvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
[root@localhost /]# ls
mysql-5.7.24-linux-glibc2.12-x86_64
mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
````

解压完成后，可以看到当前目录下多了一个解压文件，移动该文件到 **/usr/local/** 下，并将文件夹名称修改为 **mysql**。

> PS：如果 **/usr/local/** 下已经存在 **mysql**，请将已存在 mysql 文件修改为其他名称，否则后续步骤可能无法正确进行。

执行命令如下：

````bash
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/
[root@localhost /]# cd /usr/local/
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 mysql
````

如果 **/usr/local/** 下不存在 **mysql** 文件夹，直接执行如下命令，也可达到上述效果。

````bash
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
````

2、在 **/usr/local/mysql** 目录下创建 data 目录

````bash
[root@localhost /]# mkdir /usr/local/mysql/data
````

3、更改 mysql 目录下所有的目录及文件夹所属的用户组和用户，以及权限

````bash
[root@localhost /]# chown -R mysql:mysql /usr/local/mysql
[root@localhost /]# chmod -R 755 /usr/local/mysql
````

4、编译安装并初始化 mysql，**务必记住初始化输出日志末尾的密码（数据库管理员临时密码）**

````bash
[root@localhost /]# cd /usr/local/mysql/bin
[root@localhost bin]# ./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
````

> 补充说明：
>
> 第4步时，可能会出现错误：
>
> ![image-20200723194248187](.\assets\23194248187.png)
>
> 出现该问题首先检查该链接库文件有没有安装使用命令进行核查：
>
> ````bash
> [root@localhost bin]# rpm -qa|grep libaio   
> [root@localhost bin]# 
> ````
>
> 运行命令后发现系统中无该链接库文件
>
> ````bash
> [root@localhost bin]#  yum install  libaio-devel.x86_64
> ````
>
> 安装成功后，继续运行数据库的初始化命令，此时可能会出现如下错误：
>
> ![image-20200723194153021](.\assets\23194153021.png)
>
> 执行如下命令：
>
> ````bash
> [root@localhost bin]#  yum -y install numactl
> ````
>
> 执行无误之后，再重新执行第4步初始化命令，无误之后再进行第5步操作！

5、运行初始化命令成功后，输出日志如下：

![image-20200723194443802](.\assets\23194443802.png)

记录日志最末尾位置 **root@localhost:** 后的字符串，此字符串为 MySQL 管理员临时登录密码。

6、编辑配置文件 my.cnf，添加配置如下

````bash
[root@localhost bin]#  vi /etc/my.cnf

[mysqld]
datadir=/usr/local/mysql/data
port=3306
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
symbolic-links=0
max_connections=600
innodb_file_per_table=1
lower_case_table_names=1
````

7、测试启动 MySQL 服务器

````bash
[root@localhost /]# /usr/local/mysql/support-files/mysql.server start
````

显示如下结果，说明数据库安装并可以正常启动

![image-20200723194817173](.\assets\23194817173.png)

> **异常情况**
>
> 如果出现如下提示信息
>
> ````bash
> Starting MySQL... ERROR! The server quit without updating PID file
> ````
>
> 查看是否存在 mysql 和 mysqld 的服务，如果存在，则结束进程，再重新执行启动命令
>
> ````bash
> #查询服务
> ps -ef|grep mysql | grep -v grep
> ps -ef|grep mysqld | grep -v grep
> 
> #结束进程
> kill -9 PID
> 
> #启动服务
>  /usr/local/mysql/support-files/mysql.server start
> ````
>
> ![image-20200723195033594](.\assets\23195033594.png)

8、添加软连接，并重启 MySQL 服务

````bash
[root@localhost /]#  ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql 
[root@localhost /]#  ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
[root@localhost /]#  service mysql restart
````

9、登录 MySQL，修改密码(密码为步骤5生成的临时密码)

````bash
[root@localhost /]#  mysql -u root -p
Enter password:
````

![image-20200723195156592](.\assets\23195156592.png)

10、开放远程连接

````bash
mysql>use mysql;
msyql>update user set user.Host='%' where user.User='root';
mysql>flush privileges;
````

![image-20200723195251030](.\assets\23195251030.png)

11、设置开机自动启动

````bash
1、将服务文件拷贝到init.d下，并重命名为mysql
[root@localhost /]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
2、赋予可执行权限
[root@localhost /]# chmod +x /etc/init.d/mysqld
3、添加服务
[root@localhost /]# chkconfig --add mysqld
4、显示服务列表
[root@localhost /]# chkconfig --list
````

至此，mysql5.7.24 版本的数据库安装，已经完成。

## 二、Linux(Centos 7) 安装 mysql-8.0.19

> 本篇笔记摘录自：[Linux下安装mysql-8.0.19](https://www.jianshu.com/p/4587e9429702)

> 注1：**本文档讲解安装版本为mysql-8.0.19，对于其他版本，可能不适用此说明文档，请务必注意您的Mysql版本。**
>
> 注2：**安装过程中务必保证文件路径的前后统一，否则可能会导致不可预期的结果，推荐直接使用文中的命令进行操作。**

### 2.1 安装前准备

1、检查是否已经安装过 MySQL，执行命令

````bash
[root@localhost /]# rpm -qa | grep mysql
````

![image-20200723195759498](.\assets\23195759498.png)

从执行结果，可以看出我们已经安装了 **mysql-libs-5.1.73-5.el6_6.x86_64**，执行删除命令

````bash
[root@localhost /]# rpm -e --nodeps mysql-libs-5.1.73-5.el6_6.x86_64
````

再次执行查询命令，查看是否删除

````bash
[root@localhost /]# rpm -qa | grep mysql
````

![image-20200723195915002](.\assets\23195915002.png)

2、查询所有 MySQL 对应的文件夹

````bash
[root@localhost /]# whereis mysql
mysql: /usr/bin/mysql /usr/include/mysql
[root@localhost lib]# find / -name mysql
/data/mysql
/data/mysql/mysql
````

删除相关目录或文件

````bash
[root@localhost /]#  rm -rf /usr/bin/mysql /usr/include/mysql /data/mysql /data/mysql/mysql 
````

验证是否删除完毕

````bash
[root@localhost /]# whereis mysql
mysql:
[root@localhost /]# find / -name mysql
[root@localhost /]# 
````

3、检查 MySQL 用户组和用户是否存在，如果没有，则创建

````bash
[root@localhost /]# cat /etc/group | grep mysql
[root@localhost /]# cat /etc/passwd |grep mysql
[root@localhost /]# groupadd mysql
[root@localhost /]# useradd -r -g mysql mysql
[root@localhost /]# 
````

4、从官网下载是用于 Linux 的 MySQL 安装包

下载命令：

````bash
[root@localhost /]#  wget https://cdn.mysql.com/archives/mysql-8.0/mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz
````

也可以直接到 [mysql官网](https://downloads.mysql.com/archives/community/) 选择对应版本进行下载。

![image-20200723193522507](.\assets\23193522507.png)

### 2.2 安装 MySQL

1、在执行 **wget** 命令的目录下或你的上传目录下找到Mysql安装包：**mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz**
执行解压命令：

````bash
[root@localhost /]#  tar xvf mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz
[root@localhost /]# ls
mysql-8.0.19-linux-glibc2.12-x86_64
mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz
````

解压完成后，可以看到当前目录下多了一个解压文件，移动该文件到 **/usr/local/** 下，并将文件夹名称修改为 **mysql**。

> 如果 **/usr/local/** 下已经存在 **mysql**，请将已存在 mysql 文件修改为其他名称，否则后续步骤可能无法正确进行。
>
> 执行命令如下：
>
> ```bash
> [root@localhost /]# mv mysql-8.0.19-linux-glibc2.12-x86_64 /usr/local/
> [root@localhost /]# cd /usr/local/
> [root@localhost /]# mv mysql-8.0.19-linux-glibc2.12-x86_64 mysql
> ```
>
> 如果 **/usr/local/** 下不存在 **mysql** 文件夹，直接执行如下命令，也可达到上述效果。
>
> ````bash
> [root@localhost /]# mv mysql-8.0.19-linux-glibc2.12-x86_64 /usr/local/mysql
> ````

2、在 **/usr/local/mysql** 目录下创建 data 目录

````bash
[root@localhost /]# mkdir /usr/local/mysql/data
````

3、更改 mysql 目录下所有的目录及文件夹所属的用户组和用户，以及权限

````bash
[root@localhost /]# chown -R mysql:mysql /usr/local/mysql
[root@localhost /]# chmod -R 755 /usr/local/mysql
````

4、编译安装并初始化 mysql，**务必记住初始化输出日志末尾的密码（数据库管理员临时密码）**

````bash
[root@localhost /]# cd /usr/local/mysql/bin
[root@localhost bin]# ./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
````

> **补充说明1：**
>
> mysql8.0.14 以后版本，如果在my.cnf中配置 **lower-case-table-names=1** 以实现不区分表名大小写 ，启动数据库时将会报错，根据官方文档记录，只有在**初始化时配置才有效**，因此在初始化参数后添加参数 `--lower-case-table-names=1`
>
> **补充说明2：**
>
> 第4步时，可能会出现错误：
>
> ![image-20200723200732805](.\assets\23200732805.png)
>
> 出现该问题首先检查该链接库文件有没有安装使用命令进行核查
>
> ````bash
> [root@localhost bin]# rpm -qa|grep libaio   
> [root@localhost bin]# 
> ````
>
> 安装成功后，继续运行数据库的初始化命令，此时可能会出现如下错误：
>
> ![image-20200723200846892](.\assets\23200846892.png)
>
> 执行如下命令：
>
> ````bash
> [root@localhost bin]#  yum -y install numactl
> ````
>
> 执行无误之后，再重新执行第4步初始化命令，无误之后再进行第5步操作！

5、运行初始化命令成功后，输出日志如下：

![image-20200723200948638](.\assets\23200948638.png)

**记录日志最末尾位置  *root@localhost:* 后的字符串，此字符串为 MySQL 管理员临时登录密码。**

6、编辑配置文件 my.cnf，添加配置如下：

````bash
[root@localhost bin]#  vi /etc/my.cnf

[mysqld]
datadir=/usr/local/mysql/data
port=3306
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
symbolic-links=0
max_connections=600
innodb_file_per_table=1
log-error=/usr/local/mysql/data/error.log
````

7、测试启动 MySQL 服务器

````bash
[root@localhost /]# /usr/local/mysql/support-files/mysql.server start
````

显示如下结果，说明数据库安装并可以正常启动

![image-20200723201155334](.\assets\23201155334.png)

> **异常情况**
>
> 如果出现如下提示信息：
>
> ````bash
> ERROR! The server quit without updating PID file (/usr/local/mysql/data/localhost.localdomain.pid).
> ````
>
> ![image-20200723201324927](.\assets\23201324927.png)
>
> 查看错误日志：**/usr/local/mysql/data/error.log**
>
> ![image-20200723201419968](.\assets\23201419968.png)
>
> 查看 MySQL 官方文档，有记录：
>
> `lower_case_table_names can only be configured when initializing the server. Changing the lower_case_table_names setting after the server is initialized is prohibited.`
>
> **只有在初始化的时候设置 *lower_case_table_names=1* 才有效**
>
> 因此，我们此时需要删除 my.cnf 中的 lower_case_table_names=1 的配置。
> 删除后，再次执行执行启动mysql命令：
>
> ````bash
> /usr/local/mysql/support-files/mysql.server start
> ````
>
> ![image-20200723201626435](.\assets\23201626435.png)
>
> `如果有其他异常，请进入 /usr/local/mysql/data/error.log 错误日志文件进行查看！`

8、添加软连接，并重启 MySQL 服务

````bash
[root@localhost /]#  ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql 
[root@localhost /]#  ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
[root@localhost /]#  service mysql restart
````

![image-20200723201735940](.\assets\23201735940.png)

9、登录 MySQL，修改密码(密码为步骤5生成的临时密码)

````bash
[root@localhost /]#  mysql -u root -p
Enter password:
mysql>ALTER USER USER() IDENTIFIED BY 'yourpass';
mysql>flush privileges;
````

![image-20200723201834456](.\assets\23201834456.png)

10、开放远程连接

````bash
mysql>use mysql;
msyql>update user set user.Host='%' where user.User='root';
mysql>flush privileges;
````

![image-20200723201920778](.\assets\23201920778.png)

通过可视化连接工具，测试远程连接。

`注意远程连接时，需要开发远程服务器的连接端口，此处 MySQL 服务开启端口为 3306`

11、设置开机自动启动

````bash
1、将服务文件拷贝到init.d下，并重命名为mysql
[root@localhost /]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
2、赋予可执行权限
[root@localhost /]# chmod +x /etc/init.d/mysqld
3、添加服务
[root@localhost /]# chkconfig --add mysqld
4、显示服务列表
[root@localhost /]# chkconfig --list
````

![image-20200723202114376](.\assets\23202114376.png)

至此，Mysql-8.0.19 版本的安装，已经全部完成。