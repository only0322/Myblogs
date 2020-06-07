---
title: "RabbitMQ的启动过程"
date: 2020-06-01T21:27:07+08:00
draft: true
---

## RMQ的代码结构

RabbitMQ的代码都是存放在src目录下的，其中rabbit.erl就是整个程序的启动入口。

在程序启动的过程中，自动地启动相关的进程，检查RMQ的版本，检查网络情况，确定日志的路径。

据说RMQ的启动构建了一张有向无环图，启动了一大堆的进程，这一块也可以在src目录下查阅。

## 前期的准备知识

- AMQP协议 

1. AMQP协议的全称

    AMQP（Advanced Message Queuing Protocol，高级消息队列协议）是一个进程间传递异步消息的网络协议。

2. 工作流程

    由发布者发布消息，发送到交换机（交换程序），交换机通过路由规则分发给绑定的队列，最后投递消息给订阅了队列的接收者。

    ![AMQP流程图](/images/rabbit/AMQP.png)

3. 代码示例

```Erlang
start_connection(Parent, ChannelSupSupPid, Collector, StartHeartbeatFun, Deb,  
                 Sock, SockTransform) ->  
    process_flag(trap_exit, true),  
    ConnStr = name(Sock),  
    log(info, "accepting AMQP connection ~p (~s)~n", [self(), ConnStr]),  
    ClientSock = socket_op(Sock, SockTransform),  
    erlang:send_after(?HANDSHAKE_TIMEOUT * 1000, self(),  
                      handshake_timeout),  
    State = #v1{parent = Parent,…}, // 初始化state，部分代码省略  
    try  
        recvloop(Deb, switch_callback(rabbit_event:init_stats_timer(  
                                       State, #v1.stats_timer),  
                                      handshake, 8)),  
        log(info, "closing AMQP connection ~p (~s)~n", [self(), ConnStr])  
    catch  
        // 异常处理  
    after  
        rabbit_net:maybe_fast_close(ClientSock),  
        rabbit_event:notify(connection_closed, [{pid, self()}])  
    end,  
done.  
```

- OTP与Actors模型

1. 什么是OTP

    OTP全称为**Open Telecom Platform**，是爱立信推出的一套理念

2. 具体理解

    在OTP模型中，进程会被标示为一个角色类型(工作进程、监控进程)，以此来构建一个进程树。

3. Actors

    >Actors模型(Actor model)首先是由Carl Hewitt在1973定义， 由**Erlang OTP (Open Telecom Platform)** 推广，其 消息传递更加符合面向对象的原始意图。 Actors属于并发组件模型 ，通过组件方式定义并发编程范式的高级阶段，避免使用者直接接触多线程并发或线程池等基础概念。

    Actor模型=数据+行为+消息。它并不只被Erlang广泛使用，Actor只是一种高并发编程模型，同时支持多种语言，但使用最为广泛的还是Erlang。


## RabbitMQ信道与TCP

1. RMQ的信道

信道是生产消费者与rabbit通信的渠道，生产者publish或是消费者subscribe一个队列都是通过信道来通信的。信道是建立在TCP连接上的虚拟连接。

rabbitmq在一条TCP上建立成百上千个信道来达到多个线程处理，这个TCP被多个线程共享，每个线程对应一个信道，信道在rabbit都有唯一的ID ,保证了信道私有性，对应上唯一的线程使用。

2. 为什么不使用TCP连接

因为TCP每次连接需要三次握手，四次挥手，在高并发的场景下极其消耗资源。长连接下的多信道模式，更符合RMQ的业务需求。

