---
title: "Redis知识整理"
date: 2020-07-13T22:18:20+08:00
draft: true
---

## 一、简介

redis(REmote DIctionary Server)是一个key-value存储系统，它由C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value类型的数据库，并提供多种语言的API。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。

这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。

与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步，redis在3.0版本推出集群模式。


## 二、Redis的特点

- k、v键值存储以及数据结构存储（如列表、字典）
- 所有数据(包括数据的存储)操作均在内存中完成
- 单线程服务(这意味着会有较多的阻塞情况)，采用epoll模型进行请求响应，对比nginx
- 支持主从复制模式，更提供高可用主从复制模式（哨兵）
去中心化分布式集群
- 丰富的编程接口支持，如Python、Golang、Java、php、Ruby、Lua、Node.js 
- 功能丰富，除了支持多种数据结构之外，还支持事务、发布/订阅、消息队列等功能
- 支持数据持久化(AOF、RDB)

## 三、部署 

```shell
yum install gcc -y  #安装C依赖
wget http://download.redis.io/redis-stable.tar.gz  #下载稳定版本
tar zxvf redis-stable.tar.gz  #解压
cd redis-stable
make PREFIX=/opt/app/redis install   #指定目录编译，也可以不用指定
make install
mkdir /etc/redis   #建立配置目录
cp redis.conf /etc/redis/6379.conf # 拷贝配置文件
cp utils/redis_init_script /etc/init.d/redis  #拷贝init启动脚本针对6.X系统
chmod a+x  /etc/init.d/redis  #添加执行权限
vi /etc/redis/6379.conf #修改配置文件： 
bind 0.0.0.0      #监听地址
maxmemory 4294967296   #限制最大内存（4G）：
daemonize yes   #后台运行

####启动与停止
/etc/init.d/redis start
/etc/init.d/redis stop
```

查看是否成功安装

```shell
#执行客户端工具
redis-cli 
#输入命令info
127.0.0.1:6379> info
# Server
redis_version:4.0.10
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:cf83e9c690dbed33
redis_mode:standalone
os:Linux 2.6.32-642.el6.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
```

## 四、数据类型以及相关操作

通常使用redis不外乎使用其常用的5中数据类型：string、list、hash、set、sorted_set

key操作

常用：

DEL key #删除某个key
KEYS pattern  #查看符合正则的所有key
EXISTS key [key ...] #判断某个key是否存在，可支持多个，返回存在的个数
EXPIRE key seconds #刷新某个key过期时间
MOVE key db  #移动key到某个数据库

### 1.对string操作

redis命令不区分大小写。

下面命令中，str就是key，hello就是value，append为追加命令，如果原来没有str，就新建一个。

```cpp
append str hello    //对key为str的键追加hello字符串
append str redis　　//str的value变为helloredis
set str1 1　  //set命令设置一个key的value值 str1是key，1是value
get str1　　 //get命令，获取一个key的值 
incr str1　 //incr命令，执行加1操作，比如str1的值会变成2，如果指定的key的value不能表示一个整数，就会返回一个错误
decr str1　 //减一操作
```

### 2.对hash操作

```cpp
hset myhash h1 hello　　//设置一个key的value值
hget myhash h1　　//返回hello，myhash为key，h1是value里面的key，两个都需要指定
hlen myhash　　//获取myhash的字段数量，这里返回1
hkeys myhash　　//获取myhash所以字段，这里返回h1

```


### 3.对list操作

```cpp
lpush mylist a b c d　　//lpush,从队列左边入队一个或多个元素
lrange mylist 0 -1　　//获取指定范围的值，从0开始，-1代表全部，注意这里返回d,c,b,a。
rpush mylist 1 2 3　　//从右边入队,再次lrange的话就是d,c,b,a,1,2,3
lpop mylist　　//从左边弹出一个元素，这里弹出d,此时的mylist就没有d了
```

### 4.对set操作

```cpp
sadd myset a b c d　　//添加一个或多个元素到集合里面
smembers myset　　//获取集合里所有元素，输出是无序的，随机的。这里可能是b,d,c,a
srem myset a c　　//移除myset中的a和c元素，由于不可能重复也没顺序，所以可以直接指定元素值来移除
```

### 5.对zset的操作

myzset为key，a b c前面的数字就是score
```cpp
zadd myzset 2 b 1 a 3 c　　//添加一个或多个元素
zrange myzset 0 -1　　//获取指定范围的值，0开始，-1代表全部。这里返回a,b,c
```


