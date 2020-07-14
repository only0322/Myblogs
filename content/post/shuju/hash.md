---
title: "哈希算法与哈希表详解"
date: 2020-07-14T22:06:23+08:00
draft: true
---

### 一、什么是哈希函数

Hash（哈希），又称“散列”。

散列（hash）英文原意是“混杂”、“拼凑”、“重新表述”的意思。

在某种程度上，散列是与排序相反的一种操作，排序是将集合中的元素按照某种方式比如字典顺序排列在一起，而散列通过计算哈希值，打破元素之间原有的关系，使集合中的元素按照散列函数的分类进行排列。

在介绍一些集合时，我们总强调需要重写某个类的 equlas() 方法和 hashCode() 方法，确保唯一性。这里的 hashCode() 表示的是对当前对象的唯一标示。计算 hashCode 的过程就称作 哈希。

### 二、哈希函数的作用

我们通常使用数组或者链表来存储元素，一旦存储的内容数量特别多，需要占用很大的空间，而且在查找某个元素是否存在的过程中，数组和链表都需要挨个循环比较，而通过 哈希 计算，可以大大减少比较次数。

哈希 其实是随机存储的一种优化，先进行分类，然后查找时按照这个对象的分类去找。

哈希通过一次计算大幅度缩小查找范围，自然比从全部数据里查找速度要快。

### 三、几种常见的哈希函数构造方法

- 直接定址法 

取关键字或关键字的某个线性函数值为散列地址。

即 H(key) = key 或 H(key) = a*key + b，其中a和b为常数。

- 除留余数法

取关键字被某个不大于散列表长度 m 的数 p 求余，得到的作为散列地址。

即 H(key) = key % p, p < m。 

- 数字分析法 

当关键字的位数大于地址的位数，对关键字的各位分布进行分析，选出分布均匀的任意几位作为散列地址。

仅适用于所有关键字都已知的情况下，根据实际应用确定要选取的部分，尽量避免发生冲突。

- 平方取中法 

先计算出关键字值的平方，然后取平方值中间几位作为散列地址。

随机分布的关键字，得到的散列地址也是随机分布的。

- 折叠法（叠加法） 

将关键字分为位数相同的几部分，然后取这几部分的叠加和（舍去进位）作为散列地址。

用于关键字位数较多，并且关键字中每一位上数字分布大致均匀。 

- 随机数法 

选择一个随机函数，把关键字的随机函数值作为它的哈希值。

通常当关键字的长度不等时用这种方法。 



> 构造哈希函数的方法很多，实际工作中要根据不同的情况选择合适的方法，总的原则是尽可能少的产生冲突。

> 通常考虑的因素有关键字的长度和分布情况、哈希值的范围等。

> 如：当关键字是整数类型时就可以用除留余数法；如果关键字是小数类型，选择随机数法会比较好。

### 四、哈希冲突的解决

选用哈希函数计算哈希值时，可能不同的 key 会得到相同的结果，一个地址怎么存放多个数据呢？这就是冲突。

常用的主要有两种方法解决冲突：

1. 链接法（拉链法）

    拉链法解决冲突的做法是： 
    将所有关键字为同义词的结点链接在同一个单链表中。

    若选定的散列表长度为 m，则可将散列表定义为一个由 m 个头指针组成的指针数组 T[0..m-1] 。

    凡是散列地址为 i 的结点，均插入到以 T[i] 为头指针的单链表中。 
    T 中各分量的初值均应为空指针。

    在拉链法中，装填因子 α 可以大于 1，但一般均取 α ≤ 1。

2. 开放定址法

    用开放定址法解决冲突的做法是：当冲突发生时，使用某种探测技术在散列表中形成一个探测序列。

    沿此序列逐个单元地查找，直到找到给定的关键字，或者碰到一个开放的地址（即该地址单元为空）为止（若要插入，在探查到开放的地址，则可将待插入的新结点存人该地址单元）。

    查找时探测到开放的地址则表明表中无待查的关键字，即查找失败。

### 五、哈希函数代码实现

常用的字符串Hash函数还有ELFHash，APHash等等，都是十分简单有效的方法。这些函数使用


位运算使得每一个字符都对最后的函数值产生影响。另外还有以MD5和SHA1为代表的杂凑函数，


这些函数几乎不可能找到碰撞。


常用字符串哈希函数有BKDRHash，APHash，DJBHash，JSHash，RSHash，SDBMHash，

```c
unsigned int SDBMHash(char *str)
{
    unsigned int hash = 0;
 
    while (*str)
    {
        // equivalent to: hash = 65599*hash + (*str++);
        hash = (*str++) + (hash << 6) + (hash << 16) - hash;
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// RS Hash 
unsigned int RSHash(char *str)
{
    unsigned int b = 378551;
    unsigned int a = 63689;
    unsigned int hash = 0;
 
    while (*str)
    {
        hash = hash * a + (*str++);
        a *= b;
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// JS Hash 
unsigned int JSHash(char *str)
{
    unsigned int hash = 1315423911;
 
    while (*str)
    {
        hash ^= ((hash << 5) + (*str++) + (hash >> 2));
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// P. J. Weinberger Hash 
unsigned int PJWHash(char *str)
{
    unsigned int BitsInUnignedInt = (unsigned int)(sizeof(unsigned int) * 8);
    unsigned int ThreeQuarters    = (unsigned int)((BitsInUnignedInt  * 3) / 4);
    unsigned int OneEighth = (unsigned int)(BitsInUnignedInt / 8);
    unsigned int HighBits = (unsigned int)(0xFFFFFFFF) << (BitsInUnignedInt 
                                               - OneEighth);
    unsigned int hash    = 0;
    unsigned int test    = 0;
 
    while (*str)
    {
        hash = (hash << OneEighth) + (*str++);
        if ((test = hash & HighBits) != 0)
        {
            hash = ((hash ^ (test >> ThreeQuarters)) & (~HighBits));
        }
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// ELF Hash 
unsigned int ELFHash(char *str)
{
    unsigned int hash = 0;
    unsigned int x    = 0;
 
    while (*str)
    {
        hash = (hash << 4) + (*str++);
        if ((x = hash & 0xF0000000L) != 0)
        {
            hash ^= (x >> 24);
            hash &= ~x;
        }
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// BKDR Hash 
unsigned int BKDRHash(char *str)
{
    unsigned int seed = 131; // 31 131 1313 13131 131313 etc..
    unsigned int hash = 0;
 
    while (*str)
    {
        hash = hash * seed + (*str++);
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// DJB Hash 
unsigned int DJBHash(char *str)
{
    unsigned int hash = 5381;
 
    while (*str)
    {
        hash += (hash << 5) + (*str++);
    }
 
    return (hash & 0x7FFFFFFF);
}
 
// AP Hash 
unsigned int APHash(char *str)
{
    unsigned int hash = 0;
    int i;
 
    for (i=0; *str; i++)
    {
        if ((i & 1) == 0)
        {
            hash ^= ((hash << 7) ^ (*str++) ^ (hash >> 3));
        }
        else
        {
            hash ^= (~((hash << 11) ^ (*str++) ^ (hash >> 5)));
        }
    }
 
    return (hash & 0x7FFFFFFF);
}
```