# Linux 之 Redis 命令与操作

（Redis 的命令操作需先登录客户端）

## 一、公共

### 1.1 数据库操作

```sql
-- 查看数据库信息
info [section]
-- 切换所在的数据库（由 Redis 配置可知默认有16个数据库（0-15））
select index
-- 切换指定 key 所在的数据库
move key db
-- 查看数据库的 key 数量
dbsize
-- 打印命令
echo message
-- 实时存储收到的请求，返回相关的配置（* 表示获取所有配置）
config get *
-- 清空当前数据库
flushdb [async]
-- 清空所有数据库
flushall [async]
```

### 1.2 key 操作

```sql
-- 查看所有符合给定模式（pattern）的 key（有 * 和 ? 两种符号，* 表示模糊匹配字符串，如 na*，? 表示模糊匹配数字）
keys pattern
-- 删除值
del key [key ...]
-- 查看其序列化的值
dump key
-- 查看指定 key 的类型
type key
-- 查看指定 key 是否存在
exists key [key ...]
-- 查看指定 key 的过期时间（单位为秒）（默认 key 永久，返回 -1；返回 -2 表示为无效 key）
ttl key
-- 查看指定 key 的过期时间（单位为毫秒）
pttl key
-- 设置指定 key 的过期时间（单位为秒）
expire key seconds
-- 设置指定 key 的过期时间（单位为毫秒）
pexpire key milliseconds
-- 取消指定 key 的过期时间（移除后key将永久保存）
persist key
-- 随机返回一个 key
randomkey
-- 重命名一个 key
rename key newkey
```

## 二、String 操作

String 操作，Redis 中的 String 在内存中按照一个 name 对应一个 value 来存储。

<img src="assets\20201021113706.png" alt="20201021113706" style="zoom: 33%;" />

```sql
-- 赋值
-- ex：过期时间（秒）
-- px：过期时间（毫秒）
-- nx：如果设置为 true，则只有 name 不存在时，当前 set 操作才执行
-- xx：如果设置为 true，则只有 name 存在时，当前 set 操作才执行
set key value [expiration EX seconds|PX millinseconds] [NX|XX]
-- SET if Not eXists，在指定的 key 不存在时，为 key 设置指定的值
setnx key value
-- 为指定的 key 设置值及其过期时间。如果 key 已经存在，setex 命令将会替换旧的值
setex key seconds value
-- 同时设置一个或多个 key-value 对
mset key value [key value ...]

-- 取值
get key
-- 获取存储在指定 key 中字符串的子字符串。字符串的截取范围：start ≤ value 下标 ≤ end
getrange key start end
-- 设置指定 key 的值，并返回 key 的旧值
getset key value
-- 返回一个或多个给定 key 的值
mget key [key ...]

-- 自增、自减（可以利用 Redis 的自增、自减特性，作为一个计数器，应用于投票等业务场景中）
-- 如果 value 中存储为数字，则自增1；若为其他类型，则返回一个 error（例：incr num 10（key 为 num 的 value 自增10））
incr key
-- 将 key 中存储的数字自减1（例：decr num 15（key 为 num 的 value 自减15））
decr key
```

## 三、Hash 操作

Hash 类型常用于存储对象，它是 String 类型的 field 和 value 的映射表，或者说是一个 String 集合。它特别适合用于存储对象，相比较而言，将一个对象类型存储在 Hash 类型要比存储在 String 类型里占用更少的内存空间，并方便存取整个对象。

<img src="assets\20201021113707.png" alt="20201021113707" style="zoom:33%;" />

```sql
-- 赋值（name 对应的 hash 中设置一个键值对（不存在，则创建；否则，修改））
-- name：redis 的 name
-- field：name 对应的 hash 中的 key
-- value：name 对应的 hash 中的 value
hset key field value
-- 批量存储多个键值对
hmset key field value [field value ...]
hmset myhash name tom tel 10086 addr beijing

-- 取值
hget key field
-- 批量获取多个键值对
hmget key field [field ...]

-- 返回 hash 里所有的 key
hkeys key
-- 返回 hash 里所有的 value
hvals key
-- 返回 hash 里所有的 key 和 value
hgetall key

-- 自增、自减（increment 为空默认自增、自减1）
hincrby key field increment
hdecrby key field increment

-- 删除指定 hash 的field
hdel key field [field ...]

-- 检查键值是否存在（不存在返回0，存在返回1）
hexists key field
-- 返回 hash 集合中的所有键的数量
hlen key
```

