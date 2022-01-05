---
title: "Java HashMap知识归纳"
date: 2022-01-04T14:33:23+08:00
draft: true
---

## 一、什么是HashMap

HashMap是一个通过键值对存储数据的容器，所谓容器就是能够自动扩容的存储工具。我之前一直很好奇为什么叫Map，不是地图的意思吗？查了一下字典终于是明白了，这里的Map应该翻译成——**映射**。

## 二、原理

### 1.构造

HashMap构造的时候会有默认的初始容量16，以及扩展因子，默认是0.75。数据存储的比例达到扩展因子之后，HashMap就会扩充，增加大小是2倍，和C++的Vector类似。长度小于8的时候是链表，后面就会转为红黑树存储。

### 2.modCount

大概就是HashMap的结构信息，每次增加或者删除数据都会维护这个值。

### 3.fail-fast机制

在我们常见的java集合中就可能出现fail-fast机制,比如ArrayList，HashMap。在多线程和单线程环境下都有可能出现快速失败。

单线程在迭代的时候remove一个元素就会有fail-fast。

多线程更常见。

可以通过并发包java.util.concurrent的类代替HashMap，这样在并发过程中不会出现fail-fast问题。


### 4.hashcode

Java的每个对象都有hashcode方法，hashMap遇到冲突是使用的拉链法。

## 三、具体使用

```java
Map<String,String> a = new HashMap<>();
        a.put("a","1");
        String data = a.get("a");
        System.out.println("data"+data);
        System.out.println(a.hashCode());
        a.remove("a");
        System.out.println(a.hashCode());
```

输出结果 1 ,80,0

80就是哈希值了。

最后哈希表为空的时候，哈希值也为0。

## 四、tips

- 在HashMap中通过get()来获取value，通过put()来插入value，ContainsKey()则用来检验对象是否已经存在。可以看出，和ArrayList的操作相比，HashMap除了通过key索引其内容之外，别的方面差异并不大。

- 不必对每个不同的对象都产生一个唯一的hashcode，只要你的HashCode方法使get()能够得到put()放进去的内容就可以了。即"不为一原则"。 生成hashcode的算法尽量使hashcode的值分散一些，不要很多hashcode都集中在一个范围内，这样有利于提高HashMap的性能。即"分散原则"。 


暂时先写这么多吧。