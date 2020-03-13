---
title: "动态规划总结与实战"
date: 2020-03-11T22:33:19+08:00
draft: true
---

## 什么是动态规划？

动态规划（Dynamic Programming)是多将阶段过程转化为一系列单阶段问题，利用各阶段之间的关系，逐个求解的算法。



## 适用条件

任何思想方法都有一定的局限性，超出了特定条件，它就失去了作用。同样，动态规划也并不是万能的。适用动态规划的问题必须满足最优化原理和无后效性。



## 动态规划的运用

1. 爬楼梯问题

```
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
```

我们可以找出其中的规律：

台阶数|走法
--|:--:
1|1
2|2
3|3
4|5
5|8

换句话说，N=(N-1)+(N-2)

因此只需要自底向上的方式不断推导，就能知道台阶为N时，有多少种走法了。

如果换成一次走三个台阶，也是一样的做法。

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int a=1,b=2;
        int result;
        if(n<=0)
        {
            result=0;
        }
        else if(n==1)
        {
            result=1;
        }
        else if(n==2)
        {
            result=2;
        }
        else
        {
            for(int i=3;i<=n;i++)
            {
                result=a+b;
                a=b;
                b=result;
            }
        }
        return result;
    }
};

```

2. 求最大连续和

![配图](/images/dp/max.png)

做这种类型的题目，就得靠一定的经验了。但动态规划的核心思想，也就是将问题转为局部求解。

要想整体收益最大化，只要计算局部的最大化收益，最后进行统计即可。

设数组为num[]，状态方程dp。

思路：dp[0]为最开头的数num[0]，若dp[0]加上num[1]能比dp[1]大，那就说明目前的做法是有意义的。如果还没num[1]本身大，就没有必要累计下去了。

循环处理之后，就能得到局部都是最大收益的dp，从中求出最大的dp即可。

就比如dp[1]，其中`1+dp[0](-2)=-1`，还没1本身大，证明这次操作是没有意义的，那么dp[1]就还是设为1，继续进行后续的操作。

方程：`dp[n]=max(dp[n-1]+n,n)`

代码：

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int len=nums.size();
        if(len==0) return 0;
        if(len==1) return nums[0];
        vector<int>dp(len,0);
        dp[0]=nums[0];
        int max_num=dp[0];
        for(int i=1;i<len;i++)
        {
            if(dp[i-1]>0)
                dp[i]=dp[i-1]+nums[i];
            else 
                dp[i]=nums[i];
            max_num=max(dp[i],max_num);
        }
        return max_num;


    }
};
```


3. 小偷问题

![小偷](/images/dp/thief.png)

可以看做是收益最大化问题。

无非是偷和不偷两种情况，而我们要分析的是这次偷的情况，和这次不偷下次再偷，哪一种收益更大。

也要注意一个小陷阱，**并不是求奇数和与偶数和那么简单的问题**，因为小偷完全可以选择连着两次不偷。

状态方程： dp1=max(dp1,dp2+nums[n])
其中dp1偷，dp2不偷。

附上代码

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int dp1 = 0, dp2 = 0;
        int len=nums.size();
        for (int n=0;n<len;n++)
        {
            int tmp = dp1;
            dp1 = max(dp1, dp2 + nums[n]);
            dp2 = tmp;
        }
        return dp1;
    }
};
```
4. 利益最大化问题

![股票](/images/dp/gupiao.png)

这是真正的利益最大化了，在开了上帝之眼后，要怎么获得最多的利润呢？


前i天的最大收益 = max{前i-1天的最大收益，第i天的价格-前i-1天中的最小价格}

记录【今天之前买入的最小值】

计算【今天之前最小值买入，今天卖出的获利】，也即【今天卖出的最大获利】

比较【每天的最大获利】，取最大值即可

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len=prices.size();
        if(len<=1)
        {
            return 0;
        }
        int max=0;
        int min=prices[0];
        
        for(int i=1;i<len;i++)
        {
            if(min>prices[i])
            {
                min=prices[i];
            }
            else
            {
                max=max>(prices[i]-min)?max:(prices[i]-min);
            }
        }
        return max;
    }
};
```

但这真的是最好的解法吗？说实话我自己都不是很理解这种dp方程，但我可以写出一个更容易理解的做法。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int len=prices.size();
        if(len<=1)
        {
            return 0;
        }
        int max=0;
        int min=prices[0];
        
        for(int i=1;i<len;i++)
        {
            if(min>prices[i])
            {
                min=prices[i];
            }
            else
            {
                max=max>(prices[i]-min)?max:(prices[i]-min);
            }
        }
        return max;
    }
};
```

动态管理最小值变量，然后求出最大值和最小值的差额即可，比起dp来说，要好理解很多了。

# 总结

其实通过最后一个例子，我们也能发现学习动态规划算法的一些坑。也许dp真的很强大，效率真的很高。但是一个好的算法，如果都让人想不出要怎么解，那或许还不如最朴实最简单的方法呢。

学习动态规划算法，有些人是为了比赛，有些人是满足自己的兴趣，但如果只是为了面试或者查缺补漏，丰富知识面的话，其实只需要通过这几道题目，浅尝辄止即可。