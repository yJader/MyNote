





# 一. NoSQL数据库

## 1.1 为什么要使用NoSQL

- 解决***CPU***及内存压力

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211021213413082.png" style="zoom: 67%;" /> 

  - 负载均衡 : nginx反向代理
  - 但是session不经过处理的话, 只能保存在服务器集群中的一台服务器上, 需要改进session存储方式

- 解决***IO***压力

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211021213423883.png" style="zoom: 67%;" /> 

## 1.2 NoSQL概述

1. ***NoSQL（ NoSQL = Not Only SQL ）***，意即不仅仅是***SQL***，泛指非关系型的数据库。 

2. ***NoSQL***不依赖业务逻辑方式存储，而以简单的***key-value***模式存储。因此大大的增加了数据库的扩展能力。

   - 不遵循***SQL***标准。

   - 不支持***ACID***。

   - 远超于***SQL***的性能。


3. **适用于的场景**

   - 对数据高并发的读写；

   - 海量数据的读写；

   - 对数据高可扩展性的。


4. **不适用的场景**

   - 需要事务支持；

   - 基于***sql***的结构化查询存储，处理复杂的关系，需要即时查询。


5. 常见的***NoSQL***数据库

   - Redis

   - MongoDB




## 1.3 大数据时代常用的数据库类型

- 行式数据库

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211021215032857.png" style="zoom:50%;" /> 

- 列式数据库

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211021215041246.png" style="zoom:50%;" /> 

# 二. Redis概述&安装

- ***Redis***是典型的***NoSQL***数据库。
- ***redis官网***：https://redis.io/download 

- ***Redis***是一个开源的***key-value***存储系统。
  - 和***Memcached***类似，它支持存储的***value***类型相对更多，包括***string、list、set、zset、sorted set、hash***。

  - 这些数据类型都支持***push/pop、add/remove***及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。

- 在此基础上，***Redis***支持各种不同方式的排序。

  - 与***memcached***一样，为了保证效率，数据都是缓存在内存中。

  - 区别的是***Redis***会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。

- 并且在此基础上实现了***master-slave***（主从）同步。

- 单线程 +***IO***多路复用。
  - Redis 是一个单线程的内存数据库，这意味着 Redis 可以在单个 CPU 核心上运行，而无需多线程并发。Redis 之所以能够高效地处理大量的请求，是因为它使用了 IO 多路复用技术。
  - IO 多路复用是一种操作系统级别的技术，它可以让单个线程同时监听多个 IO 事件，如网络连接、文件读写等。在 Redis 中，使用了 IO 多路复用技术，使得 Redis 在等待客户端请求时不会被阻塞，能够高效地处理大量的并发请求。
  - Redis 的事件驱动模型可以分为三个阶段：监听、分派和执行。在监听阶段，Redis 会使用 IO 多路复用技术同时监听多个客户端连接；在分派阶段，Redis 会将客户端请求分派到对应的处理函数中；在执行阶段，Redis 会执行对应的处理函数，对请求进行处理并返回响应。
  - 总的来说，Redis 的单线程和 IO 多路复用技术相结合，使得 Redis 能够高效地处理大量的请求。它的单线程架构减少了上下文切换和线程同步等开销，而 IO 多路复用技术则提高了 Redis 的并发能力。
  - 来自chatgpt

## 安装和启动

安装***C***语言的编译环境

```base
yum install centos-release-scl scl-utils-build
yum install -y devtoolset-8-toolchain
scl enable devtoolset-8 bash
```

通过***wget***下载

```
wget https://download.redis.io/releases/redis-6.2.6.tar.gz

// 下载路径：/opt
```

解压至当前目录

```
tar -zxvf redis-6.2.6.tar.gz 
```

解压完成后进入目录

```
cd redis-6.2.6
```

在当前目录下执行***make***

```
make && make install
```

默认安装在 `/usr/local/bin`

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/%E6%88%AA%E5%B1%8F2021-10-21%2022.16.18.png" style="zoom:50%;" />

```
redis-benchmark：性能测试工具，可以在自己本子运行，看看自己本子性能如何
redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
redis-check-dump：修复有问题的dump.rdb文件
redis-sentinel：Redis集群使用
redis-server：Redis服务器启动命令
redis-cli：客户端，操作入口
```



前台启动：***/usr/local/bin***目录下启动***redis***

```bash
redis-server(前台启动)
```



后台启动：

- 安装***redis***的目录***/opt/redis-6.2.6***中将***redis.conf***复制到任意一个文件夹下

  ```
  cp redis.conf /etc/redis.conf
  // 将redis.conf复制到/etc/下
  ```

- 修改***/etc/redis.conf***配置文件

  ```bash
  vim redis.conf
  
  # daemonize no 修改为 daemonize yes
  ```

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/%E6%88%AA%E5%B1%8F2021-10-21%2022.55.41.png" style="zoom:50%;" />

- ***/usr/local/bin***目录下启动***redis***

  ```bash
  redis-server /etc/redis.conf
  ```



关闭***redis***

- ***kill***进程
- 命令***shutdown***



<u>**默认端口号：6379**</u>



# 三. 配置文件

***redis.conf***



## 3.1 ***Units***

> 单位，配置大小单位，开头定义了一些基本的度量单位，只支持***bytes***，不支持***bit***。
>
> 大小写不敏感。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/55d34c88aac26e341c62cb7c0ae073b9.png)

## 3.2 ***INCLUDES***

> 包含，多实例的情况可以把公用的配置文件提取出来。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/0c51f24f99a3534b739b8ba4c1a58f92.png)

## 3.3 ***NETWORK***

网络相关配置。

### 3.3.1 ***bind***

默认情况 `bind=127.0.0.1` 只能接受本机的访问请求。

不写的情况下，无限制接受任何***ip***地址的访问。

生产环境肯定要写你应用服务器的地址，服务器是需要远程访问的，*<u>所以需要将其注释掉</u>*。



<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/52774afc3ce01167d3cb28c30225c717.png" alt="img" style="zoom:67%;" />



### 3.3.2 ***protected-mode***

如果开启了***protected-mode***，那么在没有设定***bind ip***且没有设密码的情况下，***Redis***只允许接受本机的响应。

将本机访问保护模式设置***no***。

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2fb56752ae49687b08abf686f96892a3.png" alt="img" style="zoom:67%;" />



### 3.3.3 ***port***

端口号，默认***6379***。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/35b9cd7490362cc507397685d38afcad.png)



### 3.3.4 ***tcp-backlog***

设置***tcp***的***backlog***，***backlog***其实是一个连接队列，***backlog***队列总和 $=$ 未完成三次握手队列 $+$ 已经完成三次握手队列。

在高并发环境下你需要一个高***backlog***值来避免慢客户端连接问题。

注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/b601e249a09faface90e9c2ac950e011.png)

### 3.3.5 ***timeout***

一个空闲的客户端维持多少秒会关闭，0 表示关闭该功能。即永不关闭客户端。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/e9a5e502e1252b25232b7cebc4423b9d.png)



### 3.3.6 ***tcp-keepalive***

对访问客户端的一种心跳检测，每个***n***秒检测一次。

单位为秒，如果设置为 0，则不会进行***Keepalive***检测，建议设置成 60。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/03ae84912dc609f11f0a73cbbde9344b.png)



## 3.4 ***GENERAL***通用

### 3.4.1 ***daemonize***

是否为后台进程，设置为***yes***, 则为守护进程，后台启动。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/cabc2e566ec0cc6a728f67bc2827fab7.png)