**为什么不用 String 存储一个对象？**

1. 如果使用 String 类型存储整个对象，例如使用用户的 id 作为 key，用户对象作为 value，那么每次取值的时候都需要把整个用户对象取出来进行解析，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入 CAS 等复杂问题。
2. 如果使用 String 类型存储对象的每个属性，例如使用用户 id + 对应属性名称作为唯一的 key，虽然省去了序列化开销和并发问题，但是用户 id 为重复存储，如果存在大量这样的数据，意味着大量的内存浪费。

## 四、List 操作

List 操作，Redis 中的 List 在在内存中按照一个 name 对应一个 List 来存储（这个 List 类似 Java 中的链表 LinkedList，允许将数据插入到任意位置）。

<img src="assets\20201021113708.png" alt="20201021113708" style="zoom:33%;" />

```sql
-- 赋值（左插入）（例：r.lpush('oo'，11,22,33)；保存顺序为：33，22，11）
lpush key value [value ...]
-- 赋值（右插入）（例：r.rpush('oo'，11,22,33)；保存顺序为：11，22，33）
rpush key value [value ...]
-- 插入元素（将值 value 插入到列表 key 当中，位于值 pivot 之前或之后）
linsert key before|after pivot value

-- 获取列表指定范围内的元素（start：0、stop：-1：表示获取列表的所有元素）
lrange key start stop
-- 返回列表中下标为指定索引值的元素（如果指定索引值不在列表的区间范围内，返回 nil ）
lindex key index
-- 获取列表长度
llen key

-- 移除并获取列表的第一个元素
lpop key
-- 移除并获取列表的最后一个元素
rpop key
-- 根据参数 COUNT 的值，移除列表中与参数 VALUE 相等的元素
-- count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT
-- count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值
-- count = 0 : 移除表中所有与 VALUE 相等的值
lrem key count value
```

## 五、Set 操作

Set 操作，Set 集合就是不允许重复的列表。

## 六、ZSet 操作

有序集合（Sort Set），在集合的基础上，为每元素排序；元素的排序需要根据另外一个值来进行比较，所以，对于有序集合，每一个元素有两个值，即：值和分数，分数专门用来做排序

## 七、管道

### 7.1 pipeline 出现的背景

Redis 客户端执行一条命令分4个过程：发送命令 → 命令排队 → 命令执行 → 返回结果

这个过程称为 Round trip time（简称 RTT，往返时间），Redis 的原生批命令（mset，mget）有效节约了 RTT，但大部分命令（如 hgetall，并没有 mhgetall）不支持批量操作，需要消耗 N 次 RTT ，这个时候需要 pipeline 来解决这个问题。

### 7.2 什么是 pipeline

pipeline 模式则是将执行的命令写入到缓冲中，最后由 exec 命令一次性发送给 Redis 执行返回。

<img src="assets\20201021113718.jpg" alt="20201021113718" style="zoom: 67%;" />

### 7.3 pipeline 的性能

逐条写模式（常规模式）

```php
<?php
$redisObj = new \Redis();
$redisObj->connect('127.0.0.1', 6379);

$time_start = microtime(true);
$mcKey      = "lpush:normal";
for ($i = 0; $i < 20000; $i++)
{
    $redisObj->lPush($mcKey, $i);
}

$time_end = microtime(true);
$time     = $time_end - $time_start;
echo "逐条写模式耗时: {$time}\n";
```

pipeline （管道）模式

```php
<?php
$redisObj = new \Redis();
$redisObj->connect('127.0.0.1', 6379);

$time_start = microtime(true);
$redisObj->multi(Redis::PIPELINE);
unset($time, $time_end, $mcKey);
$mcKey = "lpush:pipeline";
for ($i = 0; $i < 20000; $i++)
{
    $redisObj->lPush($mcKey, $i);
}
$redisObj->exec();

$time_end = microtime(true);
$time     = $time_end - $time_start;
echo "pipeline 模式运行耗时：{$time}\n";
```

