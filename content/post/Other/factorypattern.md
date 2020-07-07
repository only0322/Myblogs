---
title: "工厂模式"
date: 2020-07-05T14:35:42+08:00
draft: true
---

## 一、概念 

工厂顾名思义就是创建产品，根据产品是具体产品还是具体工厂可分为简单工厂模式和工厂方法模式，根据工厂的抽象程度可分为工厂方法模式和抽象工厂模式。

该模式用于封装和管理对象的创建，是一种创建型模式，在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

## 二、细分

### 1.简单工厂模式

结构组成：

- 核心类：会定义一个用于创建指定的具体实例对象的接口。

- 抽象产品类： 是具体产品类的继承的父类或实现的接口。

- 具体产品类： 工厂类所创建的对象就是此具体产品实例。

```cpp
// 鞋子抽象类
class Shoes
{
public:
    virtual ~Shoes() {}
    virtual void Show() = 0;
};

// 耐克鞋子
class NiKeShoes : public Shoes
{
public:
    void Show()
    {
        std::cout << "我是耐克球鞋，我的广告语：Just do it" << std::endl;
    }
};

// 阿迪达斯鞋子
class AdidasShoes : public Shoes
{
public:
    void Show()
    {
        std::cout << "我是阿迪达斯球鞋，我的广告语:Impossible is nothing" << std::endl;
    }
};

// 李宁鞋子
class LiNingShoes : public Shoes
{
public:
    void Show()
    {
        std::cout << "我是李宁球鞋，我的广告语：Everything is possible" << std::endl;
    }
};
```

### 2.工厂方法模式

现各类鞋子抄的非常火热，于是为了大量生产每种类型的鞋子，则要针对不同品牌的鞋子开设独立的生产线，那么每个生产线就只能生产同类型品牌的鞋。

结构组成：

- 抽象工厂类：工厂方法模式的核心类，提供创建具体产品的接口，由具体工厂类实现。

- 具体工厂类：继承于抽象工厂，实现创建对应具体产品对象的方式。

- 抽象产品类：它是具体产品继承的父类（基类）。

- 具体产品类：具体工厂所创建的对象，就是此类。

工厂方法模式的特点：

- 工厂方法模式抽象出了工厂类，提供创建具体产品的接口，交由子类去实现。

- 工厂方法模式的应用并不只是为了封装具体产品对象的创建，而是要把具体产品对象的创建放到具体工厂类实现。

工厂方法模式的缺陷：

- 每新增一个产品，就需要增加一个对应的产品的具体工厂类。相比简单工厂模式而言，工厂方法模式需要更多的类定义。
- 一条生产线只能一个产品。

```cpp
// 总鞋厂
class ShoesFactory
{
public:
    virtual Shoes *CreateShoes() = 0;
    virtual ~ShoesFactory() {}
};

// 耐克生产者/生产链
class NiKeProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new NiKeShoes();
    }
};

// 阿迪达斯生产者/生产链
class AdidasProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new AdidasShoes();
    }
};

// 李宁生产者/生产链
class LiNingProducer : public ShoesFactory
{
public:
    Shoes *CreateShoes()
    {
        return new LiNingShoes();
    }
};
```

### 3.抽象工厂模式

具体情形：

鞋厂为了扩大了业务，不仅只生产鞋子，把运动品牌的衣服也一起生产了。

结构组成：

- 抽象工厂类： 工厂方法模式的核心类，提供创建具体产品的接口，由具体工厂类实现。

- 具体工厂类：继承于抽象工厂，实现创建对应具体产品对象的方式。

- 抽象产品类：它是具体产品继承的父类（基类）。

- 具体产品类：具体工厂所创建的对象，就是此类。

抽象工厂模式的特点：

提供一个接口，可以创建多个产品族中的产品对象。如创建耐克工厂，则可以创建耐克鞋子产品、衣服产品、裤子产品等。

抽象工厂模式的缺陷：

同工厂方法模式一样，新增产品时，都需要增加一个对应的产品的具体工厂类。

```cpp
// 基类 衣服
class Clothe
{
public:
    virtual void Show() = 0;
    virtual ~Clothe() {}
};

// 耐克衣服
class NiKeClothe : public Clothe
{
public:
    void Show()
    {
        std::cout << "我是耐克衣服，时尚我最在行！" << std::endl;
    }
};

// 基类 鞋子
class Shoes
{
public:
    virtual void Show() = 0;
    virtual ~Shoes() {}
};

// 耐克鞋子
class NiKeShoes : public Shoes
{
public:
    void Show()
    {
        std::cout << "我是耐克球鞋，让你酷起来！" << std::endl;
    }
};
```

## 总结

以上三种工厂模式，在新增产品时，都存在一定的缺陷。


简单工厂模式需要去修改工厂类，这违背了开闭法则。

工厂方式模式和抽象工厂模式，都需要增加一个对应的产品的具体工厂类，这就会增大了代码的编写量。