### 3.4.2 ***pidfile***

存放***pid***文件的位置，每个实例会产生一个不同的***pid***文件。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/59da939330577b75259c39113b1ee62c.png)



### 3.4.3 ***loglevel***

指定日志记录级别，***Redis***总共支持四个级别：***debug、verbose、notice、warning***，默认为***notice***。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/1f2b549c09e64e9325b97064fac96f79.png)



### 3.4.4 ***logfile***

日志文件名称。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/cfbe54285f5900691bb4031441c01d22.png)



### 3.4.5 ***database***

设定库的数量 默认16，默认数据库为 0，可以使用 `SELECT <dbid>` 命令在连接上指定数据库***id***。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/93e8b94985ea1246f49db73ea447e9fb.png)



## 3.5 ***SECURITY***

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/fa1745a30a3c24de32408726b372c587.png)

访问密码的查看、设置和取消。

在命令中设置密码，只是临时的。重启***redis***服务器，密码就还原了。

永久设置，需要在配置文件中进行设置。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/056c60447ccc4600a0b47770175e7fa4.png)

## 3.6 ***LIMITS***

### 3.6.1 ***maxclients***

设置***redis***同时可以与多少个客户端进行连接。

默认情况下为***10000***个客户端。

如果达到了此限制，***redis***则会拒绝新的连接请求，并且向这些连接请求方发出***max number of clients reached***以作回应。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/7d24ff2e377b3fc766c4d27088164d39.png)

### 3.6.2 ***maxmemory***

建议**必须设置**，否则，将内存占满，造成服务器宕机。

设置***redis***可以使用的内存量。一旦到达内存使用上限，***redis***将会试图移除内部数据，移除规则可以通过***maxmemory-policy***来指定。

如果***redis***无法根据移除规则来移除内存中的数据，或者设置了不允许移除，那么***redis***则会针对那些需要申请内存的指令返回错误信息，比如***SET、LPUSH***等。

但是对于无内存申请的指令，仍然会正常响应，比如***GET***等。如果你的***redis***是主***redis***（ 说明你的***redis***有从***redis***），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/7d24ff2e377b3fc766c4d27088164d39.png)

### 3.6.3 ***maxmemory-policy***

***volatile-lru***：使用***LRU***(<font color='orange'>Least Recently Used</font>)算法移除***key***，只对设置了过期时间的键（最近最少使用）。

***allkeys-lru***：在所有集合***key***中，使用***LRU***算法移除***key***。

***volatile-random***：在过期集合中移除随机的***key***，只对设置了过期时间的键。

***allkeys-random***：在所有集合***key***中，移除随机的***key***。

***volatile-ttl***：移除那些***TTL***值最小的***key***，即那些最近要过期的***key***。

***noeviction***：不进行移除。针对写操作，只是返回错误信息。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/b64f492c82185cdb60b755c033fb56e7.png)

### 3.6.4 ***maxmemory-samples***

设置样本数量，***LRU***算法和最小***TTL***算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，***redis***默认会检查这么多个***key***并选择其中***LRU***的那个。

一般设置 3 到 7 的数字，数值越小样本越不准确，但性能消耗越小。

![img](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/133b03bc46b51141a475cbdb57405801.png)

# 四. 常用五大基本数据类型

## key操作

`keys *`：查看当前库所有***key*** 

`exists key`：判断某个***key***是否存在

`type key`：查看你的***key***是什么类型

`del key` ：删除指定的***key***数据

`unlink key`：根据***value***选择<font color='#66ccff'>非阻塞删除</font>，仅将***keys***从***keyspace***元数据中删除，真正的删除会在后续异步操作

`expire key 10` ：为给定的***key***设置过期时间

`ttl key`：查看还有多少秒过期，-1表示永不过期，-2表示已过期



`select`：命令切换数据库

`dbsize`：查看当前数据库的***key***的数量

`flushdb`：清空当前库

`flushall`：通杀全部库



## 字符串（String）

***String***类型是二进制安全的。意味着***Redis***的***string***可以包含任何数据。比如***jpg***图片或者序列化的对象。

***String***类型是***Redis***最基本的数据类型，一个***Redis***中字符串***value***最多可以是 512M。



`set <key><value>`：添加键值对

`get <key>`：查询对应键值

`append <key><value>`：将给定的***\<value>***追加到原值的末尾, 并返回总长度

`strlen <key>`：获得值的长度

`setnx <key><value>`：只有在***key***不存在时，设置***key***的值

- set if not exist



`incr <key>`：将***key***中储存的数字值增 1，只能对数字值操作，如果为空，新增值为 1（**具有原子性**）

- 若对非数字值使用 报错`(error) ERR value is not an integer or out of range` 

`decr <key>`：将***key***中储存的数字值减 1，只能对数字值操作，如果为空，新增值为 -1

`incrby/decrby <key><步长>`：将***key***中储存的数字值增减。自定义步长



`mset <key1><value1><key2><value2>` ：同时设置一个或多个***key-value***对 

`mget <key1><key2><key3>...`：同时获取一个或多个***value*** 

`msetnx <key1><value1><key2><value2>... `：同时设置一个或多个***key-value***对，当且仅当所有给定***key***都不存在	

`getrange <key><起始位置><结束位置>`：获得值的范围

`setrange <key><起始位置><value>`：用***\<value>***覆写***\<key>***所储存的字符串值

`setex <key><过期时间><value>`：设置键值的同时，设置过期时间，单位秒。

`getset <key><value>`：以新换旧，设置了新值同时获得旧值。



**原子性**

所谓 **原子** 操作是指不会被线程调度机制打断的操作；

这种操作一旦开始，就一直运行到结束，中间不会有任何***context switch***（切换到另一个线程）。

- 在单线程中， 能够在单条指令中完成的操作都可以认为是"原子操作"，因为中断只能发生于指令之间。

- 在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。

***Redis***单命令的原子性主要得益于***Redis***的单线程。



**数据结构**

内部结构实现上类似于***Java***的***ArrayList***，采用预分配冗余空间的方式来减少内存的频繁分配

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211022000751746.png" style="zoom:50%;" /> 



## 列表（List）

***Redis***列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211022121129201.png" style="zoom:50%;" />



`lpush/rpush <key> <value1> <value2> <value3> ....`： 从左边/右边插入一个或多个值。

```redis
lpush k1 v1 v2 v3
lrange k1 0 -1
输出：v3 v2 v1

rpush k1 v1 v2 v3
lrange k1 0 -1
输出：v1 v2 v3
```

`lpop/rpop <key>`：从左边/右边吐出一个值。值在键在，值光键亡。

`rpoplpush <key1> <key2>`：从***\<key1>***列表右边吐出一个值，插到***\<key2>***列表左边。

`lrange <key> <start> <stop>`：按照索引下标获得元素（从左到右）

- `lrange mylist 0 -1  0`：左边第一个，-1右边第一个，（0 -1表示获取所有）

`lindex <key><index>`：按照索引下标获得元素（从左到右）

`llen <key>`：获得列表长度  

`linsert <key> before/after <value><newvalue>`：在***\<value>***的前面/后面插入***\<newvalue>***插入值

`lrem <key><n><value>`：从左边删除***n***个***value***（从左到右）

`lset<key><index><value>`：将列表***key***下标为***index***的值替换成***value*** 



**数据结构**

***List***的数据结构为快速链表***quickList***。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是***ziplist***，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成***quickList***。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是***int***类型的数据，结构上还需要两个额外的指针***prev***和***next***。

