---
title: "位运算总结"
date: 2020-09-11T17:05:36+08:00
draft: true
---

### 求出反码中2的个数

```cpp
class Solution {
public:
     int  NumberOf1(int n) {
         unsigned int m = n;
         int count = 0;
         while(m)
         {
             count += (m&1);
             m = m>>1;
         }
         return count;
     }
};


```

unsigned 可以将int转换一下，有符号整数变为无符号数。