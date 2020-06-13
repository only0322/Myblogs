---
title: "C++ 单例模式"
date: 2020-06-11T21:19:31+08:00
draft: true
---


## 单例模式

### 1.描述

指在整个系统生命期中,一个类最多只能有一个实例(instance)存在,使得该实例的唯一性(实例是指一个对象指针)  , 比如:统计在线人数

在单例类里,又分为了懒汉式和饿汉式,它们的区别在于创建实例的时间不同:

懒汉式 : 指代码运行后,实例并不存在,只有当需要时,才去创建实例。

饿汉式 : 指代码一运行,实例已经存在,当时需要时,直接去调用即可。

### 2.代码

```cpp
#include <iostream>
using namespace std; 

class CSingleton
{
private:
       static CSingleton* m_pInstance;

       CSingleton()         //构造函数为private
       {
       } 

       CSingleton& operator = (const CSingleton& t);
       CSingleton(const CSingleton &);
public:
       static CSingleton* getInstance()
       {
              if(m_pInstance==NULL)
                     m_pInstance= new CSingleton();            
               return m_pInstance;
       }

       void print()
       {
           cout<<this<<endl;      
       }
};

CSingleton* CSingleton::m_pInstance = NULL;

int main()
{
       CSingleton *p1=CSingleton::getInstance();
       CSingleton *p2=CSingleton::getInstance(); 
       CSingleton *p3=CSingleton::getInstance();

       p1->print();
       p2->print();
       p3->print();　　　　return 0;
}
```

### 3.优劣

优点： 

    1.在单例模式中，活动的单例只有一个实例，对单例类的所有实例化得到的都是相同的一个实例。这样就 防止其它对象对自己的实例化，确保所有的对象都访问一个实例 

    2.单例模式具有一定的伸缩性，类自己来控制实例化进程，类就在改变实例化进程上有相应的伸缩性。 

    3.提供了对唯一实例的受控访问。 

    4.由于在系统内存中只存在一个对象，因此可以 节约系统资源，当 需要频繁创建和销毁的对象时单例模式无疑可以提高系统的性能。 

    5.允许可变数目的实例。 

    6.避免对共享资源的多重占用。 

缺点： 

    1.不适用于变化的对象，如果同一类型的对象总是要在不同的用例场景发生变化，单例就会引起数据的错误，不能保存彼此的状态。 

    2.由于单利模式中没有抽象层，因此单例类的扩展有很大的困难。 

    3.单例类的职责过重，在一定程度上违背了“单一职责原则”。 

    4.滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为的单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失。 

### 4.懒汉 饿汉

1.饿汉式：单例实例在类装载时就构建，急切初始化。（预先加载法）

```cpp
/**
* 饿汉式（推荐）
*/
public class Test {
    private Test() {
    }
    public static Test instance = new Test();
    public Test getInstance() {
    return instance;
    }
}
```

优点 

    1.线程安全 

    2.在类加载的同时已经创建好一个静态对象，调用时反应速度快 

缺点 

    资源效率不高，可能getInstance()永远不会执行到，但执行该类的其他静态方法或者加载了该类（class.forName)，那么这个实例仍然初始化

2.懒汉式：单例实例在第一次被使用时构建，延迟初始化。

```cpp
 class Test {
    private Test() {
    }
    public static Test instance = null;
    public static Test getInstance() {
    if (instance == null) {
    //多个线程判断instance都为null时，在执行new操作时多线程会出现重复情况
    instance = new Singleton2();
    }
    eturn instance;
    }
}
```

优点： 

    避免了饿汉式的那种在没有用到的情况下创建事例，资源利用率高，不执行getInstance()就不会被实例，可以执行该类的其他静态方法。 

缺点： 

    懒汉式在单个线程中没有问题，但多个线程同事访问的时候就可能同事创建多个实例，而且这多个实例不是同一个对象，虽然后面创建的实例会覆盖先创建的实例，但是还是会存在拿到不同对象的情况。
    
    解决这个问题的办法就是加锁synchonized，第一次加载时不够快，多线程使用不必要的同步开销大。 