***Redis***将链表和***ziplist***结合起来组成了***quicklist***。也就是将多个***ziplist***使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20211022122514593.png" style="zoom:50%;" /> 



## Set（集合）

***Set***对外提供的功能与***List***类似列表的功能，特殊之处在于***Set***是可以**<u>自动排重</u>**的，当需要存储一个列表数据，又不希望出现重复数据时，***Set***是一个很好的选择，并且***Set***提供了判断某个成员是否在一个***Set***集合内的重要接口，这个也是***List***所不能提供的。

***Redis***的***Set***是***String***类型的无序集合。它底层其实是一个***value***为***null***的***hash***表，所以添加，删除，查找的复杂度都是***O(1)***。

一个算法，随着数据的增加，执行时间的长短，如果是***O(1)***，数据增加，查找数据的时间不变。



`sadd <key><value1><value2> ..... `：将一个或多个***member***元素加入到集合***key***中，已经存在的***member***元素将被忽略

`smembers <key>`：取出该集合的所有值

`sismember <key><value>`：判断集合***\<key>***是否为含有该***\<value>***值，有返回1，没有返回0

`scard<key>`：返回该集合的元素个数

`srem <key><value1><value2> ....`：删除集合中的某个元素

`spop <key>`：随机从该集合中吐出一个值

`srandmember <key><n>`：随机从该集合中取出***n***个值，不会从集合中删除

`smove <source><destination>value`：把集合中一个值从一个集合移动到另一个集合

`sinter <key1><key2>`：返回两个集合的交集元素

`sunion <key1><key2>`：返回两个集合的并集元素

`sdiff <key1><key2>`：返回两个集合的差集元素（***key1***中的，不包含***key2***中的）



**数据结构**

***Set***数据结构是字典，字典是用哈希表实现的。



## Hash（哈希）

***Redis hash***是一个键值对集合

***Redis hash***是一个***String***类型的***field***和***value***的映射表，***hash***特别适合用于存储对象



`hset <key> <field> <value>`：给***\<key>***集合中的***\<field>***键赋值***\<value>*** 

`hget <key1> <field>`：从***\<key1>***集合***\<field>***取出***value*** 

`hmset <key1> <field1> <value1> <field2> <value2>...`： 批量设置***hash***的值

`hexists <key1><field>`：查看哈希表***key***中，给定域***field***是否存在

`hkeys <key>`：列出该***hash***集合的所有***field*** 

`hvals <key>`：列出该***hash***集合的所有***value*** 

`hincrby <key><field><increment>`：为哈希表***key***中的域***field***的值加上增量1 -1

`hsetnx <key><field><value>`：将哈希表***key***中的域***field***的值设置为***value***，当且仅当域***field***不存在



**数据结构**

***Hash***类型对应的数据结构是两种：***ziplist***（压缩列表），***hashtable***（哈希表）

当***field-value***长度较短且个数较少时，使用***ziplist***，否则使用***hashtable*** 



## Zset（有序集合）

***Redis***有序集合***zset***与普通集合***set***非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每个成员都关联了一个评分（***score***）,这个评分（***score***）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复的。

因为元素是有序的，所以可以很快的根据评分（***score***）或者次序（***position***）来获取一个范围的元素。

访问有序集合的中间元素也是非常快的，因此能够使用有序集合作为一个没有重复成员的智能列表。



`zadd <key><score1><value1><score2><value2>…`：将一个或多个***member***元素及其***score***值加入到有序集***key***当中

`zrange <key><start><stop> [WITHSCORES]  `：返回有序集***key***中，下标在***\<start>\<stop>***之间的元素

当带***WITHSCORES***，可以让分数一起和值返回到结果集

`zrangebyscore key min max [withscores] [limit offset count]`：返回有序集***key***中，所有***score***值介于***min***和***max***之间（包括等于***min***或***max***）的成员。有序集成员按***score***值递增（从小到大）次序排列。

`zrevrangebyscore key max min [withscores] [limit offset count] `：同上，改为从大到小排列

`zincrby <key><increment><value>`：为元素的***score***加上增量

`zrem <key><value>`：删除该集合下，指定值的元素

`zcount <key><min><max>`：统计该集合，分数区间内的元素个数 

`zrank <key><value>`：返回该值在集合中的排名，从 0 开始。



**数据结构**

***SortedSet（zset）***是***Redis***提供的一个非常特别的数据结构，一方面它等价于***Java***的数据结构***Map<String, Double>***，可以给每一个元素***value***赋予一个权重***score***，另一方面它又类似于***TreeSet***，内部的元素会按照权重***score***进行排序，可以得到每个元素的名次，还可以通过***score***的范围来获取元素的列表。

***zset***底层使用了两个数据结构

- ***hash***，***hash***的作用就是关联元素***value***和权重***score***，保障元素***value***的唯一性，可以通过元素***value***找到相应的***score***值

- 跳跃表，跳跃表的目的在于给元素***value***排序，根据***score***的范围获取元素列表



# Redis6新数据结构

$\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\space to \space do\space \#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#$



# 六. Redis的发布与订阅

***Redis***发布订阅（***pub/sub***）是一种消息通信模式：发送者（***pub*** publish）发送消息，订阅者（***sub*** subscribe）接收消息。

***Redis***客户端可以订阅任意数量的频道。

1. 客户端可以订阅频道 : ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210422215853726.png)

2. 当给这个频道发布消息后，消息就会发送给订阅的客户端 : ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210422215910700.png)



```sql
subscribe channel # 订阅频道

publish channel hello # 频道发送信息
```

## 发布订阅命令行实现

1. 打开一个客户端订阅channel1
   `SUBSCRIBE channel1`
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210422220044780.png)
2. 打开另一个客户端，给channel1发布消息hello
   `publish channel1 hello`
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210422220110970.png)
   返回的1是订阅者数量
3. 打开第一个客户端可以看到发送的消息
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210422220147450.png)
   注：发布的消息没有持久化，如果在订阅的客户端收不到hello，只能收到订阅后发布的消息

# 七. 事务和锁机制

## 7.1 Redis的事务定义

***Redis***事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

***Redis***事务的主要作用就是**串联多个命令**防止别的命令插队。

## 7.2 *Multi*、*Exec*、*Discard*

### 7.2.1 基本

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210425191931365.png)

```sql
Multi
Exec
Discard	
```

从输入***Multi***命令开始，输入的命令都会依次进入命令队列中，但不会执行

直到输入***Exec***后，***Redis***会将之前的命令队列中的命令依次执行。

组队的过程中可以通过***Discard***来放弃组队。 

- 组队成功，提交成功

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230327202122925.png" alt="image-20230327202122925" style="zoom:50%;" /> 

- 放弃组队

  ![image-20230327203206564](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230327203206564.png) 

- 组队中有命令错误，不会执行

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230327202222797.png" alt="image-20230327202222797" style="zoom:50%;" /> 

  当组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消。

  ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042520002448.png) 

- 组队中不报错，执行时报错

  ![image-20230327203336240](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230327203336240.png) 
  
  如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。
  
  ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210425200720110.png) 

## 7.3 事务冲突问题

### 7.3.1 例子

一个请求想给金额减8000
一个请求想给金额减5000
一个请求想给金额减1000

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210425200817386.png) 

### 7.3.2 悲观锁

