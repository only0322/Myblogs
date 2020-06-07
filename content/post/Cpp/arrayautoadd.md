---
title: "用C++实现数组的自动扩容"
date: 2020-03-07T20:32:27+08:00
draft: true
---

# 用到的技巧

其实就是指针的简单使用，能够通过练习，加深对指针的理解。而在C++容器中，vector也是用数组实现的自动扩容。

当然，我们需要保证数组名不会随之改变，不然就不算什么自动扩容了。

# 代码部分

1. 动态数组的创建。

假设数组的长度是未知的，完全可以用`new`在堆区分配内存。

`int *p =new int[len];`

len的长度初始化为一个较小的数字即可。

2. 判断数组的下标使用情况，满了之后创建新的数组。

vector中，是自动扩容两倍。

3. 释放之前用到的内存空间，并且将旧的数组指针指向新的数组指针。

```cpp
for(int j=0;j<len;j++)
{
    p[j]=array[j];
}
delete[] array;
array=p;
array[i]=a;
```
重点来了，`delete[] array`之后，究竟发生了什么呢？

**其实是array这个指针的内存空间被释放** ，指针成为了**空指针**，能够被访问，但不会指向任何内存地址。

而我们让array重新指向p之后，就完成了数组的扩容，并且不改变之前的名称！

至于下次进行delete操作，也不会有什么影响，因为delete只关心释放的内存地址究竟是什么，而指针变量虽然还是原先的array，但是指向的内存却是新申请的，可以释放的p。

以下是示例程序。

# demo

```cpp
#include <iostream>
using namespace std;

int main()
{
    cout<<"enter len first"<<endl;
    int len;
    cin>>len;
    if(len<=0)
    {
        return -1;
    }
    int *array=new int[len];
    int a=0;
    int i=0;
    cout<<"enter a num or -1 to exit"<<endl;
    while(cin>>a)
    {
        if(a==-1)
        {
            break;
        }
        if(i<len)
        {
            array[i]=a;
            i++;
            cout<<"enter a num or -1 to exit"<<endl;
        }
        else//i>=len
        {
            cout<<"len is full at "<<i<<endl;
            int *p=new int[len*2];
            for(int j=0;j<len;j++)
            {
                p[j]=array[j];
            }
            delete[] array;
            array=p;
            array[i]=a;
            i++;
            len=len*2;
            cout<<"enter a num or -1 to exit"<<endl;
        }
    }
    for(int k=0;k<len;k++)
    {
       cout<<"array =:\n"<<endl;
       cout<<array[k]<<endl;
    }



    return 0;
}

```