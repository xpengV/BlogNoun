---
title: redis-pub/sub
date: 2017-11-04 20:05:07
tags:
- redis
---
redis发布订阅（pub/sub）是一种消息通信模式：
- 消息发送者（pub）发送消息
- 消息订阅者（sub）接收消息
一个redis客户端可以订阅任意数量的频道

<!-- more -->

# redis消息发布与接收

假设有3个客户端通过 *subscribe channel1* 命令订阅了频道channel1
![订阅者](/img/redis/redis-1.png)
当有新的消息通过 *publish channel message* 命令发送给channel1时，这个消息就会被所有的订阅者接收
![消息接收](/img/redis/redis-2.png)

```
--创建频道channel1
127.0.0.1:6379> subscribe channel1
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel1"
3) (integer) 1
```

重新开启一个redis客户端，然后发布消息，所有订阅者都可以接收消息
```
--发布消息
127.0.0.1:6379> publish channel1 "i love u"
(integer) 1
127.0.0.1:6379> 


--订阅者接收到消息
1) "message"
2) "channel1"
3) "i love u"
```

# redis发布订阅命令

命令 | 描述
---|---
subscribe channel[channel..] | 订阅一个或多个频道
unsubscribe channel[channel..] | 退订一个或多个频道
publish channel message | 向指定频道发送消息