## 五、常见问题

1.在大量的key中查询某一固定前缀的key

在实际的业务当中，key的命名是有规范的，比如缓存用户信息，key的前缀可能会是user。

现在有几千万条数据，查询user为前缀的key的话，第一下想到的可能会是keys命令

keys user*　　//user*为正则表达式
其时间复杂度为O(n)，虽然性能也算可以，但是在查询几千万条数据时明显太慢了，花上几分钟都不稀奇，而且在查询出来之前，可能会造成服务卡顿，占用大量内存，显然是不可取的。

那么这种情况就可以使用scan命令

下面的命令中，math count为可选项，可用可不用，所以需要显示的写出来。math意味后面会匹配一个正则表达式。count代表一次查询10条。

这个10条不是强制的，可能会比10条少。

scan 0 math user*  count 10　　//从0开始，查询user为前缀的key，一次查询10条并返回
执行上面一句话后，会返回两个东西，一个游标，代表执行到哪了，比如执行到了14325。返回的另一个就是user为前缀的key了。

下次再执行这条语句时，把0换成14325，接着上次的位置继续查询。但是游标不一定是递增的，也许下次的游标比这次还小，所以存在重复的隐患。

我们可以在业务代码处循环查询，记录每次返回的游标，并把查询的key存入到set当中，起到去重的效果。

scan，实际上就是分批查询，速度显然没有keys快，在查询大量数据时，不会对服务器造成压力。数据量不大时依旧推荐keys

 

2.利用Redis实现分布式锁

首先了解什么是分布式锁。即控制分布式系统访问共享资源的一种方式。

比如系统（或主机）A和B都需要访问资源DataA时，当A先访问到了DataA，这时候就需要分布式锁来把B挡住，防止A和B彼此干扰，保证数据的一致性。

额外提一点就是，Redis命令的操作是原子性的，原子性在数据库的事务中有体现，Redis的命令也是原子性的，要么执行要么不执行，不会出现一个命令执行到一半失败了，但还是改变了数据的问题。

实现分布式锁，需要解决一下几个问题：

1.互斥性，即任意时刻只能有一个客户端获取锁。

2.安全性，锁只能有持有它的客户端删除，不能由其他客户端删除。

3.死锁，即由于某些原因，一些客户端出现问题不能及时释放锁，导致其他客户端也不能获取锁。

4.容错，当某些Redis节点出现问题时，客户端也要能获取到锁。

我们可以用setnx实现锁的功能。语法：setnx key value

仅当key不存在时，才会设置成功。成功返回1，否则返回0。

1.在对应的访问资源的业务代码处，对指定的key设值，如果成功了，则代表没有其他线程执行过这段代码，也就是没有其他线程访问这个资源。

如果设值失败，就代表有其他线程占用该资源，就一直等待，直到setnx成功。

2.还有个问题就是，这个key是长期有效的，所以还需要用到expire命令，语法：expire key seconds,seconds单位为秒，用以设置对应key的过期时间。

上面两步似乎好像是实现了锁的功能，但是缺陷也非常明显，如果成功设值后，在我设置时间之前客户端就出现问题了怎么办？用两个命令实现一个功能有悖于Redis的原子性。

在Redis2.6.12版本开始，set有两个参数，就是实现了以上两个功能。虽然上面两步分开的做法是错的，但是思路是一样的。

具体语法：set key value ex 10 nx。ex代表过期时间，这里设置10秒过期，nx代表key是要唯一的，即一个命令实现了以上两个步骤。

最后还有一个小问题，如果不同资源同时设置了锁key，过期时间也是一样的，到期后Redis同时删除大量key时，难免会出现卡顿。

解决方法就是在设置过期值时加上随机值。

 

3.利用Redis实现消息队列

消息队列，简称MQ，即消息和队列两个单词的首字母缩写。常见的消息队列有RabbitMQ和RocketMQ等，利用Redis实现消息队列只是熟悉下其特点，实际当中一般会使用专门的消息队列中间件。

如果之前没了解过消息队列，建议搜索一下消息队列相关知识进行一下简单的学习。

简单地说，消息队列的作用就是接受客户端的请求，然后对这些请求依次处理，一般应用请求量特别大时，比如秒杀抢购等。上面介绍数据类型时就说到了list一般用于消息队列。

