---
title: "C++多线程初探"
date: 2020-09-09T22:47:04+08:00
draft: true
---

## 一、基本程序

```cpp

#include <iostream>
#include <thread>
using namespace std;
static const int threads = 10;
void * co(void* args)
{
    cout<<"test"<<endl;
}

int main()
{
    pthread_t th[threads];
    for(int i=0;i<threads;i++)
    {
        int res = pthread_create(&th[i],nullptr,co,nullptr);
        if(res != 0)
        {
            cout<<"创建线程 "<<i<<"失败!"<<endl;
        }
    }
    pthread_exit(nullptr);
//    return 0;
}

```

其中pthread_create是类Unix系统才能使用。

第一个参数为指向线程标识符的指针。
第二个参数用来设置线程属性。
第三个参数是线程运行函数的起始地址。
最后一个参数是运行函数的参数。


## 二、线程传参

```cpp
#include <iostream>
#include <thread>
using namespace std;
static const int threads = 10;
void * co(void* args)
{

    cout<<"test: "<<(int*)args<<endl;
}

int main()
{
    pthread_t th[threads];
    for(int i=0;i<threads;i++)
    {

        int res = pthread_create(&th[i],nullptr,co,(void * )i);
        if(res != 0)
        {
            cout<<"创建线程 "<<i<<"失败!"<<endl;
        }
    }
    pthread_exit(nullptr);
//    return 0;
}

```

传进去的是void(*),读取的时候进行强制类型转换。