悲观锁（***Pessimistic Lock***），即每次去拿数据的时候都认为有其他线程会修改，所以每次在拿数据的时候都会上锁，这样其他线程想要拿到这个数据就会被***block***直到成功拿到锁。（效率低）

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210425201015606.png)

### 7.3.3 乐观锁

乐观锁（***Optimistic Lock***），即每次去拿数据的时候都认为其他线程不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间有没有其他线程去更新这个数据，可以使用版本号等机制。

**乐观锁适用于多读的应用类型，这样可以提高吞吐量**。

***Redis***就是利用这种***check-and-set***机制实现事务的。

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210425201118493.png) 



### 7.3.4 *Watch、unwatch*——乐观锁在redis中的使用

`WATCH key [key]` 

在执行***multi***之前，先执行***watch key1 [key2]***(可以监视一个（或多个 ）***key***)。

如果在事务==执行==之前这个***key***被其他命令所改动，那么事务将被打断。

`UNWATCH`

取消***WATCH***命令对所有***key***的监视。如果在执行***WATCH***命令之后，***EXEC***命令或***DISCARD***命令先被执行，那么就不需要再执行***UNWATCH***。



### 7.3.5 事务三特性

- 单独的隔离操作 

  事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 

- 没有隔离级别的概念 

  队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行。

- 不保证原子性 

  事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 。



# 八. 持久化

Redis 提供了2个不同形式的持久化方式。

1. RDB（Redis DataBase）
2. AOF（Append Of File）

## 8.1 RDB(Redis DataBase)

1. RDB是什么 : 在指定的**时间间隔**内将内存中的数据集**快照**写入磁盘， 即***Snapshot***快照，恢复时是将快照文件直接读到内存里。

2. 备份操作的进行 : 

   ***Redis***会单独创建一个子进程（***fork***）来进行持久化。

   先将数据写入到一个临时文件中，待持久化过程完成后，再将这个临时文件内容覆盖到***dump.rdb***。 

   整个过程中，主进程是不进行任何***IO***操作的，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那***RDB***方式要比***AOF***方式更加的高效。

   *RDB* 的缺点是==最后一次持久化后的数据可能丢失==。

 

### 8.1.1 Fork

- 作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

- 在***Linux***程序中，***fork()***会产生一个和父进程完全相同的子进程，但子进程在此后多会***exec***系统调用，出于效率考虑，***Linux***中引入了 **写时复制技术**
  - <font color='#66ccff'>写时复制技术</font> : **父进程和子进程会共用同一段物理内存**，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程


### 8.1.2 RDB持久化流程

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426152939153.png)

### 8.1.3 配置

1. ***dump* 文件名字** : 在***redis.conf***中配置文件名称，默认为***dump.rdb***
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426153101274.png)

2. ***dump* 保存位置** : ***rdb***文件的保存路径可以修改。默认为***Redis***启动时命令行所在的目录下
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426153208334.png)

#### 如何配置RBD快照—保存策略

1. 配置文件中默认的快照配置
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426205545363.png)
2. 命令save VS bgsave
   - save ：save时只管保存，其它不管，全部阻塞。手动保存。不建议。
   - bgsave：Redis会在后台异步进行快照操作， 快照同时还可以响应客户端请求。
   - 可以通过lastsave 命令获取最后一次成功执行快照的时间
3. `flushshall`命令
   - 执行flushshall命令, 同样会产生dump.rdb文件, 但内部是空的, 毫无意义
4. save
   - 格式：`save [秒] [写操作次数]`
   - RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件
   - 默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。
   - 禁用 : 不设置save指令，或者给save传入空字符串

5. ***stop-writes-on-bgsave-error*** 
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042620580791.png)

   即当***redis***无法写入磁盘，关闭***redis***的写入操作。

6. ***rdbcompression*** 压缩文件
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426205900150.png)
   - 对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用**LZF算法**进行压缩。
   - 如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.

7. rdbchecksum 检查完整性
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042620595429.png)

   - 在存储快照后，还可以让redis使用CRC64算法来进行数据校验
   - 但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能
   - 推荐yes

8. rdb的备份

   - 先通过`config get dir `命令, 查询rdb文件的目录, 将`*.rdb`的文件拷贝到别的地方

   - rdb的恢复：

     - 关闭Redis

     - 先把备份的文件拷贝到工作目录下 cp dump2.rdb dump.rdb

     - 启动Redis, 备份数据会直接加载



### 8.1.4 RDB优缺点

1. 优点

   - 适合大规模的数据恢复；

   - 对数据完整性和一致性要求不高更适合使用；

   - 节省磁盘空间；

   - 恢复速度快。
   - ![image-20230327214641784](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230327214641784.png) 

2. 缺点

   - ***Fork***的时候，内存中的数据被克隆了一份，大致 2 倍的膨胀性需要考虑；

   - 虽然***Redis***在***fork***时使用了**写时拷贝技术**，但是如果数据庞大时还是比较消耗性能；

   - 在备份周期在一定间隔时间做一次备份，所以如果***Redis***意外***down***掉的话，就会丢失最后一次快照后的所有修改。


### 8.1.5 如何停止RDB

动态停止RDB : 

```sql
redis-cli config set save [] #save后给空值，表示禁用保存策略 
```

### 8.1.6 RDB总结

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426153509145.png)

## 8.2 AOF(Append Only File)

### 8.2.1 AOF是什么

**以日志的形式来记录每个写操作（增量保存）**，将***Redis***执行过的所有写指令记录下来（读操作不记录）， <u>只许追加文件但不可以改写文件</u> 

***Redis***启动之初会读取该文件重新构建数据，换言之，如果***Redis***重启就会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作



### 8.2.2 执行流程

1. 客户端的请求写命令会被***append***追加到***AOF***缓冲区内；

2. ***AOF***缓冲区根据***AOF***持久化策略 `[always,everysec,no]` 将操作***sync***同步到磁盘的***AOF***文件中；

3. ***AOF***文件大小超过重写策略或手动重写时，会对***AOF***文件***Rewrite***重写，压缩***AOF***文件容量；

4. ***Redis***服务重启时，会重新***load***加载***AOF***文件中的写操作达到数据恢复的目的。

   ![ ](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426154302204.png) 

***AOF***和***RDB***同时开启时，系统默认读取***AOF***的数据（数据不会存在丢失）

### 8.2.3 AOF的使用

1. ***AOF* 默认不开启** 
   - 可以在redis.conf中配置文件名称，默认为 **appendonly.aof** 
   - AOF文件的保存路径，同RDB的路径一致

2. AOF和RDB同时开启, redis听谁的
   - AOF和RDB同时开启，系统默认取AOF的数据（数据不会存在丢失）
3. AOF启动/修复/恢复
   - AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载。
   - 正常恢复
     1. 修改默认的appendonly no，改为yes
     2. 将有数据的aof文件复制一份保存到对应目录 (查看目录：config get dir)
     3. 恢复：重启redis然后重新加载
   - 异常恢复
     1. 修改默认的appendonly no，改为yes
     2. 如遇到AOF文件损坏，通~过/usr/local/bin/redis-check-aof–fix appendonly.aof进行恢复
     3. 备份被写坏的AOF文件
     4. 恢复：重启redis，然后重新加载

### 8.2.4 AOF同步频率设置

1. ***appendfsync always***
   - 始终同步，每次***Redis***的写入都会立刻记入日志；
   - 性能较差但数据完整性比较好。

2. ***appendfsync everysec***
   - 每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

3. ***appendfsync no***
   - ***Redis***不主动进行同步，把同步时机交给操作系统。