看一下list的常见操作，虽然叫做列表，但其特点和数据结构的队列基本一模一样。所以在用Redis实现消息队列时，首先肯定会想到list。

1.利用list的话，仿佛使用rpush生产消息，lpop消费消息就行了。但是有一个小问题，lpop不会等待rpush的，当rpush还没来得及生成数据时，这时lpop会直接返回null的。

2.既然要等待rpush生成数据，难免又会想到一个命令blpop,其语法为：blpop key seconds。和lpop功能一样，但是会等待指定的时间，这段时间内rpush如果生成数据的话，blpop会及时返回。

3.但是blpop的缺点也很明显，当然这个缺点也存在于lpop当中，就是blpop执行完后，代表出队，rpush生成的这条消息就没了，而消息队列中有的需求是需要多个消费者去接收的。

这时候就可以用上Redis的订阅者模式，Redis客户端可以订阅任意数量的频道(Topic)


4.Redis如何做持久化

Redis是基于内存当中的，那么肯定就会有疑问了，当我关闭主机或者关闭了Redis，那Redis的数据是不是就全没了。

持久化的作用就是，把Redis的数据存储到磁盘当中，以免Redis的数据丢失。

Redis有两种持久化机制，默认的一种是RDB,另一种是AOF。

1.RDB(快照)持久化会在某个时间点保存全量的数据，快照即针对内存进行的快速读取技术。而这个时间点可以由我们的实际业务进行时间策略配置。

RDB会按照时间周期策略对数据以快照的方式保存到磁盘里，并产生一个dump.rdb的二进制文件。我们可以在redis.conf配置文件中save参数查看和配置时间策略。

dump.rdb文件是如何创建的呢？rdb文件可以通过两个命令创建，一个是save,一个是bgsave。要注意这里的save是redis命令，上面提到的save是配置文件里面的参数。

save命令会阻塞Redis服务器进程，直到rdb文件创建完成，一般很少使用。

bgsave命令会fork出一个子进程来创建rdb文件，不会阻塞服务器进程。fork即创建一个与父进程几乎一样的子进程。

bgsave的基本原理：当我们使用bgsave命令时，首先会检查是否存在RDB/AOF子进程正在进行，有的话就返回错误，即当我们第一次执行了bgsave，在执行完之前其他的bgsave会被拒绝执行。

如果没有正在进行的子进程，就会调用redis源码里面的rdbSaveBackground这个方法，然后利用fork创建一个子进程。

RDB的缺点：

　　1.1.前面提到，在某个时间点会进行全量数据保存，数据量大的话由于I/O而严重影响到性能。

　　1.2.由于RDB是根据配置文件里面的时间策略进行保存的，如果发生意外情况，那么上次保存到当前时间段内的数据会发生丢失。

 

2.AOF(Append-Only-File)持久化会以追加的方式（append）保存除了查询指令以外所有变更的数据，其默认的文件名称为appendonly.aof。

AOF持久化默认是关闭的，我们可以在配置文件当中找到appendonly参数，把它的参数内容改为yes。

前面说到AOF文件会记录所有非查询的所有指令，最后肯定难以避免文件不断增大的问题，最主要的问题是记录的很多数据是不必要的。

比如循环更新一个数100次，AOF会记录这100个过程，而我们只需要最终结果就行了。

所以，Redis提供了一个日志重写的功能解决文件不断增大的问题，可以用BGREWRITEAOF命令手动执行。日志重写在服务不中断的情况下也能执行，其基本原理如下：

1.使用fork创建一个子进程。2.子进程把新的AOF写道一个临时文件里，并不会依赖现有的AOF文件，只需要读取内存中的数据。这里就优化了很多不必要的数据。

3.主进程这时候会依旧将新的变动写到内存里，也会写到现有的AOF文件里，即使子进程重写失败，数据也不会丢失。4.主进程获取到子进程AOF重写完成的信号后，会把新的变动追加到新的AOF文件里。

5.最后使用新的AOF文件替换掉原来的AOF文件。

如果启用了AOF持久化，Redis启动时会先检查AOF文件是否存在，如果存在就直接加载AOF文件，如果不存在就检查RDB文件是否存在，如果存在就加载，不存在就直接启动Redis。

在Redis4.0之后，推出了RDB-AOF混合持久化方式并作为默认方式，RDB全量保存，AOF增量保存，集成了它们各自的优点。

 

