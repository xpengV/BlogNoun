---
title: JMS基本概念
date: 2018-1-31 22:33:30
tags:
- ActiveMQ
---
JMS(java message service)java消息服务，允许应用程序组件基于javaEE平台创建、发送、接收和读取消息。它使得分布式通信耦合度更低，消息服务更加可靠以及异步。

消息模型：
1. point-to-point(p2p)  点对点模型
2. publish/subscribe(pub/sub)  发布订阅模型

<!-- more -->

## p2p模型

```
producer---[Queue]--->consumer
```

- 消息队列（queue)
- 发送者（sender）
- 接受者（receiver）
- 每个消息都会被发送到一个特定的队列，接收者从队列中获取消息。队列会一直保留消息直到它们被消费或者超时

特点：
1. 每个消息只有一个消费者
2. 发送者和消费者没有依赖
3. 接受者在成功接收消息后需向队列应答成功

## pub/sub模型
- 主题（topic）
- 发布者（publisher）
- 订阅者（subscriber）

客户端将消息发送到主题，多个发布者将消息发送到topic，系统将这些消息传递给多个订阅者

特点：
1. 每个消息可以有多个消费者
2. 发布者和订阅者之间有时间上的依赖，必须订阅之后才能消费消息
3. 订阅者必须长时间连接，才能在需要的时候消费消息


## JMS编程模型

#### ConnectionFactory

创建Connection对象的工厂，针对两种不同的jms消息模型，分别有QueueConnectionFactory和TopicConnectionFactory两种。可以通过JNDI来查找ConnectionFactory对象。

#### Destination

Destination的意思是消息生产者的消息发送目标或者说消息消费者的消息来源。对于消息生产者来说，它的Destination是某个队列（Queue）或某个主题（Topic）;对于消息消费者来说，它的Destination也是某个队列或主题（即消息来源）。
所以，Destination实际上就是两种类型的对象：Queue、Topic可以通过JNDI来查找Destination。

#### Connection

Connection表示在客户端和JMS系统之间建立的链接（对TCP/IP socket的包装）。Connection可以产生一个或多个Session。跟ConnectionFactory一样，Connection也有两种类型：QueueConnection和TopicConnection。

#### Session

Session是我们操作消息的接口。可以通过session创建生产者、消费者、消息等。Session提供了事务的功能。当我们需要使用session发送/接收多个消息时，可以将这些发送/接收动作放到一个事务中。同样，也分QueueSession和TopicSession。

#### 消息的生产者

消息生产者由Session创建，并用于将消息发送到Destination。同样，消息生产者分两种类型：QueueSender和TopicPublisher。可以调用消息生产者的方法（send或publish方法）发送消息。

#### 消息消费者

消息消费者由Session创建，用于接收被发送到Destination的消息。两种类型：QueueReceiver和TopicSubscriber。可分别通过session的createReceiver(Queue)或createSubscriber(Topic)来创建。当然，也可以session的creatDurableSubscriber方法来创建持久化的订阅者。

#### MessageListener

消息监听器。如果注册了消息监听器，一旦消息到达，将自动调用监听器的onMessage方法。

使用消息队列的优势：
1. 提供消息灵活性
2. 松耦合
3. 支持异步操作