执行结果

```bash
#php t.php
逐条写模式耗时: 1.0443658828735
pipeline 模式运行耗时：0.045078039169312
```

根据结果可以看出，`pipeline` 模式的耗时比 `逐条写模式` 提升了20倍。

### 7.4 原生批命令与 pipeline 对比

1. 原生批命令（mset，mget）是原子性，pipeline 是非原子性
   (原子性概念：一个事务是一个不可分割的最小工作单位,要么都成功要么都失败。原子操作是指你的一个业务逻辑必须是不可拆分的。处理一件事情要么都成功，要么都失败，原子不可拆分)
2. 原生批命令是一个命令对应多个 key，pipeline 支持多命令（存在事务）
3. 原生批命令是 Redis 服务端支持实现的，而 pipeline 需要 Redis 服务端与客户端共同完成

### 7.5 pipeline （管道）的优点

pipeline 通过打包命令，一次性执行，可以节省 `连接->发送命令->返回结果` 所产生的往返时间，减少的 I/O 的调用次数。

### 7.6 pipeline （管道）的缺点

1. pipeline 每批打包的命令不能过多，因为 pipeline 方式打包命令再发送，那么 Redis 必须在处理完所有命令前先缓存起所有命令的处理结果。这样就有一个内存的消耗。
2. pipeline 是责任链模式，这个模式的缺点是，每次它对于一个输入都必须从链头开始遍历（参考 Http Server 处理请求就能明白），这确实存在一定的性能损耗。
3. pipeline 不保证原子性，如果要求原子性的，不推荐使用 pipeline。

### 7.7 pipeline （管道）小结

* pipeline 对命令数量是否有限制？
  没有限制，但是打包的命令不能过多，对内存的消耗就越大。

* pipeline 打包执行多少命令合适？
  查询 Redis 官方文档，根据官方的解释，推荐是以 `10k` 每批（**注意：**这个是一个参考值，请根据自身实际业务情况调整）。

> IMPORTANT NOTE: While the client sends commands using pipelining, the server will be forced to queue the replies, using memory. So if you need to send a lot of commands with pipelining, it is better to send them as batches having a reasonable number, for instance 10k commands, read the replies, and then send another 10k commands again, and so forth. The speed will be nearly the same, but the additional memory used will be at max the amount needed to queue the replies for this 10k commands. https://redis.io/topics/pipelining

* pipeline 批量执行的时候，是否对 Redis 进行了锁定，导致其他应用无法再进行读写？
  Redis 采用多路 I/O 复用模型，非阻塞 IO，所以 pipeline 批量写入的时候，一定范围内不影响其它的读操作。

## 八、Redis 事务

> Redis 事务可以一次执行多个命令（1、按顺序串行化执行；2、不会被其他命令加塞）

### 8.1 Redis 事务没有隔离级别的概念

批量操作在发送 EXEC 命令前被放入队列缓存，并不会被实际执行，也就不存在事务内的查询要看到事务里的更新，事务外查询不能看到。

### 8.2 Redis 事务不保证原子性

Redis 中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。

````bash
WATCH key [key ...] # 监视一或多个key，如果在事务执行之前，被监视的key被其他命令改动，则事务被打断（类似乐观锁）
UNWATCH # 取消watch对所有key的监控
MULTI # 标记一个事务块的开始（queued）
EXEC # 执行所有事务块的命令（一旦执行exec后，之前加的监控锁都会被取消掉）
DISCARD # 取消事务，放弃事务块中的所有命令
````

### 8.3 Redis 事务案例

#### 8.3.1 正常执行

![Redis事务正常执行](assets\1659331-20190416204151947-1999193750.png)

#### 8.3.2 放弃事务

![Redis事务放弃事务](assets\1659331-20190416204558119-2028373874.png)

#### 8.3.3 命令性错误

若在事务队列中存在命令性错误（类似于 Java 编译性错误），则执行 EXEC 命令时，所有命令都不会执行

![Redis事务命令性错误](assets\1659331-20190416205137740-1887538258.png)

#### 8.3.4 语法性错误

若在事务队列中存在语法性错误（类似于 Java 的1/0的运行时异常），则执行 EXEC 命令时，其他正确命令会被执行，错误命令抛出异常

