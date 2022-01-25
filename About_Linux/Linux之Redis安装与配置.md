# Linux 之 Redis 安装与配置

## 一、Redis 安装

### 1.1 Windows 安装

https://www.runoob.com/redis/redis-install.html

### 1.2 Linux 安装

官方网站：https://redis.io/

官方下载：https://redis.io/download （根据需要下载不同版本）
（域名后缀 io 属于国家域名，是 britishindianoceanterritory，即英属印度洋领地）

**PS：**Redis 是 C 语言开发，安装 Redis 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，需要先安装 gcc

#### 1.2.1 安装 gcc

gcc 的安装很简单，首先要确保 root 登录，其次就是 Linux 要能连外网

````bash
yum -y install gcc automake autoconf libtool make
````

**注意：**若运行 yum 时出现：/var/run/yum.pid已被锁定，说明：PID为xxxx的另一个程序正在运行。这时需运行以下命令：

````bash
rm -f /var/run/yum.pid
````

#### 1.2.2 安装 Redis

````bash
# 命令1：
wget https://download.redis.io/releases/ redis-5.0.7.tar.gz
# 命令2：
tar zxvf redis-5.0.7.tar.gz
# 命令3：
cd redis-5.0.7
# 命令4：
make | make MALLOC=libc
````

![image-20201021145635834](assets\image-20201021145635834.png)

````bash
# 命令5：将编译后的文件安装到指定目录（必须在编译后的文件夹中操作，这里是：redis-5.0.7 文件夹）
make PREFIX=/usr/local/redis install
````

**注意：**PREFIX 必须大写，同时会自动为我们创建 redis 目录，并将结果安装到此目录

![image-20201021145913515](assets\image-20201021145913515.png)

````bash
# 命令6：查看安装
cd /usr/local/redis
# 命令7：查看bin目录，如图所示
````

![image-20201021150313274](assets\image-20201021150313274.png)

## 二、Redis 启动

### 2.1 启动 Redis 服务端

````bash
# 进入对应的安装目录
cd /usr/local/redis
# 启动Redis服务端
./bin/redis-server
# 以守护进程的方式启动Redis服务端（前提是在redis.conf文件中配置好）
./bin/redis-server ./redis.conf
````

![image-20201021151458522](assets\image-20201021151458522.png)

### 2.2 启动 Redis 客户端

````bash
# 进入对应的安装目录
cd /usr/local/redis
# 启动Redis客户端
./bin/redis-cli
# 若Redis修改了服务端口号
./bin/redis-cli -h IP地址 -p 端口
# 若Redis设置了登录密码
./bin/redis-cli -a password
# 或者
./bin/redis-cli
auth password
````

![image-20201021151552943](assets\image-20201021151552943.png)

````bash
# 退出客户端命令
Ctrl + c
# 检测是否服务端启动命令
ping
````

![image-20201021151730481](assets\image-20201021151730481.png)

## 三、Redis 配置

Redis 的配置文件位于 Redis 安装目录下，文件名为：redis.conf（Windows名为：redis.windows.conf）

### 3.1 配置 Redis

Redis 端口号或启动有默认配置，但一般我们都会通过手动配置完成

回到根目录找到解压文件中的 redis.conf

![image-20201021151927286](assets\image-20201021151927286.png)

````bash
# 将配置文件复制到安装文件的目录下
cp redis.conf /usr/local/redis
````

![image-20201021152001820](assets\image-20201021152001820.png)

### 3.2 redis.conf 配置文件解析

