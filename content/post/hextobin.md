---
title: "nodejs16进制转2进制遇到的坑"
date: 2020-03-03T21:26:15+08:00
draft: true
---

# 背景

我在初学nodejs这门语言的时候，遇到了16进制无法正常转成2进制的问题。

# 解决思路

先是对一个字符串使用toString(2)。然而发现字符串再使用这种方法，已经没有意义了。

16进制转2进制的过程：16进制字符串->数字->2进制数

```
weekdatebin=weekdate.slice(0,8);
weekdatebin=parseInt(weekdatebin,"16").toString(2);
weekdatebin=PrefixInteger(weekdatebin,32);
```

但也有问题，24个十六进制数，是无法转成24*4的2进制数的，太长了，会报错。

![wrong](/images/nodejs/hextobin.jpg)

一种解决方案，就是将这24位的数拆分成3个8位进行处理，还有一种方式则是通过C语言的按位与思想，但是没有深入尝试了。

```js
//转成二进制，按位读取  24位分3次处理
console.log(weekdate);
weekdatebin=weekdate.slice(0,8);
weekdatebin=parseInt(weekdatebin,"16").toString(2);
weekdatebin=PrefixInteger(weekdatebin,32);
console.log(weekdatebin);
weekdatebin=weekdate.slice(8,16);
weekdatebin=parseInt(weekdatebin,"16").toString(2);
weekdatebin=PrefixInteger(weekdatebin,32);
console.log(weekdatebin);
weekdatebin=weekdate.slice(16,24);
weekdatebin=parseInt(weekdatebin,"16").toString(2);
weekdatebin=PrefixInteger(weekdatebin,32);
console.log(weekdatebin);
```

第二天学习nodejs，如果觉得太简单了，日后还会更新高深一点的博客。

