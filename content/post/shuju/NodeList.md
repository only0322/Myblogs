---
title: "单链表原理与C++实现"
date: 2020-03-22T15:42:43+08:00
draft: true
---

### 单链表的特点与运用场景

单链表是线性表的一种，对比顺序存储来说，链表的链式存储，也存在一定的优缺点。

**优点**：适用于不确定元素的个数，以及需要频繁地进行增删元素，查找元素的情况。

**缺点**：实现难度较顺序表更高，存储密度更低（同样的内存空间，需要存放节点指针的地址和存储的内容）

### 单链表的数据结构

data域--存放结点值的数据域
next域--存放结点的直接后继的地址（位置）的指针域（链域）

![单链表](/images/NodeList/Node.png)

也正因为如此，才方便了我们插入和删除数据。

[源码](https://gitee.com/onlyyyy/cpp_single_linked_list)

### 单链表各模块详解

#### 1.类的结构定义

```cpp
class Node
{
public:
    int data;
    Node * next;

};

class LinkList
{
    private:
    Node * head;
};
```
通过一个单链表类来访问节点类的数据，其中节点的next指针类型一定要是这个额节点类，这是为了实现next指针的链式存储。

#### 2.链表初始化

```cpp
LinkList::LinkList()
{
    head = new Node;
    head->data=0;
    head->next=nullptr;
}
```

简单来说，就是创建一个新的节点指针，该节点的数据段设为0，下一节点(next)指针为空指针。

#### 3.链表销毁

```cpp
LinkList::~LinkList()
{
    delete head;
}
```

删除头结点，链表自然也不复存在了。

#### 4.获取链表的长度

```cpp
int LinkList::GetLen()
{
    int size=0;
    Node * p = head->next;
    while (p!=nullptr)
    {
        size++;
        p=p->next;
    }
    return size;
}
```

为什么判断的是头结点的next指针呢？其实是因为头节点在链表中的特殊性。头节点往往不存放元素，而只是为了方便管理，遍历链表用。

而头节点的next指针为空，其实就代表了该链表不存在任何元素了。

#### 5.创建链表

```cpp
int LinkList::CreateLinkList(int size)
{
    using namespace std;
    Node * pnew,* ptemp;
    ptemp = head;
    if (size<0)
    {
        cout<<"输入的节点有误"<<endl;
        exit(-1);
    }
    for (int i=0;i<size;i++)
    {
        //不断new新的指针，将当前指针的下一节点指向新的指针
        pnew = new Node;
        cout<<"请输入第"<<i+1<<"个元素的值"<<endl;
        cin>>pnew->data;
        pnew->next=nullptr; //下一节点设为空指针
        ptemp->next = pnew; //当前节点的下一节点设为新的节点
        ptemp = pnew;   //当前节点节点设为新节点
    }

}
```

创建链表相对复杂一点，先是new一个temp指针，通过循环的方式：为当前节点赋值，并且将当前节点的next指针置为空指针。最后将当前节点设为新节点。

但要记得先为指针分配内存。

#### 6.遍历链表

```cpp
void LinkList::showLinkList()
{
    using namespace std;
    if(head==nullptr&&head->next==nullptr)
    {
        cout<<"链表为空表"<<endl;
    }
    Node * p =head;
    while (p->next!=nullptr)
    {
        p = p->next;
        cout<<p->data<<endl;
    }
}

```

如果头结点为空或者next指针为空，那链表就是空表了。
反之，就通过`p=p->next`的方式让指针不断向后移动，同时访问其中的data。

#### 7.查找元素

```cpp
//查找元素
Node * LinkList::Find(int data )
{
    using namespace std;
    Node * p = head;
    if (p==nullptr)
    {
        cout<<"链表为空表"<<endl;
        return nullptr;
    }
    else
    {
        while(p->next!= nullptr)
        {
            if(p->data == data)
            {
                return p;

            }
            p=p->next;
        }
    }
    if(p->data == data)
    {
        return p;
    }
    else
    {
        return nullptr;
    }

}
```
也是借用了遍历链表的思想，返回要查找的节点值，或者是空指针。

#### 8.头插法

```cpp
//在头部插入指定元素
void LinkList::InsertElemAtHead(int data)
{
    Node * newNode = new Node;    //定义一个Node结点指针newNode
    newNode->data = data;
    Node * p = head;              //定义指针p指向头结点
    if (head == nullptr) {           //当头结点为空时，设置newNode为头结点
        head = newNode;
    }
    newNode->next = p->next;          //将新节点插入到指定位置
    p->next = newNode;
}
```
分两种情况，如果是空表，那么next指针就是要插入的节点，其余不需要做处理。
如果不是空表，那next指针还是要插入的节点，新节点的next指针指向之前的头结点。

#### 9.尾插法

```cpp
//在尾部插入指定的元素
void LinkList::InsertElemAtEnd(int data)
{
    Node * newNode = new Node;    //定义一个Node结点指针newNode
    newNode->next = nullptr;         //定义newNode的数据域和指针域
    newNode->data = data;
    Node * p = head;              //定义指针p指向头结点
    if (head == nullptr) {           //当头结点为空时，设置newNode为头结点
        head = newNode;
    }
    else                          //循环知道最后一个节点，将newNode放置在最后
    {
        while (p->next != nullptr)
        {
            p = p->next;
        }
        p->next = newNode;
    }
}
```

尾插法在处理空表的时候，逻辑是与头插法一致的。

最后，也只需要将原本为空指针的next，指向新插入的元素即可。

#### 10.任意位置插入

```cpp
//在指定位置插入指定元素
void LinkList::InsertElemAtIndex(int data,int n)
{
    using namespace std;
    if (n<1 || n>GetLen())                   //输入有误报异常
        cout << "输入的值错误" << endl;
    else
    {
        Node * ptemp = new Node;        //创建一个新的节点
        ptemp->data = data;                     //定义数据域
        Node * p = head;                    //创建一个指针指向头结点
        int i = 1;
        while (n > i)                           //遍历到指定的位置
        {
            p = p->next;
            i++;
        }
        ptemp->next = p->next;                 //将新节点插入到指定位置
        p->next = ptemp;
    }
}

```

指定位置插入，也就是”切断“之前的两个节点之间的联系，并且插入一个新的元素。

#### 11.头删法

```cpp
//在头部删除节点
void LinkList::DeleteElemAtHead()
{
    using namespace std;
    Node * p = head;
    if (p == nullptr || p->next == nullptr)
    {   //判断是否为空表，报异常
        cout << "该链表为空表" << endl;
    }
    else
    {
        Node * ptemp = nullptr;      //创建一个占位节点
        p = p->next;
        ptemp = p->next;              //将头结点的下下个节点指向占位节点
        delete p;                     //删除头结点的下一个节点
        p = nullptr;
        head->next = ptemp;           //头结点的指针更换
    }
}
```

通过暂存节点的方式进行处理。

#### 12.尾删法

```cpp
//在尾部删除元素
void LinkList::DeleteElemAtEnd()
{
    using namespace std;
    Node * p = head;          //创建一个指针指向头结点
    Node * ptemp = nullptr;      //创建一个占位节点
    if (p->next == nullptr) {        //判断链表是否为空
        cout << "单链表空" << endl;
    }
    else
    {
        while (p->next != nullptr)   //循环到尾部的前一个
        {
            ptemp = p;            //将ptemp指向尾部的前一个节点
            p = p->next;          //p指向最后一个节点
        }
        delete p;                //删除尾部节点
        p = nullptr;
        ptemp->next = nullptr;
    }
}
```

找到前一个节点，删除next指针即可。

#### 13.删除指定元素

```cpp
//删除指定的数据
void LinkList::DeleteElemAtPoint(int data)
{
    Node * ptemp = Find(data);    //查找到指定数据的节点位置
    if (ptemp == head->next) {        //判断是不是头结点的下一个节点，如果是就从头部删了它
        LinkList Link;
        Link.DeleteElemAtHead();
    }
    else
    {
        Node * p = head;          //p指向头结点
        while (p->next != ptemp)      //p循环到指定位置的前一个节点
        {
            p = p->next;
        }
        p->next = ptemp->next;         //删除指定位置的节点
        delete ptemp;
        ptemp = nullptr;
    }

}

```
与查找指定元素的方法近似。


#### 14.删除所有节点

```cpp
//删除所有数据
void LinkList::DeleteAll()
{
    Node * p = head->next;
    Node * ptemp = new Node;
    while (p != nullptr)                    //在头结点的下一个节点逐个删除节点
    {
        ptemp = p;
        p = p->next;
        head->next = p;
        ptemp->next = nullptr;
        delete ptemp;
    }
    head->next = nullptr;                 //头结点的下一个节点指向NULL
}

```

逐个遍历链表，删除节点指针。

### 总结

单链表看起来不难，但细节也真的不算少。如果出现了错漏，也会继续修改。