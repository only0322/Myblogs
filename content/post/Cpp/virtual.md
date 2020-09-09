---
title: "C++虚函数总结"
date: 2020-09-09T17:08:08+08:00
draft: true
---

### 一、虚函数的定义

虚函数是为了允许用基类的指针来调用子类的这个函数

### 二、简单实现

```cpp
class Next :public Base
{
    void show()
    {
        cout<<"Next show"<<endl;
    }
};

class That :public Base
{
    void show() override
    {
        cout<<"That show"<<endl;
    }
};

class Same :public Base
{
    virtual void show()
    {
        cout<<"That show"<<endl;
    }
};

int main()
{
    Base *be = new Next;
    
    be->show();
    return 0;
}


```
这就实现了Base调用Next类的函数了。