### 8.2.5 ***Rewrite* 压缩**

1. 什么是Rewrite压缩 : 当***AOF***文件的大小超过所设定的阈值时，***Redis***就会启动***AOF***文件的内容压缩，只保留可以恢复数据的最小指令集。
   - 可以使用命令***bgrewriteaof***手动使用。
2. 重写原理
   - AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，redis4.0版本后的重写，是指上就是把rdb的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。 
   - no-appendfsync-on-rewrite：
     - 如果 no-appendfsync-on-rewrite=yes,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能） 
     - 如果no-appendfsync-on-rewrite=no,还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低） 触发机制，何时重写
3. 重写时机
   - Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。
   - auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）
   - auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写。
     例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB
     系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size, 如果Redis的AOF当前大小>=
     base_size +base_size*100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。



### 8.2.6 AOF优缺点

1. 优点

   - 备份机制更稳健，丢失数据概率更低；

   - 可读的日志文本，通过操作***AOF***稳健，可以处理误操作。




2. 缺点

   - 比起***RDB***占用更多的磁盘空间；

   - 恢复备份速度要慢；

   - 每次读写都同步的话，有一定的性能压力；

   - 存在个别***Bug***，造成不能恢复。




## 二者的选择

> 官方推荐两个都启用。
>
> 如果对数据不敏感，可以选单独用***RDB***。
>
> 不建议单独用***AOF***，因为可能会出现***Bug***。
>
> 如果只是做纯内存缓存，可以都不用。



# 九. Redis主从复制

1. <font color='#66ccff'>主从复制</font> : 主机数据更新后根据配置和策略， 自动同步到备机的***master/slaver***机制，***Master***以写为主，***Slaver***以读为主。

2. 作用

   - 读写分离，性能扩展
   - 容灾快速恢复 : 当某一从服务器受损, 可以切换到另一个从服务器

   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426165108108.png)

3.  注意 : 一主多从, 只能有一台主服务器
   - 多个一主多从服务器构成一个服务器集群



## 测试—搭建一主两从

1. 创建文件目录
    ```bash
    /myredis
    ```

2. 将***redis.conf***配置文件复制到当前目录
    ```bash
    cp /etc/redis.conf /myredis/redis.conf
    ```

3. 创建 3 个***redis.conf***配置文件
    ```
    redis6379.conf 
    redis6380.conf
    redis6381.conf
    ```

    ```bash
    # redis6379.conf
    include /myredis/redis.conf # 引入公共配置
    pidfile /var/run/redis_6379.pid
    port 6379
    dbfilename dump6379.rdb
    
    # redis6380.conf
    include /myredis/redis.conf # 引入公共配置
    pidfile /var/run/redis_6380.pid
    port 6380
    dbfilename dump6380.rdb
    
    # redis6381.conf
    include /myredis/redis.conf # 引入公共配置
    pidfile /var/run/redis_6381.pid
    port 6381
    dbfilename dump6381.rdb
    ```

4. 启动 3 台***redis***服务器

   ```bash
   redis-server redis6379.conf
   redis-server redis6380.conf
   redis-server redis6381.conf
   ```

5. 查看主机运行情况

    ```
    info replication
    ```

    <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329204441406.png" alt="image-20230329204441406" style="zoom: 67%;" /><img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329204541857.png" alt="image-20230329204541857" style="zoom:67%;" /><img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329204614120.png" alt="image-20230329204614120" style="zoom:67%;" />

6. 配从不配主

   在从机上执行以下命令

   ```bash
   slaveof  <ip><port>
   # 成为某个实例的从服务器
   ```

   <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329204735614.png" alt="image-20230329204735614" style="zoom:67%;" /> 

7. 再次查看主机运行情况

   <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329204831318.png" alt="image-20230329204831318" style="zoom:67%;" /> 

成功搭建。



## 一主二从

主机***6379***，从机***6380***和***6381***。

1. 假设从机***6380***挂掉。
   - 当6380重启后，6380不再是6379的从机，而是作为新的master；
   - 当再次把6380作为6379的从机加入后，从机会把数据从头到尾复制。

2. 假设主机***6379***挂掉。
   - 6380和6381仍然是6379的从机，不会做任何事；
   - 当6379重启后，既然是主服务器。



## 薪火相传

上一个***slave***可以是下一个***slave***的***master***，***slave***同样可以接收其他***slave***的连接和同步请求，那么该***slave***作为了链条中下一个的***master***，可以有效减轻***master***的写压力，去中心化降低风险。(树状)

```bash
slaveof <ip><port>
```

- 中途变更转向：会清除之前的数据，重新建立拷贝最新的。
- 当某个***slave***宕机，后面的***slave***(子slave)都没法备份。即当主机挂掉，从机还是从机，但是无法继续写数据。



## 反客为主

当一个***master***宕机后，后面的***slave***可以立刻升为***master***，其后面的***slave***不用做任何修改。

```bash
slaveof no one # 让从机上位
```

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426181506400.png)

## 哨兵模式sentinel

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426181732246.png)

**反客为主的自动版**，即能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。

1. 创建***sentinel.conf***文件

```bash
/myredis/sentinel.conf
```

2. 配置哨兵

```bash
sentinel monitor mymaster 172.16.88.168 6379 1

# mymaster：监控对象起的服务器名称
# 1：至少需要多少个(1个)哨兵同意, 才能进行迁移
```

3. 启动哨兵

```bash
redis-sentinel  /myredis/sentinel.conf 
```

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230329212754626.png" alt="image-20230329212754626" style="zoom:67%;" />

主机挂掉，从 从机选举中产生新的主机。(根据选举规则)
并且原主机重启后, 会变为选举后的主机的从机

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426181808424.png) 

### 选举规则

- 根据优先级别，***slave-priority/replica-priority***，优先选择优先级靠前的。

- 根据偏移量，优先选择偏移量大的。
- 根据***runid***，优先选择最小的服务。



### 复制延时

由于所有的写操作都是先在***master***上操作，然后同步更新到***slave***上，所以从***master***同步到***slave***从机有一定的延迟

当系统很繁忙的时候，延迟问题会更加严重

此外, ***slave***机器数量的增加也会使这个问题更加严重。



## 复制原理

- ***slave***启动成功连接到***master***后会发送一个***sync***命令（同步命令）。

- ***master***接到命令启动后台的存盘进程，对数据进行持久化操作，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，***master***将传送整个数据文件（***rdb***）到***slave***，以完成一次完全同步。

- 当主服务进行写操作后，和从服务器进行数据同步。
- 全量复制：而***slave***服务在接收到数据库文件数据后，将其存盘并加载到内存中。

- 增量复制：***master***继续将新的所有收集到的修改命令依次传给***slave***，完成同步。

- 只要是重新连接***master***，一次完全同步（全量复制）将被自动执行。



# 十. 集群

> 容量不够，***redis***如何进行扩容？
>
> 并发写操作，***redis***如何分摊？
>
> 主从模式，薪火相传模式，主机宕机，导致***ip***地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息。

1. 解决方法：

   - 代理主机（***之前***）


   - <font color='#66ccff'>无中心化集群配置</font>（***redis3.0***）

2. 集群的特点

   - ***Redis***集群实现了对***Redis***的水平扩容，即启动***N***个***Redis***节点，将整个数据库分布存储在这***N***个节点中，每个节点存储总数据的***1/N***。
   - ***Redis***集群通过分区（***partition***）来提供一定程度的可用性（***availability***），即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。



