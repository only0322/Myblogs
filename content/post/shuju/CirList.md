---
title: "C++循环链表总结"
date: 2020-04-13T18:37:12+08:00
draft: true
---

### 循环链表的结构与特点

循环链表也分为单链循环和双链循环，和普通的链表相比，区别就是遍历到尾节点之后，next指针不是空指针，而又重新指向了head。

![循环链表](/images/NodeList/CirCle.png)

还是先给出源码

[cpp循环链表](https://gitee.com/onlyyyy/cpp_circular_list)

### 具体实现

#### 1.类定义

```cpp
class Node
{
public:
    int data;
    Node * next;
};

class CirList
{
private:
    Node * head;
};
```

#### 2.构造函数

```cpp
CirList::CirList()
{
    head = nullptr;
}

```
构造函数我并不想new节点，具体原因有很多……

#### 3.析构函数

```cpp
CirList::~CirList()
{
    Node *p = head;
    Node * temp =p;
    if(temp==nullptr||temp->next==nullptr)
    {
        //空表
        return;
    }
    else
    {

        int length = getsize();
        for(int i=0;i<length;i++)
        {
            p =temp;
            temp = temp->next;
            delete p;
            p = nullptr;
        }
    }
}
```

循环链表的析构函数有一定的特殊性，因为head是第一个被释放的，而最终又会指向head，用判断长度的方法去释放，容错率会高很多。

#### 4.建表

```cpp
void CirList::create(int length)
{
    if(length<=0)
    {
        cout<<"the length is illegal"<<endl;
        return;
    }
    head = new Node;
    Node * temp = head;//先指向链表头

    for(int i=0;i<length;i++)
    {
        cout<<"enter value of "<<i+1<<endl;
        Node * p = new Node;
        cin>>p->data;
        //p->next = head;
        temp->next = p;
        temp = p;
        p->next = head;
    }


}
```

其实和单链表的建表是类似的，只不过最后指向了head。

#### 5.插入任意元素

```cpp
void CirList::insert(int index, int value)
{
    int length = getsize();
    if(length ==0)
    {
        head = new Node;
        Node * p = new Node;
        head->next = p;
        p->data = value;
        p->next = head;
    }
    else if(index<length)
    {
        Node *temp =head;
        Node *p = new Node;
        for(int i=0;i<index;i++)
        {
            temp = temp->next;

        }
        p->data = value;
        p->next = temp->next;
        temp->next = p;
    }
    else
    {
        cout<<"index's value is illegal"<<endl;
        return ;
    }
}
```


#### 6.头插法

```cpp
void CirList::insert_first(int value)
{
    Node * p = new Node;
    p->data = value;
    if(getsize()==0)
    {
        //空表
        head = new Node;
        head->next = p;

        p->next = head;
    }
    else
    {
        p->next = head->next;
        head->next = p;
    }
}
```

#### 7.尾插法

```cpp
void CirList::insert_end(int value)
{
    Node *p =new Node;
    p->data = value;
    if(getsize()==0)
    {
        //空表
        head = new Node;
        head->next = p;

        p->next = head;
    }
    else
    {
        Node * temp = head;
        while(temp->next!=head)//循环链表的最后一个节点指向的是head
        {
            temp = temp->next;
        }
        //在遍历到尾节点之后
        p->next = head;
        temp->next = p;
    }
}
```

#### 8.删除节点

```cpp
void CirList::del(int index)
{
    if(index<=0||index>getsize())
    {
        cout<<"index value error"<<endl;
        return;
    }
    Node * p = head;
    Node *temp = p;
    for(int i=0;i<index-1;i++)
    {
        temp = p; //temp永远是p的前一个节点
        p = p->next;


    }
    temp->next = p->next;
    delete p;
    p = nullptr;

}
```

#### 9.头删法

```cpp
void CirList::delete_first()
{
    if(getsize()==0)
    {
        cout<<"this is an empty list"<<endl;
        return;
    }
    Node *p = head;
    Node * temp = head->next;
    p->next = p->next->next;
    delete temp;
    temp = nullptr;
}
```

#### 10.尾删法

```cpp
void CirList::delete_end()
{
    if(getsize()==0)
    {
        cout<<"this is ane empty list"<<endl;
        return ;
    }
    Node * p = head->next;
    Node * temp = nullptr;
    while(p->next!=head)
    {
        temp = p;
        p = p->next;
    }
    delete p;
    temp->next = head;
    p = nullptr;


}
```

#### 11.实现定长链表

```cpp
//不停地输入数据，实现类似定长循环链表的功能
void CirList::circle(int index)
{
    if(index>getsize()||index<=0)
    {
        cout<<"index value is error"<<endl;
        return ;
    }
    Node * temp = head;
    for(int i=0;i<index;i++)
    {
        temp = temp->next;
    }
    int value;
    while(1)
    {
        cout<<"enter the value or -999 to exit"<<endl;
        cin>>value;
        if(value == -999)
        {
            break;
        }
        else
        {
            temp->data = value;
            if(temp->next==head)
            {
                temp = temp->next->next;
            }
            else
            {
                temp = temp->next;
            }
        }
    }

}

```

定长链表是循环链表的另一种实际运用，通过不断地输入数据，来覆盖之前的数据节点，而不需要重新new新的节点，实现了节约空间和效率的操作。

#### 12.判断链表是否有环


```cpp
//判断链表是否有环
bool CirList::ifiscircle()
{
    if(getsize()==0)
    {
        cout<<"this is an empty list"<<endl;
        return false;
    }
    Node * fast = head;
    Node * slow = head;
    while(fast->next!=head||slow->next!=head)
    {
        fast =fast->next->next;
        slow = slow->next;
        if(fast == slow)
        {
            return true;
        }
    }
    return false;
}
```

一个经典的问题，解决方法是快慢指针。


### 总结

循环链表并不是什么复杂的数据结构，其使用率甚至还不如双向链表，复杂程度也有限，因此博客里就不过多叙述了。