![Redis事务语法性错误](assets\1659331-20190416205714294-77806844.png)

#### 8.3.5 使用 WATCH

>一但执行 EXEC 开启事务的执行后，无论事务使用执行成功， WATCH 对变量的监控都将被取消。
>故当事务执行失败后，需重新执行 WATCH 命令对变量进行监控，并开启新的事务进行操作。
>
>WATCH 指令类似于乐观锁，在事务提交时，如果 WATCH 监控的多个 KEY 中任何 KEY 的值已经被其他客户端更改，则使用 EXEC 执行事务时，事务队列将不会被执行，同时返回 Nullmulti-bulk 应答以通知调用者事务执行失败。

1. 使用 WATCH 检测 balance，事务期间 balance 数据未变动，事务执行成功
   ![Redis事务使用WATCH1](assets\1659331-20190416210530600-1167641209.png)
2. 使用 WATCH 检测 balance，在开启事务后（标注1处），在新窗口执行标注2中的操作，更改 balance 的值，模拟其他客户端在事务执行期间更改 WATCH 监控的数据，然后再执行标注1后命令，执行 EXEC 后，事务未成功执行
   <img src="assets\1659331-20190416211144923-1469436233.png" alt="Redis事务使用WATCH2" style="zoom:85%;" /><img src="assets\1659331-20190416211149567-1618751187.png" alt="Redis事务使用WATCH3" style="zoom:90%;" />

## 九、Docker 安装 Redis

```shell
# 1、下载镜像
[root@localhost ~]# docker pull redis:5.0
5.0: Pulling from library/redis
a076a628af6f: Pull complete 
f40dd07fe7be: Pull complete 
ce21c8a3dbee: Pull complete 
c2386bee3417: Pull complete 
46e9872918d1: Pull complete 
92b0c74830d4: Pull complete 
Digest: sha256:49255a58c9f6611e5b9e90f14f3ff8961f0801871b76194058b4a6cd98e75c4e
Status: Downloaded newer image for redis:5.0
docker.io/library/redis:5.0
# 2、启动容器
[root@localhost ~]# docker run --name redis-6379 -d -it -p 9999:6379 redis:5.0
36f38220aa8f415d6eeba633eb9309c4ceb148f88431fb7a4e33b81f2f0f44a3
# 3、查看启动情况
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                    NAMES
36f38220aa8f   redis:5.0   "docker-entrypoint.s鈥   4 seconds ago   Up 3 seconds   0.0.0.0:9999->6379/tcp   redis-6379
# 4、进入redis客户端并测试
[root@localhost ~]# docker exec -it redis-6379 redis-cli
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> set aa 123
OK
127.0.0.1:6379> get aa
"123"
127.0.0.1:6379> keys *
1) "aa"

# 注意：这里如果删除容器的话，Redis中缓存的数据都会被删掉，因此需要做数据卷的映射
# 1、新建缓存文件夹，复制redis配置文件
[root@localhost redis]# pwd
/usr/local/docker/redis
[root@localhost redis]# ll
total 64
drwxr-xr-x. 2 root root     6 Jan 28 03:58 data
-rw-r--r--. 1 root root 61818 Jan 28 03:57 redis.conf
# 2、修改配置文件（三处：不以守护进程方式运行；注释只允许本地访问；增加连接密码）
[root@localhost redis]# vim redis.conf
# ☆Redis默认不是以守护进程的方式运行，可以通过该配置项的修改，使用yes启用守护进程
daemonize no
# ☆绑定允许访问的的主机地址，默认只能本地访问
bind 127.0.0.1
# ☆设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
requirepass admin
# 3、使用数据卷映射的方式，启动Redis容器
[root@localhost redis]# docker run --name redis-6379 -d -it -p 9999:6379 redis:5.0 -v /usr/local/docker/redis/redis.conf:/usr/local/etc/redis/redis.conf -v /usr/local/docker/redis/data:/data redis-server /usr/local/etc/redis/redis.conf
# 这里不知为何启动后Redis自动关闭，docker ps看不到容器运行，未完待续。。。
# 之后启动后set key，然后删除容器，接着用数据卷映射启动一个新容器，get key看是否存在
```