## 10.1 搭建 *Redis* 集群

1. 配置文件

    ```bash
    # 以redis6379.conf为例
    include /myredis/redis.conf
    pidfile /var/run/redis_6379.pid # 更改
    port 6379 # 更改
    dbfilename dump6379.rdb # 更改
    cluster-enabled yes # 打开集群模式
    cluster-config-file nodes-6379.conf # 设置节点配置文件名称，需要更改
    cluster-node-timeout 15000 # 设置节点失联事件，超过该时间（ms），集群自动进行主从切换
    ```
    
    - 修改好redis6379.conf后, 拷贝多个redis.conf文件 ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427162617895.png) 
    - 在vim中使用查找替换修改另外5个文件 如 `%s/6379/6380`

2. 启动

   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427162713623.png) 

3. 将 6 个节点合成一个集群

   - 组合之前, 确保所有redis实例启动后，nodes-xxxx.conf文件都生成正常。

   - 组合命令

     ```bash
     # 进入redis安装目录
     /opt/redis-6.2.6/src
     
     # 执行 (实际使用时, IP不会全都相同, 即通常一台机器一个redis)
     redis-cli --cluster create --cluster-replicas 1 172.16.88.168:6379 172.16.88.168:6380 172.16.88.168:6381 172.16.88.168:6389 172.16.88.168:6390 172.16.88.168:6391
     ```

     <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330083914101.png" alt="image-20230330083914101" style="zoom: 67%;" /> <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330084151768.png" alt="image-20230330084151768" style="zoom:67%;" /> 

4. 采用集群策略连接

   - 可能直接进入读主机，存储数据时，会出现MOVED重定向操作。所以，应该以集群方式登录。

   - ```bash
     redis-cli -c -p PORT
     cluster nodes # 命令查看集群信息
     ```

     

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427163051611.png)



## 10.2 问题

### 10.2.1 *redis cluster* 如何分配这六个节点?

一个集群至少要有三个主节点。

选项 `--cluster-replicas 1`，表示希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的***IP***地址，每个从库和主库不在一个***IP***地址上。



### 10.2.2 什么是 *slots*？

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330084151768.png" alt="image-20230330084151768" style="zoom:67%;" />

一个***Redis***集群包含***16384***个插槽（***hash slot***）， 数据库中的每个键都属于这***16384***个插槽的其中一个。

集群使用公式 $CRC16(key) % 16384$ 来计算键***key***属于哪个槽， 其中***CRC16(key)***语句用于计算键***key***的***CRC16***校验和 。

集群中的每个节点负责处理一部分插槽。 例如， 如果一个集群可以有主节点， 其中：

- 节点***A***负责处理***0***号至***5460***号插槽。
- 节点***B***负责处理***5461***号至***10922***号插槽。
- 节点***C***负责处理***10923***号至***16383***号插槽。



### 10.2.3 如何在集群中录入值？

- 在***redis-cli***每次录入、查询键值，***redis***都会计算出该***key***应该送往的插槽，如果不是该客户端对应服务器的插槽，***redis***会报错，并告知应前往的***redis***实例地址和端口。

- ***redis-cli***客户端提供了***–c***参数实现自动重定向。

  例如***redis-cli -c –p 6379***登入后，再录入、查询键值对可以自动重定向, 设置数据会自动切换到对应的写主机

  ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427163051611.png) 如图, `k1`应该送往6381主机负责的12706插槽

- 不在一个slot下的键值, 是**不能使用mget, mset等多键操作的** 

  但是可以通过`{}`来定义组的概念, 从而使得key中`{}`内相同内容的键值对能够放到同一个slot中去

  <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330091452961.png" alt="image-20230330091452961"  /> 

### 10.2.4 如何查询集群中的值？

- 每个主机只能查询自己范围内部的插槽。

`cluster keyslot <key>`：查询某个***key***的***slot* **。

`cluster countkeysinslot <slot>`：查询某个***slot***是否有值。

`CLUSTER GETKEYSINSLOT <slot><count>`：返回***count***个***slot***槽中的键。



### 故障恢复

1. 如果主节点下线？从节点能否自动升为主节点？注意：***15***秒超时。<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427165237232.png" alt="在这里插入图片描述" style="zoom:150%;" />
   - 当***6379***挂掉后，***6389***成为新的主机。


2. 主节点恢复后，主从关系会如何？主节点回来变成从机。<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427165248918.png" alt="在这里插入图片描述" style="zoom:150%;" />
   - 当***6379***重启后，***6379***成为***6389***的从机。




3. 如果所有某一段插槽的主从节点都宕掉，***redis***服务是否还能继续?

   - `redis.conf` 中的参数 `cluster-require-full-coverage`

   - 如果某一段插槽的主从都挂掉，而***cluster-require-full-coverage=yes***，那么 ，整个集群都挂掉。

   - 如果某一段插槽的主从都挂掉，而***cluster-require-full-coverage=no***，那么，该插槽数据全都不能使用，也无法存储。




## 10.3 集群的优缺点

1. 优点

   - 实现扩容；

   - 分摊压力；

   - 无中心配置相对简单。


2. 缺点

   - 多键操作是不被支持的；

   - 多键的***Redis***事务是不被支持的。***lua***脚本不被支持；

   - 由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至***redis cluster***，需要整体迁移而不是逐步过渡，复杂度较大。




# 十一. Jedis操作Redis

即***Java***操作***Redis***。

1. 依赖

```xml
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>3.2.0</version>
</dependency>
```

2. 连接***Redis***

```java
public class JedisDemo {
  public static void main(String[] args) {
    Jedis jedis = new Jedis("192.168.57.101", 6379);
    String pong = jedis.ping();
    System.out.println("连接成功：" + pong);
    jedis.close();
  }
}
```



***Key***

```java
jedis.set("k1", "v1");
jedis.set("k2", "v2");
jedis.set("k3", "v3");
Set<String> keys = jedis.keys("*");
System.out.println(keys.size());
for (String key : keys) {
System.out.println(key);
}
System.out.println(jedis.exists("k1"));
System.out.println(jedis.ttl("k1"));                
System.out.println(jedis.get("k1"));
```

***String***

```java
jedis.mset("str1","v1","str2","v2","str3","v3");
System.out.println(jedis.mget("str1","str2","str3"));
```

***List***

```java
List<String> list = jedis.lrange("mylist",0,-1);
for (String element : list) {
System.out.println(element);
}
```

***Set***

```java
jedis.sadd("orders", "order01");
jedis.sadd("orders", "order02");
jedis.sadd("orders", "order03");
jedis.sadd("orders", "order04");
Set<String> smembers = jedis.smembers("orders");
for (String order : smembers) {
System.out.println(order);
}
jedis.srem("orders", "order02");
```

***Hash***

```java
jedis.hset("hash1","userName","lisi");
System.out.println(jedis.hget("hash1","userName"));
Map<String,String> map = new HashMap<String,String>();
map.put("telphone","13810169999");
map.put("address","atguigu");
map.put("email","abc@163.com");
jedis.hmset("hash2",map);
List<String> result = jedis.hmget("hash2", "telphone","email");
for (String element : result) {
System.out.println(element);
}
```

***zset***

```java
jedis.zadd("zset01", 100d, "z3");
jedis.zadd("zset01", 90d, "l4");
jedis.zadd("zset01", 80d, "w5");
jedis.zadd("zset01", 70d, "z6");

Set<String> zrange = jedis.zrange("zset01", 0, -1);
for (String e : zrange) {
System.out.println(e);
}
```



