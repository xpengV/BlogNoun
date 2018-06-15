---
title: ActiveMQ-安装和特性
date: 2018-02-01 20:12:33
tags:
- ActiveMQ
---
ActiveMQ是一个易于使用的消息中间件，用于消息的接收和转发推送。它可以有以下用途：
- 将数据从一个应用程序传递到另一个应用程序，或者在模块之间传递数据
- 建立通信网络的信道，进行数据的可靠传递
- 实现跨平台操作

<!-- more -->

## 下载ActiveMQ
官方网站：[http://activemq.apache.org](http://activemq.apache.org)
【apache-activemq-5.15.3】的目录结构：
1. bin存放的是脚本文件
2. conf存放的是基本配置文件
3. data存放的是日志文件
4. docs存放的是说明文档
5. examples存放的是简单的实例
6. lib存放的是activemq所需jar包
7. webapps用于存放项目的目录

## 启动ActiveMQ
```
apache-activemq-5.15.3 xiaopeng$ cd bin/
bin xiaopeng$ ./activemq
```
## 登录控制台
ActiveMQ启动时，内置了一个服务器，方便监控。登录http://127.0.0.1:8161/admin/
username:admin password:admin

![ActiveMQ-1](/img/Queue/ActiveMQ-1.png)

## ActiveMQ的特性
- 支持多种语言和协议编写客户端
- 对spring支持，可以使用spring ioc容器管理ActiveMQ
- 支持通过jdbc对消息进行持久化
- 设计上保证高性能，点对点

## 合适使用ActiveMQ的场景：
**- 针对多项目的异构平台兼容性（跨平台、跨语言、多系统）**
**- 减低系统间耦合性**
**- 系统前后端分离进行通信**




