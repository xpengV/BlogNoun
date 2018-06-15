---
title: redis安装与常用命令
date: 2017-11-4 10:15:20
tags:
- redis
---

redis是一个高性能的key-Value非关系型数据库。它具有以下特点：
1. 支持数据持久化
2. redis不仅支持字符串类型数据，同时支持list、set、zset、hash等数据结构的存储
3. redis支持数据备份
4. 性能极高，读写速度接近100000次/s
5. redis数据库的操作具有原子性（数据库事务控制：要么全部成功，要么全部失败）

<!-- more -->

# redis的安装
Mac下的安装
```
进入官网[http://redis.io/](http://redis.io/)下载redis-3.2.0.tar
sudo mv ~/Downloads/redis-3.2.0.tar /usr/local/
sudo tar -zxf redis-3.2.0.tar
cd redis-3.2.0
sudo make test "测试编译"
sudo make install
```

# redis常用命令
1. 登录客户端
$ redis-cli
2. 判断服务是否启动
$ redis 127.0.0.1:6379> PING
3. 远程连接
$ redis-cli -h host -p port -a password
4. 数据库备份 在redis安装目录创建dump.rdb文件
$ save
5. 数据库回复
将备份文件（dumo.rdb）移动至redis安装目录并启动服务

# redis数据类型
redis支持五种数据类型：string（字符串）、hash（哈希）、list（列表）、set（集合）、zset（有序集合）

1.string
使用键值对进行数据存储，一个key对应一个value。String可以是任何数据，甚至是二进制的序列化对象，String类型一个键最大存储521MB。
通过 *set* 和 *get* 创建数据。
```
127.0.0.1:6379> set user "xiaopeng"
OK
127.0.0.1:6379> get user
"xiaopeng"
```
redis存储String类型数据经常使用的命令如下：

命令 | 功能
---|---
set key value | 指定key的值
get key | 获取key的值
getrange key start end | 获取返回值的子字符串
getset key value | 将给定key的值设置为value，并返回旧值
mget key1 key2 | 返回多个key的值
mset key1 value1 key2 value2 | 同时设置多个key-value
setnx key value | 只有当key不存在时，才设置key的值
strlen key | 返回key所存储字符串值的长度
incr key | 将key中存放的数字值++
incrby key increment | 将key所存储的值加上指定的增量（increment）
decr key | 将key中存放的数字值减一
decrby key decrement | 将key所存储的值减去指定的减量（decrement）
append key value | 如果key已经存在并且是字符串，则在其值后追加value



2.hash
redis-hash是一个String类型的field和value的映射表，适合存储对象。语法 *HMSET HashExample field1 "Hello" field2 "World"*
```
127.0.0.1:6379> HMSET HashExample field1 "Hello" field2 "World"
OK
127.0.0.1:6379> HGET HashExample field1
"Hello"
127.0.0.1:6379> HGET HashExample field2
"World"
```

redis-hash操作：

命令 | 功能
---|---
hmset key field1 "a" | 向哈希表key中放入field1-"a"
hdel key field1 field2 | 删除一个或多个哈希表字段
hexists key field | 查看哈希表key中指定field是否存在
hget key field | 获取指定字段的值
hgetall key | 获取哈希表所有的字段和值
hlen key |  获取哈希表中字段的数量
hvals key | 获取哈希表中所有值

3.list
redis-list存放简单的字符串列表，按照插入顺序排序，可以添加元素在表头或者表尾。
```
127.0.0.1:6379> lpush ListExample "aaa"
(integer) 1
127.0.0.1:6379> lpush ListExample "bbb"
(integer) 2
127.0.0.1:6379> lpush ListExample "ccc"
(integer) 3
127.0.0.1:6379> lrange ListExample 0 2
1) "ccc"
2) "bbb"
3) "aaa"
```

命令 | 功能
---|---
lpush key value | 向列表中插入值
lrange key start end | 输出列表部分值
rpop key | 移除并获取列表最后一个元素(FIFO)
blpop key1[key2] timeout | 移除并返回列表的第一个元素(FILO)
lset key index value | 通过索引设置列表元素的值
lindex key index | 通过索引获取列表中的元素


 
4.set
redis-set是String类型的集合，可以进行添加，删除，查询操作。向set中添加元素，成功返回1失败返回0。
**set内元素具有唯一性，第二次插入相同的元素会被忽略（返回0）**
```
127.0.0.1:6379> sadd SetExample i
(integer) 1
127.0.0.1:6379> sadd SetExample love
(integer) 1
127.0.0.1:6379> sadd SetExample u
(integer) 1
127.0.0.1:6379> smembers SetExample
1) "i"
2) "u"
3) "love"
```

命令 | 功能
---|---
sadd key member1[member2] | 向集合添加一个或多个成员
smembers key | 遍历输出集合
scard key | 获取集合成员数
sdiff key1[key2] | 返回所有给定集合的差集
sinter key1[key2] | 返回所有给定集合的交集
sismember key member | 判断member元素是否是key的成员
srem key member1[member2] | 移除集合中的一个或多个成员
spop key | 移除并返回集合中的一个随机元素 

5.zset
redis-zset和set一样也是String类型元素的集合，且不允许含有重复的成员。但是zset内部会联动一个double类型的数据（score）。通过该数据对集合中的成员进行从小到大排序。
```
127.0.0.1:6379> zadd ZsetExample 0 "q"
(integer) 1
127.0.0.1:6379> zadd ZsetExample 3 "w"
(integer) 1
127.0.0.1:6379> zadd ZsetExample 2 "e"
(integer) 1
127.0.0.1:6379> zadd ZsetExample 6 "r"
(integer) 1
127.0.0.1:6379> zadd ZsetExample 6 "r"
(integer) 0         ----添加失败返回0；value成员是唯一的，不能重复但是score可以重复
127.0.0.1:6379> ZRANGEBYSCORE ZsetExample 0 1000
1) "q"
2) "e"
3) "w"
4) "r"
127.0.0.1:6379> 
```

命令 | 功能
---|---
zadd key score1 member1[score2 member2] | 向有序集合中添加或更新成员
zcard key | 获取有序集合的成员数
zrangebyscore key start end | 按照score输出集合
zrem key member1[member2] | 移除成员
zscore key member | 返回集合中成员的分数值

# 管理redis的key
可以使用以下操作管理redis数据库的key对象

操作 | 描述
---|---
exists key | 判断key是否存在
del key | key存在则删除key
dump key | 序列化key并返回序列化的值
expire key s | 设置给定key的失效时间(秒)
persist key | 移除key的失效时间，永久保存
keys pattern | 查找所有符合给定模式的key
move key db | 将当前数据库的key移动到给定的数据库db中
randomkey | 从当前数据库随机返回一个key
rename key key1 | 修改key名称
renamenx key key1 | 当key1不存在时，修改key名称为key1
Type key | 返回key所存储的值的类型