##  *Jedis* 主从复制

```java
private static JedisSentinelPool jedisSentinelPool=null;

public static  Jedis getJedisFromSentinel(){

  if(jedisSentinelPool==null){
    Set<String> sentinelSet=new HashSet<>();
    sentinelSet.add("172.16.88.168:26379"); // 端口为sentinal
    JedisPoolConfig jedisPoolConfig =new JedisPoolConfig();
    jedisPoolConfig.setMaxTotal(10); // 最大可用连接数
    jedisPoolConfig.setMaxIdle(5); // 最大闲置连接数
    jedisPoolConfig.setMinIdle(5); // 最小闲置连接数
    jedisPoolConfig.setBlockWhenExhausted(true); // 连接耗尽是否等待
    jedisPoolConfig.setMaxWaitMillis(2000); // 等待时间
    jedisPoolConfig.setTestOnBorrow(true); // 取连接的时候进行测试

    jedisSentinelPool=new JedisSentinelPool("mymaster",sentinelSet,jedisPoolConfig); // 服务主机名
    return jedisSentinelPool.getResource();
  }
  else {
    return jedisSentinelPool.getResource();
  }
}
```



## 集群的 *Jedis* 开发

即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。

无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。

```java
public class JedisClusterTest {
  public static void main(String[] args) { 
     Set<HostAndPort>set =new HashSet<HostAndPort>();
     set.add(new HostAndPort("172.16.88.168",6379)); // 任何一个端口
     JedisCluster jedisCluster = new JedisCluster(set);
     jedisCluster.set("k1", "v1");
     System.out.println(jedisCluster.get("k1"));
  }
}
```



# SpringBoot整合Redis

1. 依赖

```xml
<!-- redis -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- spring2.X集成redis所需common-pool2-->
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-pool2</artifactId>
  <version>2.6.0</version>
</dependency>
```

2. 配置文件配置***Redis***

```properties
#Redis服务器地址
spring.redis.host= ip
#Redis服务器连接端口
spring.redis.port=6379
#Redis数据库索引（默认为0）
spring.redis.database= 0
#连接超时时间（毫秒）
spring.redis.timeout=1800000
#连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

3. ***Redis***配置类（需要继承***CachingConfigurerSupport***）

```java
@EnableCaching
@Configuration
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
				// key序列化方式
        template.setKeySerializer(redisSerializer);
				// value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
				// value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
				// 解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
				// 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = 
          RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
      .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
```



# 十二. 应用问题解决

## 12.1 缓存穿透

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330094518227.png" alt="image-20230330094518227" style="zoom:67%;" />

### 12.1.1 现象

- ***key***对应的数据在数据源并不存在，每次针对此***key***的请求从缓存获取不到，请求都会压到数据库，从而可能压垮数据库。

- 比如用一个不存在的用户***id***获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。
- 造成：
  1. 应用服务器压力变大。
  1. ***redis***命中率下降 $\longrightarrow$ 查询数据库 。




### 12.1.2 如何解决

1. **对空值缓存**

  - 如果一个查询返回的数据为空（不管是数据是否不存在），仍然把这个空结果（***null***）进行缓存

  - 设置空结果的过期时间会很短，最长不超过五分钟。

2. **设置可访问的名单（白名单）：**

  - 使用***bitmaps***类型定义一个可以访问的名单，名单***id***作为***bitmaps***的偏移量
  - 每次访问和***bitmap***里面的***id***进行比较，如果访问***id***不在***bitmaps***里面，进行拦截，则不允许访问。

3. **采用布隆过滤器**

  - 布隆过滤器（***Bloom Filter***）是1970年由布隆提出的。它实际上是一个很长的二进制向量（位图）和一系列随机映射函数（哈希函数）。


  - 布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。


  - 将所有可能存在的数据哈希到一个足够大的***bitmaps***中，一个一定不存在的数据会被这个***bitmaps***拦截掉，从而避免了对底层存储系统的查询压力。

4. **进行实时监控**

  - 当发现***Redis***的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务。



## 12.2 缓存击穿

<img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330095538316.png" alt="image-20230330095538316" style="zoom:67%;" /> 

- ***key***对应的数据存在，==但在***redis***中过期==，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端***DB***加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端***DB***压垮。
- 一个key失效, 此时有大量请求访问该key, 造成DB压力过大

### 解决方案

1. 预先设置热门数据

  - 在***redis***高峰访问之前，把一些热门数据提前存入到***redis***里面，加大这些热门数据***key***的时长。

2. 实时调整

  - 现场监控哪些数据热门，实时调整***key***的过期时长。

3. 使用锁

   1. 就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。
   2. 先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key
   3. 当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；
   4. 当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427165735913.png)



## 12.3缓存雪崩

***key***对应的数据存在，但在***redis***中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端***DB***加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端***DB***压垮。

- 缓存雪崩与缓存击穿的区别在于这里针对很多***key***缓存，前者则是某一个***key***。
- 大量key同时失效, 此时请求量大, DB压力过大

1. 正常访问
   ![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427165942327.png)
2. 缓存时效瞬间
   <img src="https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/image-20230330100311310.png" alt="image-20230330100311310" style="zoom:67%;" />



### 解决方案

1. **构建多级缓存架构**
  - ***nginx***缓存 +***redis***缓存 + 其他缓存（***ehcache***等）

2. **使用锁或队列：**
  - 用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况。

3. **设置过期标志更新缓存：**
  - 记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际***key***的缓存。

4. **将缓存失效时间分散开：**
  - 比如我们可以在原有的失效时间基础上增加一个随机值，比如 1～5 分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发**集体失效**的事件。



## 12.4 分布式锁

### 12.4.1 问题描述

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

- 分布式锁主流的实现方案：
  1. 基于数据库实现分布式锁
  2. 基于缓存（Redis等）
  3. 基于Zookeeper

- 每一种分布式锁解决方案都有各自的优缺点：
  1. 性能：redis最高
  2. 可靠性：zookeeper最高

这里，我们就基于redis实现分布式锁。

### 13.4.2 解决方案：使用redis实现分布式锁

redis : 命令

`set sku:1:info “OK” NX PX 10000` 

- `EX second` ：设置键的过期时间为 second 秒。 
  - SET key value EX second 效果等同于 SETEX key second value 。
- `PX millisecond` ：设置键的过期时间为 millisecond 毫秒。 
  - SET key value PX millisecond 效果等同于 PSETEX key millisecond value 
- `NX` ：只在键不存在时，才对键进行设置操作。 
  - SET key value NX 效果等同于 SETNX key value 。
- `XX` ：只在键已经存在时，才对键进行设置操作。

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427170843840.png)

1. 多个客户端同时获取锁（setnx）
2. 获取成功，执行业务逻辑{从db获取数据，放入缓存}，执行完成释放锁（del）
3. 其他客户端等待重试

### 4.3 编写代码

Redis: set num 0

```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setne
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return;
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        redisTemplate.delete("lock");

    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

重启，服务集群，通过网关压力测试：
`ab -n 1000 -c 100 http://192.168.140.1:8080/test/testLock`

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042717112672.png)

查看redis中num的值：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171142388.png)
基本实现。
问题：setnx刚好获取到锁，业务逻辑出现异常，导致锁无法释放
解决：设置过期时间，自动释放锁。

### 4.4 优化之设置锁的过期时间

设置过期时间有两种方式：

