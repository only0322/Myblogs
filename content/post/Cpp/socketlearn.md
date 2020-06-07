---
title: "C++ socket初探"
date: 2020-02-25T09:54:15+08:00
draft: true
---

本文讲述socket客户端和服务端的简单实现。

# 客户端

1. 用到的头文件及用法

```cpp
#include <iostream>
/*获取设置socket，bind*/

#include<sys/socket.h>
/*基本数据类型，如time_t*/
#include<sys/types.h>
/*用于socketaddr_in结构体，htons系统调用*/
#include<netinet/in.h>
/*对read write提供支持*/
#include<unistd.h>
/*提供 inet_addr*/
#include<arpa/inet.h>
#include<errno.h>
```

2. 程序原理

- 先声明一个存放端口和协议类型的结构体对象。
`sockret=socket(AF_INET,SOCK_STREAM,0);`

- 设置好连接的方式

```cpp
sock.sin_family = AF_INET;
sock.sin_addr.s_addr = inet_addr("127.0.0.1");
sock.sin_port = htons(4000);//40975
len=sizeof(sock);
result=connect(sockret,(struct sockaddr*)&sock,len);
```

- 向服务端收发数据

```cpp
write(sockret,&ch,1);
read(sockret,&ch,1);
```

3.源代码如下

```cpp
#include <iostream>
/*获取设置socket，bind*/
#include<sys/socket.h>
/*基本数据类型，如time_t*/
#include<sys/types.h>
/*用于socketaddr_in结构体，htons系统调用*/
#include<netinet/in.h>
/*对read write提供支持*/
#include<unistd.h>
/*提供 inet_addr*/
#include<arpa/inet.h>

#include<errno.h>
using namespace std;

int main()
{
    int sockret;
    int len;
    struct sockaddr_in sock;
    int result;
    char ch='J';
    /*AF_INET是ipv4，sock流是tcp协议*/
    sockret=socket(AF_INET,SOCK_STREAM,0);
    /*连接方式，IP ，端口，端口用大小端转换*/
    sock.sin_family = AF_INET;
    sock.sin_addr.s_addr = inet_addr("127.0.0.1");
    sock.sin_port = htons(4000);//40975
    len=sizeof(sock);
    result=connect(sockret,(struct sockaddr*)&sock,len);
    if(result!=0)
    {
        perror("clinent error");
    }
    write(sockret,&ch,1);
    read(sockret,&ch,1);
    cout<<"从服务器收到的字符串为="<<ch<<endl;
    close(sockret);
    return 0;
}
```

# 服务端

主要负责监听端口，并且响应连接请求。

```cpp
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<errno.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<unistd.h>

#define MAXLINE 4096

int main(){
    int  listenfd, connfd;
    struct sockaddr_in  servaddr;
    char  buff[4096];
    int  n;

    if( (listenfd = socket(AF_INET, SOCK_STREAM, 0)) == -1 ){
        printf("create socket error: %s(errno: %d)\n",strerror(errno),errno);
        return 0;
    }

    memset(&servaddr, 0, sizeof(servaddr));
    servaddr.sin_family = AF_INET;
    servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
    servaddr.sin_port = htons(4000);

    if( bind(listenfd, (struct sockaddr*)&servaddr, sizeof(servaddr)) == -1){
        printf("bind socket error: %s(errno: %d)\n",strerror(errno),errno);
        return 0;
    }

    if( listen(listenfd, 10) == -1){
        printf("listen socket error: %s(errno: %d)\n",strerror(errno),errno);
        return 0;
    }

    printf("======waiting for client's request======\n");
    while(1){
        if( (connfd = accept(listenfd, (struct sockaddr*)NULL, NULL)) == -1){
            printf("accept socket error: %s(errno: %d)",strerror(errno),errno);
            continue;
        }
        n = recv(connfd, buff, MAXLINE, 0);
        buff[n] = '\0';
        printf("recv msg from client: %s\n", buff);
        close(connfd);
    }
    close(listenfd);
    return 0;
}

```


# 总结

有后续的内容，以后再补充吧

