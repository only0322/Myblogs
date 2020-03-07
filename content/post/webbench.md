---
title: "著名压测工具webbench源码解析"
date: 2020-02-27T16:33:28+08:00
draft: false
---

# 项目概述

Webbench是用的标准C语言编写，仅仅只有500多行代码的轻量级压测软件。

用到的技术有：Linux提供的运行时参数解析，多进程，服务器通信，管道。
github地址：[wenbbench](https://github.com/EZLippi/WebBench)

# 项目整体流程

1.解析程序运行时的参数。

2.根据选择的http协议组装报文。

3.开始压力测试。

4.获取压力测试的结果。

## 1.解析程序运行时的参数

其实这就是传说中的argc argv[]，不过程序在此处用到了C语言的getopt_long函数。

![大概的代码](/images/webbench/webbench_getopt.png)

当然，也会有对于输入的ip地址，端口号的解析和错误处理。
正常的webbench命令示例：webbench http://www.baidu.com/ 真的是少了一个斜杠都不行啊。

## 2.组包

程序提供了http0.9/1.0/1.1的协议选择，那么这三种协议到底有什么区别呢？


http0.9：只支持get方法。
http1.0：支持get，head，post方法。
http1.1：支持长连接，更完善的缓冲处理等等。

详细的解读：https://www.cnblogs.com/wupeixuan/p/8642100.html

http请求的含义：

~~~
GET： 请求指定的页面信息，并返回实体主体。

HEAD： 只请求页面的首部。

POST： 请求服务器接受所指定的文档作为对所标识的URI的新的从属实体。

PUT： 从客户端向服务器传送的数据取代指定的文档的内容。

DELETE： 请求服务器删除指定的页面。

OPTIONS： 允许客户端查看服务器的性能。

TRACE： 请求服务器在响应中的实体主体部分返回所得到的内容。

PATCH： 实体中包含一个表，表中说明与该URI所表示的原内容的区别。

MOVE： 请求服务器将指定的页面移至另一个网络地址。

COPY： 请求服务器将指定的页面拷贝至另一个网络地址。

LINK： 请求服务器建立链接关系。

UNLINK： 断开链接关系。

WRAPPED： 允许客户端发送经过封装的请求。

Extension-mothed：在不改动协议的前提下，可增加另外的方法。
~~~

出处：https://www.cnblogs.com/zyrhmh/p/8942995.html

调用这些请求的时候，只需要将相应的字符拼进去即可。
`strcpy(request,"GET");`

选择协议，也很简单，继续拼接。

`strcat(request," HTTP/1.1");`

还有大篇幅的对于不同http协议版本，运行参数的处理。

## 3.压力测试

先检查服务器是否可以联通，随后创建供父进程和子进程通信的管道。

子进程根据配置发送报文，并且通过管道向父进程回报结果。

![子进程操作](/images/webbench/pipe.png)

在子进程中，也会设置缓冲模式为无缓冲。因为并不需要等到服务器回应我们的消息，而这样设置还可以减少资源的消耗。

setvbuf函数用法如下：

![setvbuf](/images/webbench/setvbuf.png)


_IOFBF(满缓冲）：当缓冲区为空时，从流读入数据。或者当缓冲区满时，向流写入数 据。

_IOLBF(行缓冲）：每次从流中读入一行数据或向流中写入一行数据。

_IONBF(无缓冲）：直接从流中读入数据或直接向流中写入数据，而没有缓冲区。



## 4.统计结果

这部分的代码比较简单，子进程通过管道来向父进程返回结果，用变量去统计结果即可。

```
fprintf(f,"%d %d %d\n",speed,failed,bytes);
fclose(f);
return 0;
```



# 结语

通过学习webbench的源码，我们也能从中了解到，原来压力测试也就是在短时间内建立大量的连接，计算与服务器通信的情况。
webbench用到的多进程，进程间通信，以及http协议，都是值得初学者去学习的。

本文仅仅是以我目前浅薄的见识，来写下自己对webbench的理解，也许日后会进行进一步的更新。