1. 首先想到通过expire设置过期时间（缺乏原子性：如果在setnx和expire之间出现异常，锁也无法释放）
2. 在set时指定过期时间（推荐）

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171218156.png)
设置过期时间：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171301126.png)

压力测试肯定也没有问题。自行测试
问题：可能会释放其他服务器的锁。

场景：如果业务逻辑的执行时间是7s。执行流程如下

1. index1业务逻辑没执行完，3秒后锁被自动释放。
2. index2获取到锁，执行业务逻辑，3秒后锁被自动释放。
3. index3获取到锁，执行业务逻辑
4. index1业务逻辑执行完成，开始调用del释放锁，这时释放的是index3的锁，导致index3的业务只执行1s就被别人释放。
   最终等于没锁的情况。

解决：setnx获取锁时，设置一个指定的唯一值（例如：uuid）；释放前获取这个值，判断是否自己的锁

### 4.5 优化之UUID防误删

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171340643.png)

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171350165.png)

问题：删除操作缺乏原子性。
场景：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171437640.png)

### 4.6 优化之LUA脚本保证删除的原子性

```java
@GetMapping("testLockLua")
public void testLockLua() {
    //1 声明一个uuid ,将做为一个value 放入我们的key所对应的值中
    String uuid = UUID.randomUUID().toString();
    //2 定义一个锁：lua 脚本可以使用同一把锁，来实现删除！
    String skuId = "25"; // 访问skuId 为25号的商品 100008348542
    String locKey = "lock:" + skuId; // 锁住的是每个商品的数据

    // 3 获取锁
    Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid, 3, TimeUnit.SECONDS);

    // 第一种： lock 与过期时间中间不写任何的代码。
    // redisTemplate.expire("lock",10, TimeUnit.SECONDS);//设置过期时间
    // 如果true
    if (lock) {
        // 执行的业务逻辑开始
        // 获取缓存中的num 数据
        Object value = redisTemplate.opsForValue().get("num");
        // 如果是空直接返回
        if (StringUtils.isEmpty(value)) {
            return;
        }
        // 不是空 如果说在这出现了异常！ 那么delete 就删除失败！ 也就是说锁永远存在！
        int num = Integer.parseInt(value + "");
        // 使num 每次+1 放入缓存
        redisTemplate.opsForValue().set("num", String.valueOf(++num));
        /*使用lua脚本来锁*/
        // 定义lua 脚本
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        // 使用redis执行lua执行
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        // 设置一下返回值类型 为Long
        // 因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String 类型，
        // 那么返回字符串与0 会有发生错误。
        redisScript.setResultType(Long.class);
        // 第一个要是script 脚本 ，第二个需要判断的key，第三个就是key所对应的值。
        redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);
    } else {
        // 其他线程等待
        try {
            // 睡眠
            Thread.sleep(1000);
            // 睡醒了之后，调用方法。
            testLockLua();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

Lua 脚本详解：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042717154497.png)
项目中正确使用：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427171619682.png)

### 4.7 总结

1、加锁

```java
// 1. 从redis中获取锁,set k1 v1 px 20000 nx
String uuid = UUID.randomUUID().toString();
Boolean lock = this.redisTemplate.opsForValue()
      .setIfAbsent("lock", uuid, 2, TimeUnit.SECONDS);
1234
```

2、使用lua释放锁

```java
// 2. 释放锁 del
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
// 设置lua脚本返回的数据类型
DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
// 设置lua脚本返回类型为Long
redisScript.setResultType(Long.class);
redisScript.setScriptText(script);
redisTemplate.execute(redisScript, Arrays.asList("lock"),uuid);
12345678
```

3、重试

```java
Thread.sleep(500);
testLock();
12
```

为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：

- 互斥性。在任意时刻，只有一个客户端能持有锁。
- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
- 加锁和解锁必须具有原子性。

# 十三. Redis6.0新功能

## 1.ACL

### 1.1 简介

Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接。

在Redis 5版本之前，Redis 安全规则只有密码控制 还有通过rename 来调整高危命令比如 flushdb ， KEYS* ， shutdown 等。Redis 6 则提供ACL的功能对用户进行更细粒度的权限控制 ：

1. 接入权限:用户名和密码
2. 可以执行的命令
3. 可以操作的 KEY

### 1.2 命令

1、使用acl list命令展现用户权限列表
（1）数据说明
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172027133.png)

------

2、使用acl cat命令
（1）查看添加权限指令类别
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172045247.png)
（2）加参数类型名可以查看类型下具体命令
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172127847.png)

------

3、使用acl whoami命令查看当前用户
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172156427.png)

------

4、使用aclsetuser命令创建和编辑用户ACL
（1）ACL规则
下面是有效ACL规则的列表。某些规则只是用于激活或删除标志，或对用户ACL执行给定更改的单个单词。其他规则是字符前缀，它们与命令或类别名称、键模式等连接在一起。

![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172311449.png)
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172325670.png)

（2）通过命令创建新用户默认权限
acl setuser user1
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172359369.png)
在上面的示例中，我根本没有指定任何规则。如果用户不存在，这将使用just created的默认属性来创建用户。如果用户已经存在，则上面的命令将不执行任何操作。

（3）设置有用户名、密码、ACL权限、并启用的用户
acl setuser user2 on >password ~cached:* +get
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172413518.png)
（4）切换用户，验证权限
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210427172442266.png)

## 2.IO多线程

### 2.1 简介

Redis6终于支持多线程了，告别单线程了吗？

IO多线程其实指客户端交互部分的网络IO交互处理模块多线程，而非执行命令多线程。Redis6执行命令依然是单线程。

### 2.2 原理架构

Redis 6 加入多线程,但跟 Memcached 这种从 IO处理到数据访问多线程的实现模式有些差异。Redis 的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程。之所以这么设计是不想因为多线程而变得复杂，需要去控制 key、lua、事务，LPUSH/LPOP 等等的并发问题。整体的设计大体如下：
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/2021042618034716.png)
另外，多线程IO默认也是不开启的，需要再配置文件中配置

```
io-threads-do-reads  yes 
io-threads 4
12
```

## 3.工具支持（Cluster）

之前老版Redis想要搭集群需要单独安装ruby环境，Redis 5 将 redis-trib.rb 的功能集成到 redis-cli 。另外官方 redis-benchmark 工具开始支持 cluster 模式了，通过多线程的方式对多个分片进行压测。
![在这里插入图片描述](https://yj-notes.oss-cn-hangzhou.aliyuncs.com/image/20210426180828406.png)

## 4.Redis新功能持续关注

Redis6新功能还有：

1. **RESP3新的 Redis 通信协议**：优化服务端与客户端之间通信
2. **Client side caching客户端缓存**：基于 RESP3 协议实现的客户端缓存功能。为了进一步提升缓存的性能，将客户端经常访问的数据cache到客户端。减少TCP网络交互。
3. **Proxy集群代理模式**：Proxy 功能，让 Cluster 拥有像单实例一样的接入方式，降低大家使用cluster的门槛。不过需要注意的是代理不改变 Cluster 的功能限制，不支持的命令还是不会支持，比如跨 slot 的多Key操作。
4. **Modules API**：Redis 6中模块API开发进展非常大，因为Redis Labs为了开发复杂的功能，从一开始就用上Redis模块。Redis可以变成一个框架，利用Modules来构建不同系统，而不需要从头开始写然后还要BSD许可。Redis一开始就是一个向编写各种系统开放的平台。

# 感谢

- [尚硅谷-王老师](https://www.bilibili.com/video/BV1Rv41177Af)