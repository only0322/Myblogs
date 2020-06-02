---
title: "RabbitMQ的消息机制"
date: 2020-06-02T21:25:32+08:00
draft: true
---

## 1.RabbitMQ发送消息

对于消息发送方来说：

生产者发送一条消息，正常情况下是通过交换机发送到队列中，再由消费者接受队列中消息，由消费者进行逻辑操作。

但是RabbitMQ在收到消息后，还需要有一段时间才能将消息存入磁盘之中。
并且也并不是为每条消息都做fsync的处理，可能仅仅保存到cache中而不是物理磁盘上。如果RabbitMQ broker正巧发生了崩溃，因为消息还没来得及保存磁盘，消息将会丢失。

## 2.如何确认消息的发送是否成功？

- a. 消息发送确认

RabbitMQ底层是利用了AMQP协议进行消息传输，关于消费者和生产者的模型就不再赘述了。

RabbitMQ的AMQP协议支持事务，它会通过事务的方式进行消息的发送。而直到一个消息被所有的队列保存完毕后，才会进行提交事务。

但在提交之前，若程序出现了异常，则会调用回滚来回滚当前的事务。

这一部分与数据库事务极为相似。


```C
channel.txSelect();
 //ConfirmConfig.exchangeName(交换机名称)
 //ConfirmConfig.routingKey(路由键)
 //message （消息内容）
channel.basicPublish(ConfirmConfig.exchangeName, ConfirmConfig.routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, message));
channel.txCommit();
```


但缺点也有，就是事务的执行会降低RabbitMQ的性能，同时等待提交的过程也违背了使用RabbitMQ以利用高并发特性的初衷。

- b.发送者确认

生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者（包含消息的唯一ID）。

这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。


对于ConfirmCallback来说：
如果消息没有到exchange,则confirm回调,ack=false
如果消息到达exchange,则confirm回调,ack=true

对于ReturnCallback来说：
exchange到queue成功,则不回调return
exchange到queue失败,则回调return(需设置mandatory=true,否则不回回调,消息就丢了)
比如路由不到队列时触发回调


- c.消息接受确认

1.通过 ACK机制（消息确认机制）确认消息是否被正确接收，每个 Message 都要被确认（acknowledged），可以手动去 ACK 或自动 ACK。

2、默认情况下，一个消息被消费者正确消费就会从队列中移除

3、自动确认会在消息发送给消费者后立即确认，但存在丢失消息的可能，如果消费端消费逻辑抛出异常，也就是消费端没有处理成功这条消息，那么就相当于丢失了消息



