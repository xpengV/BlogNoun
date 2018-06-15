---
title: ActiveMQ-实例
date: 2018-02-01 23:29:17
tags:
- ActiveMQ
---
使用ActiveMQ实现一种点对点的消息模型。工程需要导入activemq-all-5.15.3.jar

<!-- more -->

## 工程结构
![工程结构](/img/Queue/ActiveMQ-2.png)

点对点的消息模型，只有一个消息生产者和一个消息消费者
消息生产者：**ActiveMQProducer**
```java
package com.xiaopeng.activemq;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.MessageProducer;
import javax.jms.Session;
import javax.jms.TextMessage;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;


/**
 * 消息的生产者（发送者）
 */
public class ActiveMQProducer { //默认连接用户名

    private static final String USERNAME = ActiveMQConnection.DEFAULT_USER;
    //默认连接密码
    private static final String PASSWORD = ActiveMQConnection.DEFAULT_PASSWORD;
    //默认连接地址
    private static final String BROKEURL = ActiveMQConnection.DEFAULT_BROKER_URL;
    //发送的消息数量
    private static final int SENDNUM = 10;

    public static void main(String[] args) {
        //连接工厂
        ConnectionFactory connectionFactory;
        //连接
        Connection connection = null;
        //会话
        Session session;

        Destination destination;
        MessageProducer messageProducer;
        connectionFactory = new ActiveMQConnectionFactory(ActiveMQProducer.USERNAME, ActiveMQProducer.PASSWORD, ActiveMQProducer.BROKEURL);
        try {
            connection = connectionFactory.createConnection();
            connection.start();
            //创建session
            session = connection.createSession(true, Session.AUTO_ACKNOWLEDGE);
            //创建一个名称为HelloWorld的消息队列
            destination = session.createQueue("HelloWorld");
            //创建消息生产者
            messageProducer = session.createProducer(destination);
            //发送消息
            sendMessage(session, messageProducer);
            session.commit();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (connection != null) {
                try {
                    connection.close();
                } catch (JMSException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     *
     * @param session
     * @param messageProducer
     * @throws Exception
     */
    public static void sendMessage(Session session, MessageProducer messageProducer)
            throws Exception {
        for (int i = 0; i < ActiveMQProducer.SENDNUM; i++) { //创建一条文本消息
            TextMessage message = session.createTextMessage("ActiveMQ 发送消息" + i);
            System.out.println("发送消息：Activemq 发送消息" + i); //通过消息生产者发出消息
            messageProducer.send(message);
        }
    }
}

```

消息消费者
```java
package com.xiaopeng.activemq;

import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.MessageConsumer;
import javax.jms.Session;
import javax.jms.TextMessage;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

/**
 * 消息的消费者（接受者)
 */
public class ActiveMQConsumer {
    private static final String USERNAME = ActiveMQConnection.DEFAULT_USER;
    //默认连接用户名
    private static final String PASSWORD = ActiveMQConnection.DEFAULT_PASSWORD;//默认连接密码
    private static final String BROKEURL = ActiveMQConnection.DEFAULT_BROKER_URL;//默认连接地址

    public static void main(String[] args) {
        ConnectionFactory connectionFactory;//连接工厂
        Connection connection = null;//连接
        Session session = null;//会话
        Destination destination;//消息的目的地
        MessageConsumer messageConsumer;//消息的消费者
        // 实例化连接工厂
        connectionFactory = new ActiveMQConnectionFactory(ActiveMQConsumer.USERNAME, ActiveMQConsumer.PASSWORD, ActiveMQConsumer.BROKEURL);
        try { //通过连接工厂获取连接
            connection = connectionFactory.createConnection(); //启动连接
            connection.start();
            session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE); //创建一个连接HelloWorld的消息队列
            destination = session.createQueue("HelloWorld"); //创建消息消费者
            messageConsumer = session.createConsumer(destination);
            while (true) {
                TextMessage textMessage = (TextMessage) messageConsumer.receive(100000);
                if (textMessage != null) {
                    System.out.println("收到的消息:" + textMessage.getText());
                } else {
                    break;
                }
            }
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
}

```

测试过程如下：
1. 启动ActiveMQ，运行ActiveMQProducer
2. 查看控制台，生产者生产了10条消息：

```
发送消息：Activemq 发送消息0
发送消息：Activemq 发送消息1
发送消息：Activemq 发送消息2
发送消息：Activemq 发送消息3
发送消息：Activemq 发送消息4
发送消息：Activemq 发送消息5
发送消息：Activemq 发送消息6
发送消息：Activemq 发送消息7
发送消息：Activemq 发送消息8
发送消息：Activemq 发送消息9
```
3. 查看ActiveMQ服务器，队列情况如下：
![Queue](/img/Queue/ActiveMQ-3.png)

Queue代表点对点消息；
Number of pending Messages 代表生产者生产了10条消息；
Messages Enqueued 代表有10条消息在排队，等待被消费

点击Browse查看具体是那十条消息未被消费：
![消息明细](/img/Queue/ActiveMQ-4.png)

## 运行消息消费者
运行ActiveMQConsumer，控制台输出如下：
```
收到的消息:ActiveMQ 发送消息0
收到的消息:ActiveMQ 发送消息1
收到的消息:ActiveMQ 发送消息2
收到的消息:ActiveMQ 发送消息3
收到的消息:ActiveMQ 发送消息4
收到的消息:ActiveMQ 发送消息5
收到的消息:ActiveMQ 发送消息6
收到的消息:ActiveMQ 发送消息7
收到的消息:ActiveMQ 发送消息8
收到的消息:ActiveMQ 发送消息9
```

此时可以发现HelloWorld的消息队列多了一个消费者，队列中的10条消息均被消费了。
