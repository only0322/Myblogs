---
title: "RabbitMQ消息重发、死信处理"
date: 2020-06-02T22:01:18+08:00
draft: true
---

## 了解死信队列

![死信](/images/rabbit/IPCLOOP.png)

从图中发现知：

生产者发送的消息会经由交换机传送到绑定的队列中，然后经由消费者接受消息，消息一旦被消费者消费后，就会移除队列中，表示整个流程的结束。

但是如果消息没有被消费成功（或者说消息变为了死信），那么正常来说和原来的（正常）队列绑定的消费者就无法消费消息，那么变为死信的消息怎么处理啦！

在正常的业务中，会针对这一部分进行特殊处理，可以考虑指定一个死信交换机，并绑定一个死信队列，当消息变成死信时，该消息就会被发送到该死信队列上，这样就方便我们查看消息失败的原因了。

换句话说就是 没有被消费的消息， 换个地方重新被消费。

整个流程可以表示成

生产者 --> 消息 --> 交换机 --> 队列 --> 变成死信 --> DLX交换机 -->队列 --> 消费者

## 死信产生的条件

（1）消息被拒绝（basic.reject或basic.nack）并且requeue=false.

（2）消息TTL超期 (rabbitmq Time-To-Live -> messageProperties.setExpiration())

（3）队列达到最大长度（队列满了，无法再添加数据到mq中）

## RabbitMQ削峰

比如系统的最高负载量是每秒2000个请求，但客户端每秒钟发送了10000个请求，服务器无法进行处理。这种情况下就会导致服务的崩溃，而削峰就能在一定程度上防止崩溃的发生。

rabbitmq提供了一种服务质量保障功能，即在非自动确认消息的前提下，如果一定数目的消息未被确认，不进行消费新的消息。

而消息中间件本身就有这方面的用途，使用较多的消息队列有 ActiveMQ、RabbitMQ、 ZeroMQ、Kafka、MetaMQ、RocketMQ 等，它们都可以将瞬时的请求放到消息队列中进行缓冲，实现间接发送。


## 后续有内容再做补充吧