````bash
# ☆Redis默认不是以守护进程的方式运行，可以通过该配置项的修改，使用yes启用守护进程
daemonize no
# 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
pidfile /var/run/redis_6379.pid
# ☆指定Redis监听端口，默认端口为6379
port 6379
# ☆绑定允许访问的的主机地址，默认只能本地访问
bind 127.0.0.1
# 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
timeout 300
# 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为notice
loglevel notice
# 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
logfile stdout
# 设置数据库的数量，默认为16，可以使用SELECT <dbid>命令在连接上指定数据库id
databases 16
# ☆指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合（Redis默认配置文件中提供了三个条件）
save <seconds> <changes>
save 900 1      # 表示900秒（15分钟）内有1个更改
save 300 10     # 表示300秒（5分钟）内有10个更改
save 60 10000   # 表示60秒（1分钟）内有10000个更改
# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变得巨大
rdbcompression yes 
# ☆指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb
# 指定本地数据库存放目录（默认为当前目录，即bin同级目录下）
dir ./
# 设置当本机为slave服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
slaveof <masterip> <masterport>
# 当master服务设置了密码保护时，slave服务连接master的密码
masterauth <master-password>
# ☆设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
requirepass foobared
# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
maxclients 128
# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理后，仍然到达最大内存设置，将无法再进入写入操作，但仍然可以进行读取操作。Redis新的VM机制，会把Key存放内存，Value会存放在swap区（建议：为总内存的1/4到1/2）。
maxmemory <bytes>
# 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步地把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为Redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中，默认为no。
appendonly no
# 指定更新日志文件名，默认为：appendonly.aof
appendfilename appendonly.aof
# 指定更新日志条件，共有3个可选项：
no                     # 表示等操作系统进行数据缓存同步到磁盘（快）
always                 # 表示每次更新操作后，手动调用fsync()方法将数据写到磁盘（慢，安全）
everysec               # 表示每秒同步一次（折中，默认值）
appendfsync everysec
# 指定是否启用虚拟内存机制，默认值为no，简单地介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页，即冷数据swap到磁盘上，访问多的页面由磁盘自动传出到内存中。
vm-enabled no
# 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享。
vm-swap-file /tmp/redis.swap
# 将所有大于vm-max-memory的数据存入虚拟内存，无论vm-max-memory设置多小，所有索引数据都是内存存储的（Redis的索引数据就是keys），也就是说，当vm-max-memory设置为0的时候，其实是所有value都存在于磁盘。默认值为0。
vm-max-memory 0
# Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的数量大小来设定的，建议如果存储很多小对象，page大小最好设置为32或者64 bytes，如果存储很大的对象，则可以使用更大的page，如果不确定，就使用默认值。
vm-page-size 32
# 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bit map）是放在内存中的，在磁盘上每8个pages将消耗1 byte的内存。
vm-pages 134217728
# 设置访问swap文件的线程数，最好不要超过机器的核数，如果设置为0，那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4。
vm-max-threads 4
# 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启。
glueoutputbuf yes
# 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法。
hash-max-zipmap-entries 64
hash-max-zipmap-value 512
# 指定是否激活重置哈希，默认为开启。
activerehashing yes
# 制定包含其他的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件。
include /path/to/local.conf
# ☆Redis作为优秀的中间缓存件，时常会存储大量的数据，即使采取了集群部署来动态扩容，也应该即时的整理内存，维持系统性能（如果数据一直新增，内存很快就会占满）
# 在Redis中有两种解决方案：
# 一是为数据设置超时时间，设定内存空间，建议内存为1/4至1/2
# 二是采用LRU算法动态将不用的数据删除（内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据）
volatile-lru      # 加入键的时候如果过限，首先从设置了过期时间的键集合中驱逐最久没有使用的键
allkeys-lru       # 加入键的时候，如果过限，首先通过LRU算法驱逐最久没有使用的键
volatile-random   # 在已经设定了超时的数据中随机删除
allkeys-random    # 查询所有的key，之后随机删除
volatile-ttl      # 查询全部设定超时时间的数据，之后排序，将马上将要过期的数据进行删除操作
noeviction        # 如果设置为该属性，则不会进行删除操作，如果内存溢出则报错返回
volatile-lfu      # 从所有配置了过期时间的键中驱逐使用频率最少的键
allkeys-lfu       # 从所有键中驱逐使用频率最少的键
````

参考网址：[Redis的缓存淘汰策略LRU与LFU](https://www.jianshu.com/p/c8aeb3eee6bc)

### 3.3 Redis 默认配置修改

````bash
# 允许以守护进程启动Redis服务
daemonize yes
# 注释掉默认的绑定主机地址
bind 127.0.0.1
# 设置客户端连接密码
requirepass 密码
````

## 四、Redis 关闭

````bash
# 第一种关闭方式：（断电、非正常关闭，容易数据丢失）
ps -ef|grep -i redis  # 查询Redis服务端的PID
ps -ef|grep redis     # 查询Redis服务端的PID
kill -9 PID
# 第二种关闭方式：（正常关闭、数据保存）
./bin/redis-cli shutdown
# 或者通过客户端进行shutdown（如果Redis设置了密码，需要先在客户端通过密码登录，再进行shutdown关闭服务端）
````

![image-20201021154927647](assets\image-20201021154927647.png)

查看是否关闭成功：

![image-20201021155024089](assets\image-20201021155024089.png)